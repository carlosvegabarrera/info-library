<h1><strong>Indice</strong></h1>

- [Configuraciones](#configuraciones)
  - [Separar credenciales de código](#separar-credenciales-de-código)
    - [Usando un archivo .env (El estándar en desarrollo)](#usando-un-archivo-env-el-estándar-en-desarrollo)
    - [Usando un archivo JSON](#usando-un-archivo-json)
    - [Usando el archivo de configuración nativo de Fabric](#usando-el-archivo-de-configuración-nativo-de-fabric)
- [PIP](#pip)
  - [Comando para actualizar pip](#comando-para-actualizar-pip)
  - [Cómo actualizar otros paquetes](#cómo-actualizar-otros-paquetes)
- [Streamlit](#streamlit)
  - [🚀 Flags y Variables de Entorno](#-flags-y-variables-de-entorno)
    - [🚩 Flags de Línea de Comandos (CLI)](#-flags-de-línea-de-comandos-cli)
    - [🌐 Variables de Entorno](#-variables-de-entorno)
    - [🔐 secrets.toml vs .env](#-secretstoml-vs-env)
    - [🏆 ¿Cuál usar y cuándo?](#-cuál-usar-y-cuándo)
  - [🛠️ Comandos de Instalación y Ejecución](#️-comandos-de-instalación-y-ejecución)
  - [✍️ Visualización de Texto](#️-visualización-de-texto)
  - [🔘 Widgets Interactivos (Entrada de Datos)](#-widgets-interactivos-entrada-de-datos)
  - [📊 Visualización de Datos y Gráficos](#-visualización-de-datos-y-gráficos)
  - [🍱 Diseño y Layout](#-diseño-y-layout)
  - [🌐 Estrategias para ejecutar múltiples servicios de Streamlit en un servidor](#-estrategias-para-ejecutar-múltiples-servicios-de-streamlit-en-un-servidor)
    - [Estrategia Multipuerto (Desarrollo/Pruebas)](#estrategia-multipuerto-desarrollopruebas)
    - [Estrategia de Proxy Inverso con NGINX para Streamlit](#estrategia-de-proxy-inverso-con-nginx-para-streamlit)
    - [📑 Estrategia Multipágina Nativa en Streamlit](#-estrategia-multipágina-nativa-en-streamlit)
    - [Estrategia de Contenedores (Escalabilidad)](#estrategia-de-contenedores-escalabilidad)
  - [Configuración declarativa](#configuración-declarativa)
    - [📂 Estructura y Configuración de la Carpeta .streamlit](#-estructura-y-configuración-de-la-carpeta-streamlit)
    - [⚙️ config.toml (Personalización del Entorno)](#️-configtoml-personalización-del-entorno)
    - [🔐 secrets.toml (Gestión de Credenciales)](#-secretstoml-gestión-de-credenciales)
    - [📁 Otros elementos en .streamlit/](#-otros-elementos-en-streamlit)
    - [🏆 Jerarquía de Configuración (Orden de Prioridad)](#-jerarquía-de-configuración-orden-de-prioridad)
  - [El "Proxy Inverso" y los encabezados en implementaciones de k8s con EntraID](#el-proxy-inverso-y-los-encabezados-en-implementaciones-de-k8s-con-entraid)

## Configuraciones
### Separar credenciales de código
#### Usando un archivo .env (El estándar en desarrollo)
Es la forma más limpia. Creas un archivo llamado .env en la misma carpeta de tu proyecto (y lo agregas a .gitignore para no subirlo nunca).

**Archivo .env:**
```ini
SSH_USER=tu-usuario
SSH_PASS=tu-password
```

**Tu código Python:**
Primero instala la librería: `pip install python-dotenv`
```python
# Archivo .py
import os
from dotenv import load_dotenv
from fabric import Connection

load_dotenv()  # Carga las variables del archivo .env

user = os.getenv("SSH_USER")
password = os.getenv("SSH_PASS")

c = Connection(host, user=user, connect_kwargs={"password": password})
```

#### Usando un archivo JSON
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

#### Usando el archivo de configuración nativo de Fabric
Fabric puede leer automáticamente archivos YAML o archivos de configuración de SSH. Puedes crear un archivo llamado `fabric.yml`:

**Archivo fabric.yml:**
```yaml
user: tu_user
connect_kwargs:
  password: "tu_password"
```

Si el archivo se llama `fabric.yml` y está en la misma carpeta, Fabric lo cargará por defecto sin que tengas que programar la lectura.

> [!IMPORTANT]
> **Advertencia sobre Base64**
> Recuerda que Base64 no es cifrado, es solo una forma de codificar texto para que no sea legible a simple vista. Cualquier programador puede decodificarlo en un segundo.
> Si necesitas seguridad real, lo ideal sería usar llaves SSH (archivos `.pem` o `.pub`) en lugar de contraseñas.

## PIP
### Comando para actualizar pip
Para actualizar el propio gestor de paquetes pip a su versión más reciente, usa el siguiente comando en tu terminal o símbolo del sistema:
```sh
python -m pip install --upgrade pip
```

Explicación de las opciones:
- **python -m pip**: Ejecuta el módulo pip usando el intérprete de Python actual. Esto es más seguro que usar solo `pip` para asegurar que estás actualizando la versión correcta de Python.
- **install**: Es el comando base para manejar paquetes.
- **--upgrade (o -U)**: Indica a pip que, si el paquete ya existe, busque la versión más nueva e intente actualizarlo.
- **pip**: Es el nombre del paquete que deseas actualizar (pip se actualiza a sí mismo como cualquier otra librería).

### Cómo actualizar otros paquetes
Si lo que quieres es actualizar una librería específica (por ejemplo, `pandas`), la sintaxis es la misma:
```sh
python -m pip install --upgrade pandas
```

Si necesitas verificar qué paquetes están desactualizados antes de proceder, puedes ejecutar `pip list --outdated`.

## Streamlit
### 🚀 Flags y Variables de Entorno
Streamlit permite configurar casi cualquier aspecto del servidor mediante comandos en la terminal (flags) o variables configuradas en el sistema operativo.

#### 🚩 Flags de Línea de Comandos (CLI)
Los flags son parámetros que añades directamente al comando `streamlit run app.py`. Son útiles para cambios rápidos o temporales.

| Flag | Uso | Descripción |
| :--- | :--- | :--- |
| `--server.port` | `--server.port 8080` | Cambia el puerto donde corre la app. |
| `--server.address` | `--server.address 0.0.0.0` | Define la IP donde escucha el servidor (0.0.0.0 para acceso externo). |
| `--theme.base` | `--theme.base "dark"` | Cambia rápidamente entre tema claro u oscuro. |
| `--server.headless` | `--server.headless true` | Ejecuta sin intentar abrir el navegador automáticamente. |
| `--global.developmentMode` | `--global.developmentMode false` | Desactiva funciones de desarrollo (como el aviso de archivos modificados). |
| `--logger.level` | `--logger.level debug` | Cambia el nivel de detalle de los logs en la terminal. |

#### 🌐 Variables de Entorno
Streamlit **no lee nativamente un archivo `.env`**. Para usar un archivo `.env`, necesitarías una librería externa como `python-dotenv`.

Sin embargo, Streamlit reconoce variables de entorno del sistema siempre que sigan el patrón `STREAMLIT_` + `NOMBRE_EN_MAYUSCULAS`.

**¿Cómo configurarlas?**
- **Linux/macOS:** `export STREAMLIT_SERVER_PORT=8502`
- **Windows (PowerShell):** `$env:STREAMLIT_SERVER_PORT=8502`
- **Docker:** Se pasan en la sección `environment:` del archivo `docker-compose.yml`.

**Variables más comunes:**

| Variable | Equivalente en Config | Propósito |
| :--- | :--- | :--- |
| `STREAMLIT_SERVER_PORT` | `[server] port` | Definir el puerto de red. |
| `STREAMLIT_SERVER_HEADLESS` | `[server] headless` | Útil en servidores CI/CD o Docker. |
| `STREAMLIT_BROWSER_GATHER_USAGE_STATS` | `[browser] gatherUsageStats` | Privacidad: desactivar telemetría. |
| `STREAMLIT_THEME_PRIMARY_COLOR` | `[theme] primaryColor` | Cambiar el color de marca dinámicamente. |

#### 🔐 secrets.toml vs .env
Aunque Streamlit no usa `.env`, tiene su propia alternativa nativa: **`secrets.toml`**.

- **Si usas `secrets.toml`**: Streamlit lo gestiona solo y accedes con `st.secrets`. Es la forma "oficial".
- **Si usas `.env`**: Debes cargar las variables manualmente en tu código Python:
  ```python
  from dotenv import load_dotenv
  import os

  load_dotenv()
  mi_llave = os.getenv("MI_LLAVE_API")
  ```

#### 🏆 ¿Cuál usar y cuándo?
1. **Flags:** Para pruebas rápidas o scripts de arranque únicos.
2. **config.toml:** Para configuraciones fijas del proyecto (puertos, temas).
3. **Variables de Entorno:** Para despliegues en la nube (Heroku, AWS, Azure) o contenedores Docker.
4. **secrets.toml:** Exclusivamente para contraseñas y llaves de API.

### 🛠️ Comandos de Instalación y Ejecución
Para empezar con un proyecto, usa estos comandos en tu terminal:

Instalación:
```sh
pip install streamlit
```

Ejecutar App:
```sh
streamlit run nombre_archivo.py
```

Ver Ayuda CLI:
```sh
streamlit --help
```

### ✍️ Visualización de Texto
Ideal para estructurar tu aplicación:

- `st.title('Título principal')`: El título más grande de la página.
- `st.header('Encabezado')`: Subdivisión principal.
- `st.subheader('Subencabezado')`: Ideal para secciones menores.
- `st.markdown('**Texto** en *Markdown*')`: Permite usar negritas, cursivas y enlaces.
- `st.code('print("Hola Mundo")')`: Bloques de código con resaltado de sintaxis.
- `st.write('Texto mixto', variable, data_frame)`: La "navaja suiza" que detecta y muestra casi cualquier objeto.

### 🔘 Widgets Interactivos (Entrada de Datos)
Guarda los valores en variables para que tu app reaccione:

- Botón: `if st.button('Click'): st.write('¡Presionado!')`
- Checkbox: `st.checkbox('Acepto los términos')`
- Selección única: `st.radio('Elige uno', ['Opción A', 'Opción B'])`
- Desplegable: `st.selectbox('Elige', ['Gatos', 'Perros'])`
- Multiselección: `st.multiselect('Compra', ['Leche', 'Pan'])`
- Slider: `st.slider('Selecciona un número', 0, 100)`
- Texto: `st.text_input('Tu nombre')`
- Subir archivos: `st.file_uploader('Carga tu CSV')`

### 📊 Visualización de Datos y Gráficos
Streamlit integra librerías como Pandas y Matplotlib de forma nativa:

- Tablas: `st.dataframe(df)` (interactiva) o `st.table(df)` (estática)
- Gráficos nativos: `st.line_chart(df), st.bar_chart(df), st.area_chart(df)`
- Mapas: `st.map(df_con_lat_lon)`

### 🍱 Diseño y Layout
Organiza el contenido en la pantalla:

- Barra Lateral: `st.sidebar.button('Botón lateral')`
- Columnas: `col1, col2 = st.columns(2)` para dividir el ancho de la página
- Pestañas: `tab1, tab2 = st.tabs(["Resumen", "Detalle"])`
- Contenedores: `with st.expander("Ver más"): st.write("Contenido oculto")`

### 🌐 Estrategias para ejecutar múltiples servicios de Streamlit en un servidor
Para ejecutar varias instancias en un mismo servidor, la clave es que cada una utilice un **puerto diferente** (el puerto por defecto es `8501`).

#### Estrategia Multipuerto (Desarrollo/Pruebas)
Lanza cada aplicación asignando un puerto específico con el flag `--server.port`.

```bash
# Terminal 1
streamlit run app1.py --server.port 8501

# Terminal 2
streamlit run app2.py --server.port 8502
```
*Acceso: `localhost:8501` y `localhost:8502`.*

#### Estrategia de Proxy Inverso con NGINX para Streamlit
**Configuración**  
El Proxy Inverso permite que tus usuarios accedan vía `://tudominio.com` o `://tudominio.com` en lugar de usar puertos manuales.

**Concepto de flujo**  
`Usuario (Puerto 80/443)` ⮕ `NGINX` ⮕ `Streamlit (Puerto 8501, 8502...)`

**Configuración de NGINX (archivo .conf)**  
Streamlit utiliza **WebSockets**, por lo que la configuración de Nginx debe incluir cabeceras específicas para que la conexión no se pierda.

Crea un archivo en `/etc/nginx/sites-available/streamlit_apps`:

```nginx
server {
    listen 80;
    server_name ://tudominio.com;

    location / {
        proxy_pass http://localhost:8501; # Tu primera app de Streamlit
        
        # Cabeceras obligatorias para WebSockets
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        
        # Pasar IP real del usuario
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

server {
    listen 80;
    server_name ://tudominio.com;

    location / {
        proxy_pass http://localhost:8502; # Tu segunda app de Streamlit
        
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
    }
}
```
**Ajustes necesarios en Streamlit (`config.toml`)**  
Para que Streamlit acepte las peticiones que vienen del proxy, debes ajustar la configuración de seguridad en tu archivo `.streamlit/config.toml`:

```toml
[server]
port = 8501
enableCORS = false         # Desactivar CORS para permitir el proxy
enableXsrfProtection = false # Evita errores de "transmisión de datos"
headless = true
```
**Ventajas de esta estrategia**  
1.  **SSL/TLS (HTTPS):** Puedes usar `Certbot` (Let's Encrypt) para ponerle candadito a todas tus apps en un solo lugar.
2.  **Balanceo de carga:** Podrías tener varias copias de la misma app y que Nginx reparta el tráfico.
3.  **Seguridad:** No expones los puertos 8501/8502 al internet público (puedes cerrarlos en el firewall y dejar solo el 80/443).
4.  **Nombres amigables:** Usas subdominios en lugar de números de puerto difíciles de recordar.

**🚀 Comandos útiles**  
*   **Probar configuración:** `sudo nginx -t`
*   **Reiniciar Nginx:** `sudo systemctl restart nginx`
*   **Ver logs de error:** `sudo tail -f /var/log/nginx/error.log`


#### 📑 Estrategia Multipágina Nativa en Streamlit
Desde la versión 1.10.0, Streamlit reconoce automáticamente una estructura de archivos específica para crear navegación sin necesidad de librerías extra.

**Estructura de Carpetas 📂**  
Debes tener un archivo principal y una carpeta llamada exactamente `pages` en el mismo nivel.

```text
mi_proyecto/
├── 🏠_Inicio.py          # Archivo principal (punto de entrada)
├── .streamlit/           # Configuración (opcional)
│   └── config.toml
└── pages/                # Streamlit busca aquí las otras páginas
    ├── 01_📊_Dashboard.py
    ├── 02_⚙️_Ajustes.py
    └── 03_📝_Contacto.py
```

**Reglas de Oro para los Archivos 💡**  
Streamlit usa el nombre de los archivos dentro de `pages/` para generar el menú lateral:
*   **Orden:** Si quieres un orden específico, antepón un número (ej: `01_`, `02_`). El número se ocultará en la interfaz.
*   **Emojis:** Puedes incluir emojis en el nombre del archivo y Streamlit los renderizará en el menú.
*   **Separadores:** Los guiones bajos (`_`) se convierten en espacios automáticamente.

**Funcionamiento y Estado (`Session State`) 🔄**  
Esta es la mayor ventaja técnica:
*   **Contexto compartido:** Aunque el usuario cambie de página, el `st.session_state` se mantiene. Puedes capturar datos en la página 1 y usarlos en la página 3.
*   **Ejecución aislada:** Cada vez que haces clic en una página, Streamlit ejecuta ese archivo específico de arriba hacia abajo.

**Ejemplo de Código en el Archivo Principal**  
En el archivo raíz (ej. `🏠_Inicio.py`), puedes configurar la página para que afecte a todas las demás:

```python
import streamlit as st

st.set_page_config(
    page_title="Mi App Pro",
    page_icon="🚀",
    layout="wide"
)

st.title("Página Principal")
st.write("Bienvenido. Usa el menú de la izquierda para navegar.")
```

**¿Cuándo usar esta estrategia? ✅**  
*   Cuando las "apps" comparten el mismo set de datos o autenticación.
*   Cuando quieres evitar configurar Nginx o Docker para separar servicios.
*   Cuando buscas una experiencia de usuario fluida (SPA - Single Page Application).

---

**🚀 Tip Pro: Personalización del Menú**
Si no te gusta cómo Streamlit genera el menú por defecto, en el `config.toml` puedes ocultar el menú nativo:
```toml
[client]
showSidebarNavigation = false
```
Y luego crear tu propio sistema de navegación usando `st.navigation()` y `st.Page()` (disponible en versiones más recientes 1.31+), lo cual te da control total sobre la jerarquía.


#### Estrategia de Contenedores (Escalabilidad)
Usa **Docker Compose** para aislar entornos y gestionar puertos de forma limpia.

```yaml
# Ejemplo rápido de docker-compose.yml
services:
  app1:
    build: .
    command: streamlit run app1.py --server.port 8501
    ports:
      - "8501:8501"
  app2:
    build: .
    command: streamlit run app2.py --server.port 8502
    ports:
      - "8502:8502"
```

---
**Recomendación:** Para entornos profesionales en un solo servidor, utiliza **Docker + NGINX**.

### Configuración declarativa
#### 📂 Estructura y Configuración de la Carpeta .streamlit
La carpeta `.streamlit` actúa como el **cerebro declarativo** de tu aplicación. Centralizar la configuración aquí permite separar la lógica de negocio (Python) de los parámetros del entorno y la seguridad.

#### ⚙️ config.toml (Personalización del Entorno)
Es el archivo más potente para controlar el comportamiento y la apariencia de la app sin escribir código Python o CSS.

```toml
[theme]
primaryColor = "#F63366"
backgroundColor = "#FFFFFF"
secondaryBackgroundColor = "#F0F2F6"
textColor = "#262730"
font = "sans serif"

[server]
port = 8501
maxUploadSize = 200 # Límite de subida en MB
headless = true     # Ideal para despliegue en la nube (sin navegador local)

[browser]
gatherUsageStats = false # Desactiva el envío de estadísticas de uso

[client]
toolbarMode = "minimal"  # Oculta opciones del menú para usuarios finales
```

#### 🔐 secrets.toml (Gestión de Credenciales)
Streamlit inyecta estos valores automáticamente en un diccionario accesible desde tu código.

**Contenido del archivo:**
```toml
db_username = "admin"
db_password = "super-secret-password"
api_key = "12345-abcdef"
```

**Uso en Python:**
```python
import streamlit as st

password = st.secrets["db_password"]
api = st.secrets["api_key"]
```

> ⚠️ **Regla de oro:** Añade siempre `secrets.toml` a tu archivo `.gitignore` para evitar fugas de información en GitHub.

#### 📁 Otros elementos en .streamlit/
- `static/`: Carpeta para archivos que no cambian (favicons, logos).
- `certs/`: Para almacenar certificados SSL/TLS si necesitas HTTPS de forma local.

#### 🏆 Jerarquía de Configuración (Orden de Prioridad)
Si una configuración se repite, Streamlit elige basándose en este orden:
1. **Flags de CLI:** `streamlit run app.py --server.port 80`
2. **Variables de Entorno:** `STREAMLIT_SERVER_PORT`
3. **Archivo Local:** `./.streamlit/config.toml` (específico del proyecto)
4. **Archivo Global:** `~/.streamlit/config.toml` (configuración para todos tus proyectos en la máquina)

### El "Proxy Inverso" y los encabezados en implementaciones de k8s con EntraID
A veces, al usar EntraID detrás de una Route (que actúa como proxy), Streamlit puede confundirse sobre si la conexión es segura o no. Si notas que tras loguearte la app entra en un bucle o da error de "Insecure Origin", añade estas variables de entorno en tu `deployment.yaml`:

```yaml
env:
  - name: STREAMLIT_SERVER_ENABLE_CORS
    value: "false"
  - name: STREAMLIT_SERVER_ENABLE_XSRF_PROTECTION
    value: "false"
```
