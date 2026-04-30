## Git bash

### Comando para visualizar la configuración global actual:

- `git config --global --list`

### Comandos para setear configuraciones globales básicas principales:

- `git config --global user.name "nombre de usuario"`
- `git config --global user.email email@email`
- `git config --global core.longpaths true`
- `git config --global init.defaultbranch main`
- `git config --global core.autocrlf input`

**Notas:**  
El valor de **core.longpaths** permite que Git maneje rutas de archivos que superen los 260 caracteres. Windows tiene un límite histórico de 260 caracteres para las rutas de archivos (el famoso MAX_PATH) por lo que si la ruta de un archivo supera ese límite, Windows "se rompe" y Git no puede leer ni escribir ese archivo.

El valor de **init.defaultbranch** podría cambiar dependiendo del estandar requerido (en algunas empresas con proyectos legacy aún utilizan master como rama principal por simplicidad).

El valor de **core.autocrlf** se establece como input por los siguientes motivos:
- Al subir: Convierte a LF (por seguridad).
- Al bajar: No toca nada. Deja el archivo tal cual está en el repositorio (LF); es importante tener presente que si algo se encuentra como CRLF en el repositorio, git lo mantendrá así.

Sin la configuración de  **core.autocrlf**, podrían aparecer warnings del tipo: `warning: in the working copy of 'nombre.archivo', LF will be replaced by CRLF the next time Git touches it` y esto sucede porque Git intenta ser "amigable" con Windows:
- Al subir (Commit): Convierte CRLF → LF (mantiene el repo limpio para Linux).
- Al bajar (Checkout): Convierte LF → CRLF (para que programas viejos de Windows no fallen).

Hay otra opción que es **`false`** pero si se establece de esta forma, el riesgo que se corre es que cuando trabajas con más personas o en distintos equipos:
- Si por error editas un archivo en el Bloc de Notas de Windows o en una terminal que no sea Git Bash, podrías guardar accidentalmente cambios en CRLF.
- Al subirlo al repositorio (autocrlf false), Git lo subirá con CRLF.
- Si ese script luego lo intentas ejecutar en un servidor Linux, fallará porque el repositorio ahora tiene finales de línea de Windows.

En caso de utilizar credenciales (user, password) como método de autenticación en lugar de ssh-keys, se puede incluir:

`git config --global credential.helper store`

Esto guarda tus credenciales de forma local para que no te las pida en cada push o pull.

**Nota:**  
Este método guarda las credenciales en texto plano en tu PC. Úsalo solo en equipos de confianza.
