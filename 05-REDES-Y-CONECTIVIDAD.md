# 🌐 Módulo 5: Diagnóstico de Redes y Conectividad

Los problemas más elusivos a menudo residen en la red. Entender qué procesos están usando el ancho de banda, si una conexión es estable o cómo se resuelven los nombres de dominio es fundamental. Este módulo te equipa con herramientas modernas para iluminar cada rincón de tu conectividad.

---

### Herramienta: `bandwhich`

* **Descripción Corta:** Un "radar" de red para tu terminal. Muestra el uso de ancho de banda en tiempo real, agrupado por proceso, conexión y destino.
* **Análisis (Estabilidad y Mantenimiento):** Escrito en Rust, `bandwhich` es una herramienta moderna, ligera y estable. Es excelente para obtener una visión general instantánea de "quién está hablando con quién" en tu red. Aunque es más joven que `nethogs`, su vista combinada (proceso + conexión) es a menudo más útil para un primer diagnóstico.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Gestor de paquetes de Rust, `cargo`.
    * **Prerrequisitos:** `cargo` y las herramientas de compilación. Instálalos con: `sudo apt install -y cargo build-essential`.
    * **Comando:** `cargo install bandwhich` (Luego, asegúrate que `$HOME/.cargo/bin` esté en tu `$PATH`).

#### Ejercicios Prácticos con `bandwhich`

* **Ejercicio 1: Obtener un "Mapa" del Tráfico de Red Actual**
    * **Escenario:** Quieres ver rápidamente qué procesos están utilizando la red y a dónde se están conectando.
    * **Comando:** `sudo bandwhich`
    * **Desglose y Análisis:** `bandwhich` te mostrará una lista de procesos con cada una de sus conexiones activas, la dirección remota y el tráfico de subida/bajada. Es la forma más rápida de obtener un mapa mental de la actividad de red de tu servidor.

* **Ejercicio 2: Ver IPs Puras sin Resolución de Nombres**
    * **Escenario:** La resolución de nombres de dominio (DNS) es lenta o quieres una salida más limpia y rápida para un script.
    * **Comando:** `sudo bandwhich --no-resolve`
    * **Desglose y Análisis:**
        * `--no-resolve` o `-n`: Desactiva la búsqueda de nombres de DNS para las IPs remotas.
    * La salida será más rápida y mostrará únicamente direcciones IP, lo cual es ideal para un análisis sin el "ruido" de los nombres de dominio o para copiar IPs y analizarlas con otras herramientas.

* **Ejercicio 3: Monitorizar una Interfaz de Red Específica**
    * **Escenario:** Tu servidor tiene múltiples interfaces de red (una pública, una privada, una para Docker). Solo te interesa el tráfico de la interfaz pública `eth0`.
    * **Comando:** `sudo bandwhich --interface eth0`
    * **Desglose y Análisis:**
        * `--interface eth0` o `-i eth0`: Le dice a `bandwhich` que escuche únicamente en la interfaz especificada.
    * Esto es crucial para aislar el tráfico y no mezclar el tráfico interno de la red privada o de Docker con el tráfico de internet.

> **💡 Consejo Pro:** Usa `bandwhich` para detectar actividad sospechosa. Si ves un proceso conocido (como `php-fpm`) conectándose a una IP desconocida en un país extraño, podría ser una señal de una brecha de seguridad.

---

### Herramienta: `nethogs`

* **Descripción Corta:** Un monitor de red al estilo `top` que agrupa el ancho de banda por **proceso**. Es la mejor herramienta para responder "¿Qué programa está usando toda mi red?".
* **Análisis (Estabilidad y Mantenimiento):** `nethogs` es un clásico moderno. Es extremadamente estable, ligero y fiable. A diferencia de `bandwhich` que se centra en las conexiones, `nethogs` se enfoca en el total consumido por cada PID, lo que lo hace ideal para encontrar el proceso "culpable" general.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y nethogs`

#### Ejercicios Prácticos con `nethogs`

* **Ejercicio 1: Identificar el Proceso "Acaparador" de Ancho de Banda**
    * **Escenario:** Tu conexión a internet está saturada. Necesitas encontrar el proceso responsable.
    * **Comando:** `sudo nethogs`
    * **Desglose y Análisis:** En otra terminal, inicia una descarga grande (ej. `wget http://releases.ubuntu.com/24.04/ubuntu-24.04-desktop-amd64.iso`). Verás cómo el proceso `wget` sube a la cima de la lista en `nethogs`, mostrando exactamente cuántos KB/s está recibiendo. Las columnas `SENT` y `RECEIVED` son claras y directas.

* **Ejercicio 2: Cambiar la Frecuencia de Actualización**
    * **Escenario:** En un sistema muy cargado, quieres que `nethogs` actualice los datos con menos frecuencia para reducir su propia sobrecarga.
    * **Comando:** `sudo nethogs -d 5`
    * **Desglose y Análisis:**
        * `-d 5`: Establece el intervalo de actualización (`d` de delay) en 5 segundos (el predeterminado es 1).
    * Esto es útil para monitoreos a largo plazo donde no necesitas una granularidad de un segundo.

* **Ejercicio 3: Modo de Trazado para Scripts (No Interactivo)**
    * **Escenario:** Quieres registrar la actividad de red en un archivo para analizarla más tarde.
    * **Comando:** `sudo nethogs -t > network_log.txt`
    * **Desglose y Análisis:**
        * `-t`: Activa el "modo de trazado" (tracemode), que imprime la salida en un formato limpio y no interactivo.
    * Al redirigir la salida a un archivo, puedes crear un log de los procesos que más red han consumido a lo largo del tiempo, ideal para auditorías o análisis post-mortem.

> **💡 Consejo Pro
