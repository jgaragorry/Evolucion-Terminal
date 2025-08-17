# ⏱️ Módulo 7: Benchmarking y Optimización

Un profesional no solo resuelve problemas, sino que optimiza los sistemas para prevenirlos. Este módulo te proporciona las herramientas para dejar de "suponer" qué es más rápido y empezar a **medirlo científicamente**. Además, optimizaremos una de las tareas más comunes y lentas: la búsqueda de archivos.

---

### Herramienta: `hyperfine`

* **Descripción Corta:** Una herramienta de benchmarking para la línea de comandos que proporciona un análisis estadístico y riguroso de los tiempos de ejecución de los comandos.
* **Análisis (Estabilidad y Mantenimiento):** Escrito en Rust, `hyperfine` es la herramienta estándar para benchmarks en la TTY. Es estable, precisa y muy confiable. Su capacidad para realizar múltiples ejecuciones, calentar la caché y proporcionar análisis estadísticos la hace inmensamente superior al simple comando `time`.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt update && sudo apt install -y hyperfine`

#### Ejercicios Prácticos con `hyperfine`

* **Ejercicio 1: La Prueba Científica: `fd` vs. `find`**
    * **Escenario:** Quieres demostrar con datos irrefutables que `fd` es más rápido que `find` para buscar archivos en un proyecto grande.
    * **Comando:**
        ```bash
        # Navega a un directorio con muchos archivos, como /usr o el código fuente de un proyecto grande
        hyperfine 'find . -name "*.py"' 'fd ".py$"'
        ```
    * **Desglose y Análisis:** `hyperfine` ejecutará ambos comandos múltiples veces. La salida te mostrará el tiempo `Mean` (promedio) y `StdDev` (desviación estándar) para cada uno. Al final, un `Summary` te dirá: **`'fd ".py$"' ran X.XX ± Y.YY times faster than 'find . -name "*.py"'`**. Ahora tienes una prueba objetiva.

* **Ejercicio 2: Benchmarking Justo con Calentamiento de Caché (Warmup)**
    * **Escenario:** Estás comparando dos herramientas de búsqueda de texto que leen mucho del disco, como `grep` y `ripgrep` (`rg`). La primera ejecución siempre es más lenta porque los datos no están en la caché del disco.
    * **Comando:**
        ```bash
        hyperfine --warmup 3 'grep -r "systemd" /etc' 'rg "systemd" /etc'
        ```
    * **Desglose y Análisis:**
        * `--warmup 3`: Este flag le indica a `hyperfine` que ejecute cada comando 3 veces **antes** de empezar a medir. Esto asegura que la caché del sistema de archivos esté "caliente" (llena de los datos relevantes), resultando en una comparación más justa del rendimiento de procesamiento de cada programa, no de la velocidad del disco.

* **Ejercicio 3: Encontrar el Parámetro Óptimo para un Comando**
    * **Escenario:** Estás comprimiendo un archivo grande y quieres saber qué nivel de compresión (`-1` al `-9`) ofrece el mejor equilibrio entre velocidad y tamaño (aunque aquí solo mediremos la velocidad).
    * **Comando:**
        ```bash
        # Primero, crea un archivo de prueba grande
        dd if=/dev/zero of=testfile bs=1M count=100
        hyperfine --parameter-scan level 1 9 'gzip -{level} -k -f testfile'
        ```
    * **Desglose y Análisis:**
        * `--parameter-scan level 1 9`: Esta es una característica increíble. Le dice a `hyperfine` que ejecute el comando varias veces, reemplazando la variable `{level}` con cada número del 1 al 9.
    * El resultado será una tabla que te mostrará cuánto tarda cada nivel de compresión. Podrás ver claramente cómo el tiempo de ejecución aumenta drásticamente en los niveles más altos, permitiéndote tomar una decisión informada.

> **💡 Consejo Pro:** Usa `--export-markdown reporte.md` para guardar los resultados de tu benchmark en un archivo Markdown con una tabla perfectamente formateada. Es ideal para incluir en documentación, informes o pull requests.

---

### Herramienta: `fd` (fdfind)

* **Descripción Corta:** Una alternativa simple, rápida y amigable a `find`. Está diseñado para ser intuitivo y eficiente.
* **Análisis (Estabilidad y Mantenimiento):** Escrito en Rust, `fd` es el complemento perfecto para `ripgrep`. `fd` encuentra los archivos, y `rg` busca dentro de ellos. Es extremadamente rápido debido al paralelismo y a que inteligentemente ignora directorios ocultos y patrones de tu `.gitignore` por defecto. Es un proyecto muy estable y popular.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y fd-find`
    * **Importante:** En Ubuntu, el comando es `fdfind`. Para usar `fd`, crea un alias (`alias fd=fdfind`) o un enlace simbólico: `sudo ln -s $(which fdfind) /usr/local/bin/fd`.

#### Ejercicios Prácticos con `fd`

* **Ejercicio 1: Búsqueda Intuitiva por Patrón y Directorio**
    * **Escenario:** Quieres encontrar todos los archivos de configuración (`.conf`) dentro del directorio `/etc`.
    * **Comando:** `fd --extension conf /etc`
    * **Desglose y Análisis:**
        * `fd`: El comando.
        * `--extension conf` o `-e conf`: Filtra por la extensión del archivo.
        * `/etc`: El directorio donde buscar.
    * Compara esto con `find /etc -type f -name "*.conf"`. La sintaxis de `fd` es más limpia, legible y fácil de recordar.

* **Ejercicio 2: Encontrar Archivos y Ejecutar un Comando sobre Ellos**
    * **Escenario:** Has descargado un lote de imágenes `.JPEG` en mayúsculas y quieres convertirlas todas a `.jpg` en minúsculas.
    * **Comando:**
        ```bash
        fd -e JPEG --exec mv {} {.}.jpg
        ```
    * **Desglose y Análisis:**
        * `-e JPEG`: Encuentra todos los archivos con la extensión `JPEG`.
        * `--exec`: Ejecuta un comando por cada archivo encontrado.
        * `{}`: Es un marcador de posición para la ruta completa del archivo encontrado (ej: `./IMG_001.JPEG`).
        * `{.}`: Es un marcador de posición para la ruta sin la extensión (ej: `./IMG_001`).
        * `{.}.jpg`: Construye la nueva ruta con la extensión en minúsculas.
    * Este patrón de `buscar y ejecutar` es una de las características más potentes de `fd` para la automatización de tareas.

* **Ejercicio 3: Búsqueda Avanzada por Tamaño y Fecha para Limpieza**
    * **Escenario:** Necesitas encontrar todos los archivos de logs (`.log`) en `/var/log` que sean más grandes de 50 megabytes y que no hayan sido modificados en los últimos 30 días, para poder archivarlos.
    * **Comando:**
        ```bash
        fd . '/var/log' -e log --size +50M --changed-before 30d
        ```
    * **Desglose y Análisis:**
        * `.`: El patrón de búsqueda (un punto significa "cualquier cosa").
        * `'/var/log'`: El directorio de búsqueda.
        * `-e log`: Filtra por extensión.
        * `--size +50M`: Filtra por archivos de más de 50 Megabytes.
        * `--changed-before 30d`: Filtra por archivos modificados hace más de 30 días.
    * `fd` te permite construir consultas de limpieza y auditoría muy complejas de una manera sorprendentemente legible.

> **💡 Consejo Pro:** Por defecto, `fd` no busca en directorios ocultos. Si necesitas encontrar un archivo de configuración en un directorio como `~/.config`, usa el flag `-H` o `--hidden` para incluirlos en la búsqueda.
