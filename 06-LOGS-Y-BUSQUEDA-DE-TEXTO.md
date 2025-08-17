# 📜 Módulo 6: Maestría en Logs y Búsqueda de Texto

Los problemas más complejos dejan un rastro. Ese rastro casi siempre se encuentra en los logs. Saber cómo navegar, filtrar y correlacionar eventos en archivos de log es una habilidad de detective. Al mismo tiempo, poder buscar texto en cientos de archivos de forma instantánea es un superpoder. Estas herramientas son tu equipo forense.

---

### Herramienta: `lnav` (The Log File Navigator)

* **Descripción Corta:** Un visor de logs avanzado todo-en-uno que fusiona archivos, reconoce formatos, resalta errores y te permite realizar consultas SQL directamente sobre tus logs de texto.
* **Análisis (Estabilidad y Mantenimiento):** `lnav` es una herramienta madura, estable y absolutamente revolucionaria. No hay nada que se le compare en la TTY para el análisis de logs. Su capacidad para entender la semántica de los logs (timestamps, niveles de severidad) en lugar de verlos como simple texto es lo que la hace tan poderosa. Se mantiene activamente.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt update && sudo apt install -y lnav`

#### Ejercicios Prácticos con `lnav`

* **Ejercicio 1: La "Línea de Tiempo" Unificada del Sistema**
    * **Escenario:** Un servicio falló a las 10:30 AM. No sabes si fue por un problema de autenticación, un error del kernel o algo más. Necesitas ver el contexto completo de lo que pasaba en el sistema en ese momento.
    * **Comando:** `sudo lnav /var/log`
    * **Desglose y Análisis:** `lnav` escaneará todos los archivos en `/var/log`, detectará sus formatos (syslog, auth.log, kern.log, etc.) y los **fusionará en una única vista ordenada por fecha y hora**. Simplemente navega hasta las 10:30 y podrás ver la secuencia exacta de eventos de todo el sistema, con errores en rojo y advertencias en amarillo. Esto es algo imposible de hacer eficientemente con herramientas tradicionales.

* **Ejercicio 2: Filtrado Interactivo en Tiempo Real para Detectar Ataques**
    * **Escenario:** Estás bajo un posible ataque de fuerza bruta en SSH. Quieres ver en tiempo real todos los intentos de login fallidos de una IP específica.
    * **Comando:** (Dentro de `lnav` con `sudo lnav /var/log/auth.log`)
    * **Desglose y Análisis:**
        1.  Presiona `:` para entrar al modo comando.
        2.  Escribe: `filter-in "Failed password for.*123.45.67.89"` (reemplaza con la IP) y presiona `Enter`.
    * La vista se filtrará para mostrarte únicamente las líneas que te interesan. Lo más impresionante es que **el filtro permanece activo para los nuevos logs que llegan**. Verás los nuevos ataques aparecer en tiempo real. Para desactivar, usa `:filter-out`.

* **Ejercicio 3: Ejecutar Consultas SQL para Obtener Inteligencia de tus Logs**
    * **Escenario:** Quieres un reporte de las 5 direcciones IP que más han intentado atacar tu servidor SSH.
    * **Comando:** (Dentro de `lnav` con `sudo lnav /var/log/auth.log`)
    * **Desglose y Análisis:**
        1.  Presiona `;` (punto y coma) para abrir el prompt de SQL.
        2.  Escribe la siguiente consulta y presiona `Enter`:
            ```sql
            SELECT c_ip, count(*) AS total FROM log_messages WHERE log_line LIKE '%Failed password%' GROUP BY c_ip ORDER BY total DESC LIMIT 5
            ```
    * ¡Acabas de ejecutar una consulta SQL sobre un archivo de texto plano! `lnav` parsea los logs y los expone como una tabla virtual. El resultado será una tabla con las IPs y el número de intentos fallidos, dándote inteligencia accionable sin necesidad de exportar los datos a otro sistema. Esta es la característica más poderosa de `lnav`.

> **💡 Consejo Pro:** Presiona la tecla `i` para cambiar a la vista de **histograma**. Esto te muestra un gráfico de barras de la cantidad de mensajes por minuto/hora. Es una forma increíblemente rápida de detectar visualmente picos de actividad o tormentas de errores.

---

### Herramienta: `ripgrep` (`rg`)

* **Descripción Corta:** Un reemplazo para `grep` ultra rápido, escrito en Rust, que es inteligente por defecto (respeta `.gitignore`, ignora archivos binarios) y tiene una sintaxis más amigable.
* **Análisis (Estabilidad y Mantenimiento):** `ripgrep` es el estándar de oro para la búsqueda de texto en la línea de comandos moderna. Es significativamente más rápido que `grep` en la mayoría de los casos. Es un proyecto extremadamente estable, maduro y mantenido por un desarrollador principal del equipo de Rust. Es una herramienta fundamental.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y ripgrep`

#### Ejercicios Prácticos con `ripgrep`

* **Ejercicio 1: Búsqueda Recursiva Inteligente por Defecto**
    * **Escenario:** Necesitas encontrar la palabra "ERROR" en todos los archivos de log dentro de `/var/log`.
    * **Comando:** `rg "ERROR" /var/log`
    * **Desglose y Análisis:**
        * `rg` es recursivo por defecto. No necesitas el flag `-r`.
        * Automáticamente ignorará archivos binarios que podrían "ensuciar" la salida.
        * La salida está coloreada, con números de línea y nombres de archivo claramente visibles.
    * Compara esto con `grep -r "ERROR" /var/log`. La salida de `rg` es más útil y el comando es más simple.

* **Ejercicio 2: Búsqueda Enfocada por Tipo de Archivo**
    * **Escenario:** Eres un desarrollador y necesitas encontrar dónde se define la función `process_payment` en todos tus archivos Python, ignorando otros archivos.
    * **Comando:** `rg "def process_payment" --type py`
    * **Desglose y Análisis:**
        * `--type py` o `-t py`: Este flag le dice a `rg` que busque únicamente en archivos que reconoce como Python (`.py`, `.pyi`, etc.).
    * Puedes buscar en múltiples tipos (`-t py -t md`) o excluir tipos (`-T js`). Esto elimina la necesidad de construir complejos comandos con `find` y `grep` para filtrar por extensión.

* **Ejercicio 3: Encontrar Archivos que NO Contienen un Término (Auditoría)**
    * **Escenario:** Quieres encontrar todos los archivos de configuración de Nginx en `/etc/nginx` que **no** incluyan la directiva `ssl_protocols`. Esto podría indicar una configuración de seguridad incompleta.
    * **Comando:** `rg --files-without-match "ssl_protocols" /etc/nginx`
    * **Desglose y Análisis:**
        * `--files-without-match` o `-L`: Este es el inverso de una búsqueda normal. En lugar de mostrar las líneas que coinciden, `rg` imprimirá únicamente los nombres de los archivos que **no contienen** el patrón en absoluto.
    * Esta es una técnica de auditoría y limpieza extremadamente poderosa, difícil de replicar con `grep`.

> **💡 Consejo Pro:** `ripgrep` tiene un modo de "vista previa" interactiva cuando se combina con `fzf`. Prueba esto: `rg --line-number "tu_busqueda" | fzf`. Te permitirá buscar interactivamente a través de los resultados de `rg` y ver una vista previa del contenido del archivo en tiempo real.
