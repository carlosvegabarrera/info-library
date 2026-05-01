# Python

## Separar credenciales de código
### Usando un archivo .env (El estándar en desarrollo)
Es la forma más limpia. Creas un archivo llamado .env en la misma carpeta de tu proyecto (y lo agregas a .gitignore para no subirlo nunca).

**Archivo .env:**
```ini
SSH_USER=tu-usuario
SSH_PASS=tu-password
```

**Tu código Python:**  
Primero instala la librería: ``pip install python-dotenv``
```python
# Archivo .py
import os
from dotenv import load_dotenv  
from fabric import Connection

load_dotenv() # Carga las variables del archivo .env

user = os.getenv("SSH_USER")  
password = os.getenv("SSH_PASS")

c = Connection(host, user=user, connect_kwargs={"password": password})

```
### Usando un archivo JSON
Si prefieres un formato estructurado, un archivo JSON es ideal.


**Archivo config.json:**
```json
{
    "user": "tu_user",
    "pass": "dHVfcGFzc3dvcmQK" 
}
```
**Nota:** Aquí la contraseña está en Base64.

**Tu código Python:**
```python
import json
import base64

with open('config.json') as f:
    config = json.load(f)

# Decodificar de Base64 a texto plano
raw_pass = base64.b64decode(config['pass']).decode('utf-8')

c = Connection(host, user=config['user'], connect_kwargs={"password": raw_pass})
```
### Usando el archivo de configuración nativo de Fabric
Fabric puede leer automáticamente archivos YAML o archivos de configuración de SSH. Puedes crear un archivo llamado fabric.yml:

**Archivo fabric.yml:**
```yaml
user: tu_user
connect_kwargs:
  password: "tu_password"
```

Si el archivo se llama fabric.yml y está en la misma carpeta, Fabric lo cargará por defecto sin que tengas que programar la lectura.

**<span style="color:#F54927">Advertencia sobre Base64</span>**  
Recuerda que Base64 no es cifrado, es solo una forma de codificar texto para que no sea legible a simple vista. Cualquier programador puede decodificarlo en un segundo.  
Si necesitas seguridad real, lo ideal sería usar Llaves SSH (archivos .pem o .pub) en lugar de contraseñas.


## El "Proxy Inverso" y los encabezados
A veces, al usar EntraID detrás de una Route (que actúa como proxy), Streamlit puede confundirse sobre si la conexión es segura o no. Si notas que tras loguearte la app entra en un bucle o da error de "Insecure Origin", añade estas variables de entorno en tu deployment.yaml:
yaml
env:
  - name: STREAMLIT_SERVER_ENABLE_CORS
    value: "false"
  - name: STREAMLIT_SERVER_ENABLE_XSRF_PROTECTION
    value: "false"