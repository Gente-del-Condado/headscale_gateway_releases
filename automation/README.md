# Control público de compilación

Este directorio y `../.github/workflows/` son fuentes del control de publicación
que se exporta a `Gente-del-Condado/headscale_gateway_releases`. Se mantienen
primero en Forgejo; el control no escribe en Forgejo ni replica su historial.

La primera comprobación `Verificar lectura privada de Forgejo` se activa al
actualizar el control en main. Consulta la API con un token de solo lectura,
selecciona una release estable y descarga su commit exacto. Rechaza borradores,
prereleases, cambios de etiqueta, rutas externas y enlaces en el archivo. Valida
VERSION y el manifiesto de los instaladores generados. No publica binarios.

Secretos del repositorio **headscale_gateway_releases**, no del espejo privado:

- `FORGEJO_RELEASE_READ_TOKEN`: token Forgejo limitado al principal,
  `repository` en Leídas y los demás permisos sin acceso.
- `PUBLICATION_TOKEN`: token GitHub fine-grained con Contents Read and write
  solo en `headscale_gateway_releases`, `headscale_gateway_flatpak`,
  `headscale_gateway_ynh`, `headscale_gateway_ha`, `Mis_app_ha` y
  `Mis_componente_ha`. No necesita acceso al principal privado.
- `ANDROID_SIGNING_BUNDLE`: la identidad Android existente, nunca una nueva
  clave para esta migración. Se introduce solo en el formulario cifrado.

Las pruebas locales del lector son ligeras:

```sh
python3 -B -m unittest discover -s packaging/releases/automation -p 'test_*.py'
```

El lector no sigue redirecciones autenticadas: una respuesta del SSO o un error
HTTP detienen la ejecución sin imprimir tokens. Las compilaciones y sus fuentes
correspondientes se preparan en runners públicos estándar de GitHub; el principal
y su historial permanecen privados. La comprobación inicial de lectura por sí
sola no afirma que una release ya se haya compilado o publicado.
