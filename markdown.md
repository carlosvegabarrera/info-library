<h1><strong>Indice</strong></h1>

- [Creación de un índice](#creación-de-un-índice)
  - [El método manual (Universal)](#el-método-manual-universal)
  - [Usando "Anclas" personalizadas](#usando-anclas-personalizadas)
- [Resaltado de bloques de código (Syntax Highlighting)](#resaltado-de-bloques-de-código-syntax-highlighting)
  - [Referencia de Identificadores Comunes](#referencia-de-identificadores-comunes)
- [¿Cómo resaltar información con colores en Markdown?](#cómo-resaltar-información-con-colores-en-markdown)
  - [Alternativa visual](#alternativa-visual)
  - [Formas recomendadas](#formas-recomendadas)

## Creación de un índice
### El método manual (Universal)
Para que funcione, el nombre del enlace debe ser igual al título del encabezado, pero en minúsculas, sustituyendo los espacios por guiones (-) y eliminando signos de puntuación (**Kebab Case**).  
Por ejemplo:
```markdown
# Índice
1. [Introducción](#introducción)
2. [Instalación de Python](#instalación-de-python)
3. [Configuración de Scripts](#configuración-de-scripts)

## Introducción
Contenido aquí...

## Instalación de Python
Contenido aquí...

## Configuración de Scripts
Contenido aquí...
```
### Usando "Anclas" personalizadas
Si no quieres que el enlace dependa del texto del título (por ejemplo, cuando es muy largo), puedes insertar una etiqueta HTML vacía con un id justo arriba de tu sección:
```markdown
### [Ir a Configuración](#config-link)

<a name="config-link"></a>
## Esta es una sección con un nombre muy largo y complejo
```

## Resaltado de bloques de código (Syntax Highlighting)
Para activar el resaltado de sintaxis en bloques de código, se debe especificar el identificador del lenguaje justo después de las comillas de apertura (```lenguaje).

### Referencia de Identificadores Comunes
| Categoría | Lenguaje / Herramienta | Identificador (Tag) |
| :--- | :--- | :--- |
| **Programación** | Python | `python`, `py` |
| | JavaScript / TypeScript | `javascript`, `js`, `typescript`, `ts` |
| | Java / C# / C++ | `java`, `csharp`, `cpp` |
| | Rust / Go / Swift | `rust`, `go`, `swift` |
| | PHP / Ruby | `php`, `ruby` |
| **Scripting & Terminal** | Bash / Shell | `bash`, `sh`, `shell` |
| | PowerShell | `powershell`, `ps1` |
| | Terminal (Genérico) | `console` |
| **Web & Datos** | HTML / CSS | `html`, `css` |
| | JSON / YAML / XML | `json`, `yaml`, `xml` |
| | SQL | `sql` |
| **Configuración** | Docker / Makefile | `dockerfile`, `makefile` |
| | Markdown | `markdown`, `md` |

**Notas Adicionales**  
- **Texto Plano:** Usa `text` o deja el espacio vacío si no deseas resaltado de colores.
- **Motores:** La mayoría de los visores (GitHub, VS Code, Obsidian) se basan en *Highlight.js* o *Prism.js*, por lo que estos tags son estándar.

## ¿Cómo resaltar información con colores en Markdown?
>[!IMPORTANT]
>En Markdown no existe un modo nativo para cambiar el color del texto. El estándar de Markdown fue diseñado para ser simple y centrarse en la estructura, no en el diseño visual.  

Para cambiar el color, es posible usar HTML, pero aquí hay un detalle importante:

- En GitHub se bloquea por seguridad casi cualquier estilo CSS en línea (p.ej: <style="color:red">). Por lo tanto, no podrás ver texto de colores en el archivo `.md` renderizado dentro de la web de GitHub.
- En otros visores (VS Code, Obsidian, Blogs) si visualizas el archivo localmente en VS Code o en una herramienta que no sea tan restrictiva, puedes usar:

  ```html
  <span style="color: #e74c3c;">Este texto es rojo</span>
  <font color="blue">Este texto es azul</font>
  ```
### Alternativa visual
Como no puedes usar colores directamente, se puede usar el siguiente "truco", sin embargo, dependerá del procesador de texto si se logra renderizar el color correctamente aunque podría no presentarse de la forma esperada.

- **Bloques de código con lenguaje**: Algunos lenguajes colorean el texto.
  ```markdown
  ```diff
  - Este texto se verá rojo
  + Este texto se verá verde
  ! Este texto se verá naranja
  ```

### Formas recomendadas

- **Emojis**: Son la forma más común de dar color y jerarquía visual. Por ejemplo:
  - 🔴 Urgente / Importante
  - 🟢 Correcto / Recomendado
  - 🔵 Info / Nota

- Notas y Advertencias (Soportado nativamente por GitHub)
  ```markdown
  > [!NOTE]
  > Información útil que quieres resaltar. Aparecerá con un borde azul.

  > [!IMPORTANT]
  > Datos clave que no deben olvidarse. Suele aparecer en color púrpura.

  > [!WARNING]
  > Contenido crítico o advertencias. Aparece en color naranja/amarillo.
  ```
