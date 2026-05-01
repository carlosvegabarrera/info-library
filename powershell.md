<h1><strong>Indice</strong></h1>

- [Creación de archivos](#creación-de-archivos)
  - [Método estandar](#método-estandar)
  - [Método rápido (Redirección)](#método-rápido-redirección)
  - [Usando Set-Content o Out-File](#usando-set-content-o-out-file)
  - [Crear un archivo en una ruta específica](#crear-un-archivo-en-una-ruta-específica)
- [Ejecución de scripts](#ejecución-de-scripts)


## Creación de archivos
### Método estandar
El comando oficial para crear un archivo vacío es:
```powershell
New-Item -Path "archivo.txt" -ItemType File
```
### Método rápido (Redirección)
Es la forma más corta. Si el archivo no existe, lo crea; si existe, lo sobrescribe.

**Con contenido:**
```powershell
"Hola mundo" > nota.txt
```

**Vacío:**
```powershell
$null > vacio.txt
```

### Usando Set-Content o Out-File
Ideal cuando quieres crear el archivo e insertar texto de inmediato.
```powershell
Set-Content -Path "datos.txt" -Value "Este es el contenido"
```

### Crear un archivo en una ruta específica
Si la carpeta no existe, deberás crearla primero, pero para el archivo es así:
```powershell
New-Item -Path "C:\proyectos\test.py" -ItemType File -Force
# (El parámetro -Force permite sobrescribir si el archivo ya existe).
```

## Ejecución de scripts
PowerShell incluye una capa de protección llamada Execution Policy; su función es evitar la ejecución accidental de scripts maliciosos.  
Si intentas correr un script y el sistema lo bloquea, puedes usar estas soluciones:

**Uso temporal:** Ejecuta el script saltando la restricción solo por esa vez
```powershell
powershell -ExecutionPolicy Bypass -File .\script.ps1
```

**Configuración para desarrolladores:** Si creas tus propios scripts, lo ideal es cambiar la política a *RemoteSigned*. Esto permite ejecutar archivos locales sin trabas sin embargo, se requiere tener permisos de administrador.
```powershell
Set-ExecutionPolicy RemoteSigned
```

**Archivos descargados:** Si un script específico está bloqueado por venir de internet, desbloquéalo con:
```powershell
Unblock-File -Path .\script.ps1
```
