# 📂 Módulo 4: Navegación y Gestión de Ficheros

Las tareas más comunes en la terminal son listar, ver y moverse entre archivos y directorios. Optimizar este flujo de trabajo tiene el mayor impacto en tu productividad diaria. Las herramientas de este módulo reemplazan a los comandos clásicos con alternativas más inteligentes, visuales y eficientes.

---

### Herramienta: `eza`

* **Descripción Corta:** El reemplazo definitivo para `ls` y `tree`. Escrito en Rust, es un listador de archivos moderno con íconos, vista de árbol, integración con Git y una mejor presentación por defecto.
* **Análisis (Estabilidad y Mantenimiento):** `eza` es el fork activo y recomendado del ahora inactivo `exa`. Es extremadamente rápido, estable y su popularidad garantiza un desarrollo continuo. Es la elección estándar para una experiencia de listado de archivos moderna.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional. Para ver los íconos, se recomienda una [Nerd Font](https://www.nerdfonts.com/font-downloads).
    * **Comando:** `sudo apt update && sudo apt install -y eza`

#### Ejercicios Prácticos con `eza`

* **Ejercicio 1: Un `ls -l` con Superpoderes (Git e Íconos)**
    * **Escenario:** Estás en un repositorio de Git y quieres ver un listado detallado que te muestre el estado de cada archivo.
    * **Comando:** `eza -l --git --icons`
    * **Desglose y Análisis:**
        * `-l`: Activa la vista larga (long), mostrando permisos, tamaño, fecha, etc.
        * `--git`: Añade una columna que muestra el estado de Git (`N`=nuevo, `M`=modificado, `-`=sin cambios).
        * `--icons`: Añade íconos para cada tipo de archivo, facilitando la identificación visual.
    * Esta vista te da un contexto inmenso de un solo vistazo, fusionando la información de `ls -l`, `tree` y `git status`.

* **Ejercicio 2: El "Mapa" Perfecto de un Proyecto**
    * **Escenario:** Acabas de clonar un proyecto y necesitas entender su estructura de directorios, pero sin abrumarte con todos los detalles.
    * **Comando:** `eza --tree --level=2`
    * **Desglose y Análisis:**
        * `--tree`: Muestra el contenido en una estructura de árbol.
        * `--level=2`: Limita la profundidad del árbol a solo dos niveles.
    * Este comando es perfecto para obtener un "mapa" rápido de la arquitectura de un proyecto sin generar una salida de miles de líneas.

* **Ejercicio 3: Encontrar los Archivos más Pesados al Instante**
    * **Escenario:** Un directorio está ocupando mucho espacio y quieres saber qué archivos específicos son los más grandes.
    * **Comando:** `eza -l --sort=size`
    * **Desglose y Análisis:**
        * `-l`: Necesario para poder ver la columna de tamaño.
        * `--sort=size`: Ordena la salida por tamaño, de mayor a menor.
    * A diferencia de `ls`, que requiere una sintaxis más compleja (`ls -lS`), `eza` hace que ordenar por diferentes criterios (`size`, `modified`, `name`, etc.) sea muy intuitivo.

> **💡 Consejo Pro: ¡Integración Total!** La verdadera magia de `eza` se desata cuando reemplaza a `ls` por completo. Añade estos alias a tu `~/.bashrc` para una transformación instantánea de tu flujo de trabajo:
> ```bash
> alias ls='eza --icons --group-directories-first'
> alias ll='eza -l --git --icons --group-directories-first'
> alias la='eza -la --git --icons --group-directories-first'
> alias lt='eza --tree --level=2 --icons --group-directories-first'
> ```

---

### Herramienta: `bat` (batcat)

* **Descripción Corta:** Un `cat` con alas. Muestra el contenido de archivos con resaltado de sintaxis, numeración de líneas, integración con Git y paginación automática.
* **Análisis (Estabilidad y Mantenimiento):** Escrito en Rust, `bat` es extremadamente rápido y estable. Es un estándar en la comunidad de desarrolladores y administradores de sistemas. Su desarrollo es muy activo, con nuevos lenguajes y temas añadidos constantemente.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y bat`
    * **Importante:** En Ubuntu, el binario se instala como `batcat` para evitar conflictos. Crea un alias o un enlace simbólico para usarlo como `bat`: `alias bat='batcat'` en tu `.bashrc` o `sudo ln -s /usr/bin/batcat /usr/local/bin/bat`.

#### Ejercicios Prácticos con `bat`

* **Ejercicio 1: Leer un Fichero de Configuración con Claridad**
    * **Escenario:** Necesitas revisar la configuración de Nginx.
    * **Comando:** `bat /etc/nginx/nginx.conf`
    * **Desglose y Análisis:** En lugar de un texto plano, `bat` te mostrará el archivo con las directivas, comentarios y valores en diferentes colores. Además, si el archivo es largo, lo abrirá automáticamente en `less`, permitiéndote navegar y buscar sin perder el resaltado.

* **Ejercicio 2: Ver Solo los Cambios en un Archivo de Git**
    * **Escenario:** Has modificado un script y quieres revisar únicamente las líneas que has cambiado antes de hacer un commit.
    * **Comando:** `bat --diff mi_script.sh`
    * **Desglose y Análisis:**
        * `--diff`: Este poderoso flag le pide a `bat` que muestre únicamente las diferencias (hunks) entre la versión actual del archivo y la última versión en Git.
    * Verás las líneas añadidas y eliminadas resaltadas, como en un `git diff`, pero con el resaltado de sintaxis completo del lenguaje, haciendo la revisión de código mucho más clara.

* **Ejercicio 3: "Inspeccionar" un Archivo en Busca de Caracteres Ocultos**
    * **Escenario:** Un script te da un error extraño. Sospechas que puede haber caracteres no imprimibles, tabulaciones extrañas o finales de línea incorrectos.
    * **Comando:** `bat -A /ruta/al/script.sh`
    * **Desglose y Análisis:**
        * `-A` o `--show-all`: Muestra todos los caracteres no imprimibles (como finales de línea `LF`, tabulaciones `TAB`, etc.).
    * Esto es invaluable para la depuración, permitiéndote "ver" lo invisible y encontrar problemas que de otra manera serían imposibles de detectar.

> **💡 Consejo Pro:** `bat` soporta temas. Escribe `bat --list-themes` para ver los disponibles. Puedes establecer tu favorito permanentemente en tu `~/.bashrc` con `export BAT_THEME="Nord"` (reemplaza "Nord" con el tema que prefieras).

---

### Herramienta: `broot`

* **Descripción Corta:** Un navegador de directorios interactivo que te ayuda a encontrar archivos y obtener una visión general de la estructura de directorios sin perderte.
* **Análisis (Estabilidad y Mantenimiento):** Proyecto maduro escrito en Rust. Es rápido y estable. Resuelve un problema muy común: la dificultad de navegar por árboles de directorios profundos y complejos desde la TTY. Su desarrollo es constante.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y broot`

#### Ejercicios Prácticos con `broot`

* **Ejercicio 1: El Flujo "Buscar y Moverse" (Find & cd)**
    * **Escenario:** Sabes que hay un directorio de `config` en algún lugar de tu proyecto, pero no recuerdas la ruta exacta. Quieres encontrarlo y moverte a él.
    * **Comando:** `br`
    * **Desglose y Análisis:**
        1.  Ejecuta `br` en la raíz de tu proyecto.
        2.  Empieza a escribir `config`. `broot` filtrará el árbol en tiempo real.
        3.  Cuando veas el directorio que buscas, selecciónalo con las flechas.
        4.  Presiona `Alt+Enter`. ¡Magia! Saldrás de `broot` y tu shell estará dentro de ese directorio. (Requiere configuración inicial, ver Consejo Pro).

* **Ejercicio 2: Obtener un Resumen de Tamaños Interactivo**
    * **Escenario:** Quieres ver qué carpetas de tu proyecto son las más pesadas, como una especie de `du` interactivo.
    * **Comando:** (Dentro de `broot`)
    * **Desglose y Análisis:**
        1.  Ejecuta `br`.
        2.  Escribe `:s` y presiona `Enter`. `broot` escaneará los tamaños y los mostrará junto a cada directorio.
        3.  Ahora puedes ver las carpetas más grandes y seguir navegando, con los tamaños recalculándose a medida que te mueves.

* **Ejercicio 3: Previsualizar Archivos sin Abrirlos**
    * **Escenario:** Estás buscando un archivo, pero no estás seguro de si es el correcto. Quieres ver su contenido rápidamente.
    * **Comando:** (Dentro de `broot`)
    * **Desglose y Análisis:**
        1.  Selecciona un archivo con las flechas.
        2.  Presiona la **flecha derecha** `→`.
        3.  Se abrirá un panel de previsualización que muestra el contenido del archivo (usando `bat` si está disponible). Esto te permite inspeccionar archivos sin interrumpir tu flujo de navegación.

> **💡 Consejo Pro: ¡La Integración es CLAVE!** Para que la magia de "moverse al directorio al salir" (`Alt+Enter`) funcione, necesitas añadir una función a tu `.bashrc`. Ejecuta `broot --install`, que te dará la línea exacta a añadir. Normalmente es algo como: `source /usr/share/broot/launcher/bash/br`. ¡No te saltes este paso!

---

### Herramienta: `mc` (Midnight Commander)

* **Descripción Corta:** El legendario gestor de archivos de doble panel para la TTY. Un clásico indestructible y extremadamente poderoso para la gestión masiva de archivos.
* **Análisis (Estabilidad y Mantenimiento):** `mc` es uno de los proyectos de código abierto más antiguos y estables que existen. Es la definición de "probado en batalla". Aunque su interfaz no es "moderna" como las otras, su funcionalidad y eficiencia para operaciones de archivos complejas en la terminal son inigualables.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y mc`

#### Ejercicios Prácticos con `mc`

* **Ejercicio 1: Copia Masiva de Archivos entre Directorios**
    * **Escenario:** Necesitas copiar 50 archivos de imágenes `.jpg` de un directorio a otro.
    * **Comando:** `mc`
    * **Desglose y Análisis:**
        1.  En el panel izquierdo, navega al directorio de origen.
        2.  Presiona la tecla `+` y escribe `*.jpg` para seleccionar todos los archivos jpeg.
        3.  En el panel derecho (muévete con `Tab`), navega al directorio de destino.
        4.  Presiona `F5` (Copiar). `mc` copiará todos los archivos seleccionados. Este método es mucho más rápido y menos propenso a errores que usar `cp` con rutas largas y complejas.

* **Ejercicio 2: Gestionar un Servidor Remoto vía SFTP**
    * **Escenario:** Necesitas subir unos archivos de configuración a un servidor remoto.
    * **Comando:** `mc`
    * **Desglose y Análisis:**
        1.  Presiona `F9` para activar el menú superior.
        2.  Ve a `Izquierda` (o `Derecha`) y selecciona `Conexión SFTP`.
        3.  Escribe la dirección en formato `usuario@host.com`.
        4.  ¡El panel se convertirá en un explorador de archivos del servidor remoto! Ahora puedes copiar archivos entre tu máquina local (el otro panel) y el servidor con `F5`.

* **Ejercicio 3: Edición Rápida de un Fichero de Configuración**
    * **Escenario:** Necesitas hacer un cambio rápido en un fichero de configuración.
    * **Comando:** `mc`
    * **Desglose y Análisis:**
        1.  Navega hasta el archivo que quieres editar.
        2.  Presiona `F4` (Editar).
        3.  El archivo se abrirá en el editor de texto integrado (`mcedit`). Haz tus cambios.
        4.  Presiona `F2` para guardar y `F10` para salir del editor.
    * Este flujo es extremadamente rápido para pequeñas ediciones, ya que no tienes que salir de tu gestor de archivos.

> **💡 Consejo Pro:** Las teclas de función (`F1` a `F10`) son el corazón de `mc`. Siempre están visibles en la parte inferior de la pantalla. `F5`=Copiar, `F6`=Mover, `F8`=Borrar. Dominarlas te convierte en un ninja de la gestión de archivos.
