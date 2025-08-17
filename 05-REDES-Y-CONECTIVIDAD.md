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

> **💡 Consejo Pro:** Dentro de la interfaz interactiva de `nethogs`, presiona `m` para cambiar las unidades de medida (KB/s -> KB -> B -> MB). Esto te ayuda a adaptar la vista a la escala del tráfico que estás observando.

---

### Herramienta: `gping`

* **Descripción Corta:** Un `ping` con gráficos en la terminal. Visualiza la latencia y la pérdida de paquetes a lo largo del tiempo.
* **Análisis (Estabilidad y Mantenimiento):** Escrito en Rust, `gping` es una herramienta simple, estable y que resuelve un problema muy común: la dificultad de interpretar la salida de `ping` para diagnosticar conexiones inestables. Es una herramienta "de un solo uso" que hace su trabajo a la perfección.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y gping`

#### Ejercicios Prácticos con `gping`

* **Ejercicio 1: Diagnosticar una Conexión a Internet Inestable**
    * **Escenario:** Tu conexión a internet parece "micro-cortarse". Quieres una prueba visual de su estabilidad.
    * **Comando:** `gping google.com`
    * **Desglose y Análisis:** `gping` mostrará un gráfico en tiempo real de la latencia (el tiempo de ida y vuelta) a `google.com`. Si ves **picos altos**, significa que la latencia está aumentando (lag). Si ves **huecos en el gráfico**, significa que se están perdiendo paquetes. Es una forma infalible de demostrar visualmente un problema de conexión.

* **Ejercicio 2: Comparar la Estabilidad de Varios Hosts a la Vez**
    * **Escenario:** No estás seguro si el problema es tu conexión, tu DNS o el servidor de destino. Quieres hacer ping a varios sitios a la vez para comparar.
    * **Comando:** `gping google.com 8.8.8.8 tusitio.com`
    * **Desglose y Análisis:** Esta es la característica estrella de `gping`. Abrirá un gráfico para cada host, permitiéndote comparar visualmente su rendimiento. Si `8.8.8.8` es estable pero `tusitio.com` tiene picos, el problema probablemente está en el servidor de tu sitio, no en tu conexión.

* **Ejercicio 3: Observar la Latencia en tu Red Local (LAN)**
    * **Escenario:** Quieres verificar la calidad de la conexión a tu router o a otro servidor en tu red local.
    * **Comando:** `gping 192.168.1.1` (reemplaza con la IP de tu router o servidor)
    * **Desglose y Análisis:** En una red local, la latencia debería ser extremadamente baja y estable (normalmente < 2ms). Si `gping` muestra picos o inestabilidad, podría indicar un problema con tu cable de red, tu switch o la conexión WiFi.

> **💡 Consejo Pro:** Usa el flag `--buffer` o `-b` para aumentar el historial de tiempo que se muestra en el gráfico. Por ejemplo, `gping -b 120 google.com` te mostrará los últimos 2 minutos de historial, ideal para observar tendencias a más largo plazo.

---

### Herramienta: `dog`

* **Descripción Corta:** Un cliente de DNS moderno y amigable. Es una versión mejorada de `dig` y `nslookup` con una salida coloreada y una sintaxis mucho más intuitiva.
* **Análisis (Estabilidad y Mantenimiento):** `dog` es un proyecto estable escrito en Rust. Cumple su objetivo de hacer las consultas DNS más humanas. Su principal ventaja es el soporte nativo para protocolos de DNS seguros como DoT (DNS-over-TLS) y DoH (DNS-over-HTTPS), algo crucial en la administración de sistemas moderna.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno.
    * **Comando:** `sudo apt install -y dog`

#### Ejercicios Prácticos con `dog`

* **Ejercicio 1: Reemplazar `dig` para Consultas Simples y Legibles**
    * **Escenario:** Quieres saber la dirección IP (registro A) de `github.com`.
    * **Comando:** `dog github.com`
    * **Desglose y Análisis:** Compara la salida limpia, coloreada y directa de `dog` con la verbosidad de `dig github.com`. `dog` te da la respuesta que necesitas sin toda la información de protocolo que rara vez es necesaria para una consulta rápida.

* **Ejercicio 2: Consultar Tipos de Registros Específicos**
    * **Escenario:** Necesitas verificar los servidores de correo (registros MX) de un dominio y sus registros de texto (TXT) para verificaciones de SPF o DKIM.
    * **Comandos:**
        ```bash
        dog gmail.com MX
        dog google.com TXT
        ```
    * **Desglose y Análisis:** La sintaxis es simple y predecible: `dog <dominio> <TIPO_DE_REGISTRO>`. Es mucho más fácil de recordar que la sintaxis de `dig`.

* **Ejercicio 3: Realizar una Consulta DNS Segura y Encriptada (DoT)**
    * **Escenario:** Sospechas que tu DNS local está siendo interceptado o te da resultados incorrectos. Quieres hacer una consulta directamente a un servidor público como Cloudflare de forma segura.
    * **Comando:** `dog github.com @tls://1.1.1.1`
    * **Desglose y Análisis:**
        * `@tls://1.1.1.1`: Esta sintaxis le dice a `dog` que se conecte al servidor DNS `1.1.1.1` usando el protocolo **DNS-over-TLS**, que encripta tu consulta.
    * Realizar una consulta DNS encriptada es así de simple. Esta es una capacidad fundamental para la privacidad y seguridad que `dog` hace trivial.

> **💡 Consejo Pro:** ¿Quieres ver todos los registros DNS comunes de un dominio a la vez? Usa el tipo de registro `ALL`: `dog google.com ALL`. Te dará los registros A, AAAA, MX, NS y SOA en una sola consulta.
