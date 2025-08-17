# 💾 Módulo 3: Análisis Forense de Disco y I/O

Cuando el rendimiento cae o el espacio se agota, el culpable suele ser el subsistema de disco. Las herramientas de este módulo son tu equipo forense para investigar el uso de espacio y la actividad de Entrada/Salida (I/O), reemplazando a los crípticos `df`, `du` y `iostat`.

---

### Herramienta: `gdu` (Go Disk Usage)

* **Descripción Corta:** Un analizador de uso de disco interactivo y ultra rápido escrito en Go, diseñado para encontrar "devoradores de espacio" y permitirte actuar sobre ellos.
* **Análisis (Estabilidad y Mantenimiento):** `gdu` es un proyecto maduro y muy estable. Su principal ventaja es la velocidad, lograda mediante el procesamiento en paralelo. Es la herramienta de elección para análisis interactivos en sistemas de ficheros muy grandes. Su desarrollo es activo.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt update && sudo apt install -y gdu`

#### Ejercicios Prácticos con `gdu`

* **Ejercicio 1: Análisis Interactivo de un Directorio "Pesado"**
    * **Escenario:** Recibes una alerta de que el espacio en `/var` está bajo. Necesitas explorar qué subdirectorios son los culpables.
    * **Comando:** `sudo gdu /var`
    * **Desglose y Análisis:** `gdu` escaneará `/var` a una velocidad asombrosa y te presentará una lista de sus contenidos, ordenada por tamaño. Puedes usar las flechas **Arriba/Abajo** para navegar, **Enter** para entrar en un subdirectorio y la flecha **Izquierda** para volver. Es la forma más rápida de "taladrar" hasta la fuente del consumo de espacio.

* **Ejercicio 2: Liberación de Espacio Segura y Directa**
    * **Escenario:** Has encontrado una carpeta de logs antiguos en `/var/log/journal` que ocupa varios gigabytes y quieres eliminarla.
    * **Comando:** (Dentro de la interfaz de `gdu`)
    * **Desglose y Análisis:**
        1.  Navega hasta el directorio o archivo que deseas eliminar.
        2.  Presiona la tecla `d` (delete).
        3.  `gdu` te pedirá confirmación, mostrando el nombre de lo que se va a borrar. Presiona `y` para confirmar.
    * Este flujo (analizar, navegar, eliminar) dentro de una sola herramienta es mucho más seguro y eficiente que tener `du` en una terminal y `rm -rf` en otra, reduciendo el riesgo de errores catastróficos.

* **Ejercicio 3: Generar un Reporte No Interactivo para Automatización**
    * **Escenario:** Quieres guardar un "snapshot" del uso de disco de un directorio para compararlo en el futuro o para procesarlo con un script.
    * **Comando:** `gdu -o /tmp/reporte_home.json /home/usuario`
    * **Desglose y Análisis:**
        * `-o /tmp/reporte_home.json`: Le indica a `gdu` que no inicie la interfaz interactiva (`-o` de output) y que en su lugar, exporte los resultados del escaneo a un archivo en formato JSON.
    * Esto es extremadamente útil para tareas de monitoreo programadas con `cron`, permitiéndote guardar historiales de uso de disco.

> **💡 Consejo Pro:** `gdu` ignora por defecto los dow montajes de otros sistemas de archivos para evitar escanear, por ejemplo, un dow montado de red. Si necesitas escanear todo sin excepción, usa el flag `gdu -x`.

---

### Herramienta: `dust` (du + rust)

* **Descripción Corta:** Un reemplazo visual y no interactivo para `du`. Su objetivo es darte una visión general, clara y rápida del uso de disco con un árbol de directorios y barras de porcentaje.
* **Análisis (Estabilidad y Mantenimiento):** Escrito en Rust, `dust` es muy rápido y estable. Se diferencia de `gdu` en su filosofía: no es para la interacción, sino para el reporte rápido. Es el sucesor perfecto de `du` para usar en scripts o para una revisión visual instantánea.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt install -y du-dust` (El paquete es `du-dust`, el comando es `dust`).

#### Ejercicios Prácticos con `dust`

* **Ejercicio 1: Obtener un "Mapa de Calor" de tu Directorio Home**
    * **Escenario:** Acabas de entrar a tu directorio home y quieres saber de un vistazo dónde se concentra el espacio.
    * **Comando:** `dust`
    * **Desglose y Análisis:** `dust` presenta un árbol donde cada "rama" es un directorio. A la izquierda, barras de colores y porcentajes te muestran visualmente qué directorios son los más pesados en relación a sus "hermanos". Es una forma increíblemente intuitiva de leer la distribución del espacio.

* **Ejercicio 2: Replicar `du -sh *` de Forma Mejorada**
    * **Escenario:** El árbol completo es demasiado. Solo quieres ver el resumen de los directorios del nivel actual.
    * **Comando:** `dust -d 1`
    * **Desglose y Análisis:**
        * `-d 1` o `--depth 1`: Limita la profundidad del árbol a 1, mostrando solo los directorios del nivel en el que te encuentras.
    * La salida es similar a `du -sh *`, pero con la gran ventaja de estar ordenada por tamaño y enriquecida con las barras visuales.

* **Ejercicio 3: Encontrar Directorios con "Enjambres" de Archivos Pequeños**
    * **Escenario:** El espacio en disco parece estar bien, pero las operaciones son lentas. Sospechas que algún directorio tiene demasiados archivos pequeños (un problema de inodos).
    * **Comando:** `dust -c`
    * **Desglose y Análisis:**
        * `-c` o `--filecount`: Cambia la visualización para que el tamaño y las barras representen el **número de archivos** en lugar de los bytes.
    * Este comando es una joya oculta. Te ayudará a identificar directorios como `.cache` o `node_modules` que pueden contener cientos de miles de archivos, lo cual puede degradar el rendimiento del sistema de ficheros.

> **💡 Consejo Pro:** Combina `dust` con `watch` para un monitoreo en tiempo real. Ejecuta `watch dust -d 1` en un directorio donde se escriben archivos (como `Downloads` o `/var/log`) para ver los cambios de tamaño cada 2 segundos.

---

### Herramienta: `duf` (Disk Usage/Free)

* **Descripción Corta:** Un reemplazo moderno y amigable para `df`. Presenta la información de los sistemas de ficheros en una tabla limpia, coloreada y fácil de interpretar.
* **Análisis (Estabilidad y Mantenimiento):** Escrito en Go, `duf` es rápido, estable y no tiene dependencias. Es un proyecto maduro que cumple su función a la perfección. Una vez que lo usas, no querrás volver a la salida críptica de `df`.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt install -y duf`

#### Ejercicios Prácticos con `duf`

* **Ejercicio 1: Reporte de Discos para Humanos**
    * **Escenario:** Necesitas revisar el estado de todos los discos montados en el sistema.
    * **Comando:** `duf`
    * **Desglose y Análisis:** `duf` presenta una tabla clara, agrupando "Local Devices" (tus discos duros reales) y "Special Devices" (`tmpfs`, `squashfs` para snaps, etc.). Las columnas están perfectamente alineadas y una barra de progreso visual te indica el porcentaje de uso. Es infinitamente más legible que `df -h`.

* **Ejercicio 2: Filtrar y Ordenar para Enfocarse en lo Importante**
    * **Escenario:** Solo te interesan los sistemas de ficheros `ext4` y quieres verlos ordenados por el que tiene menos espacio disponible.
    * **Comando:** `duf --only-fs ext4 --sort avail`
    * **Desglose y Análisis:**
        * `--only-fs ext4`: Filtra la salida para mostrar únicamente los sistemas de ficheros de tipo `ext4`.
        * `--sort avail`: Ordena los resultados por la columna de espacio disponible (`avail`), de menor a mayor por defecto.
    * Esta capacidad de filtrado y ordenamiento nativo es algo que con `df` requeriría complejas combinaciones con `grep` y `sort`.

* **Ejercicio 3: Mostrar Inodos en Lugar de Espacio**
    * **Escenario:** Has recibido una alerta de "no queda espacio en el dispositivo", pero `duf` muestra que hay gigabytes libres. El problema podría ser el agotamiento de inodos.
    * **Comando:** `duf --inodes`
    * **Desglose y Análisis:**
        * `--inodes`: Cambia la vista para mostrar el uso y la disponibilidad de inodos en lugar de bloques de datos.
    * Si ves que la barra de uso de inodos está al 100% en algún dispositivo, has encontrado tu problema. Es hora de usar `dust -c` para encontrar qué directorio tiene la plaga de archivos pequeños.

> **💡 Consejo Pro:** Para terminales con fondo claro, la visibilidad puede mejorar. Usa `duf --theme light` para cambiar a un esquema de colores optimizado para fondos claros.

---

### Herramienta: `iotop`

* **Descripción Corta:** El `top` para las operaciones de I/O del disco. Te muestra en tiempo real qué proceso está leyendo o escribiendo en el disco y a qué velocidad.
* **Análisis (Estabilidad y Mantenimiento):** Es un "clásico moderno". Aunque es más antiguo que las otras herramientas de este módulo, sigue siendo indispensable y no tiene un reemplazo directo que mejore su función principal. Es estable, ligero y se encuentra en los repositorios de todas las distribuciones.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt install -y iotop`

#### Ejercicios Prácticos con `iotop`

* **Ejercicio 1: Encontrar al Proceso que "Secuestra" el Disco**
    * **Escenario:** La luz de actividad del disco no para de parpadear y el sistema está muy lento, aunque la CPU está libre.
    * **Comando:** `sudo iotop`
    * **Desglose y Análisis:** `iotop` requiere `sudo` para funcionar. Te mostrará una lista de procesos ordenada por su uso de disco. Fíjate en las columnas `DISK READ` y `DISK WRITE`. El proceso en la cima es el que está causando el cuello de botella. Comúnmente pueden ser procesos de bases de datos, copias de seguridad, o `rsync`.

* **Ejercicio 2: Reducir el Ruido y Ver Solo la Actividad Real**
    * **Escenario:** La lista de `iotop` es muy larga y la mayoría de procesos muestran `0.00 B/s`. Quieres una vista más limpia.
    * **Comando:** `sudo iotop --only`
    * **Desglose y Análisis:**
        * `--only` o `-o`: Este flag instruye a `iotop` para que muestre únicamente los procesos o hilos que están realizando operaciones de I/O en ese preciso momento.
    * La pantalla se limpiará drásticamente, y solo verás aparecer procesos cuando realmente estén leyendo o escribiendo, haciendo el diagnóstico mucho más fácil.

* **Ejercicio 3: Ver el Uso Acumulado de I/O**
    * **Escenario:** Quieres saber qué proceso ha escrito más datos en total desde que empezaste a monitorizar, no solo la velocidad actual.
    * **Comando:** `sudo iotop --accumulated`
    * **Desglose y Análisis:**
        * `--accumulated` o `-a`: Cambia las columnas de velocidad (B/s) a contadores totales (B).
    * Esto es útil para identificar procesos que realizan muchas escrituras pequeñas a lo largo del tiempo, cuyo impacto podría no ser obvio en la vista de velocidad instantánea.

> **💡 Consejo Pro:** Presta atención a la columna `IO>`. Esta muestra el porcentaje de tiempo que el proceso ha pasado esperando por operaciones de I/O. Un valor alto aquí, incluso con baja actividad de `READ/WRITE`, puede indicar un disco lento o saturado.
