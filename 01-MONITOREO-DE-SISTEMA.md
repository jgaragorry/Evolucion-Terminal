# 📊 Módulo 1: Monitoreo Integral del Sistema

Aquí se presentan las herramientas tipo "dashboard" que ofrecen una visión global del estado del sistema. Son el punto de partida para cualquier investigación de rendimiento.

---

### Herramienta: `btop`

* **Descripción Corta:** El monitor de sistema TTY más avanzado y visualmente atractivo disponible. Sucesor de `bpytop`, reescrito en C++ para un rendimiento extremo.
* **Análisis (Estabilidad y Mantenimiento):** Proyecto extremadamente popular y con un mantenimiento muy activo por parte de su desarrollador original. Escrito en C++, es increíblemente rápido y eficiente. Es una apuesta segura y estable a largo plazo.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt update && sudo apt install -y btop`

#### Ejercicios Prácticos con `btop`

* **Ejercicio 1: Navegación por Mouse y Vistas Expandidas**
    * **Escenario:** Necesitas una visión general, pero quieres investigar rápidamente el historial de uso de la CPU.
    * **Comando:** `btop`
    * **Desglose y Análisis:** Al iniciar `btop`, usa el **mouse** para hacer clic directamente sobre la caja de "CPU". La vista se expandirá a pantalla completa, mostrándote el historial de cada núcleo. Esto demuestra la naturaleza híbrida de `btop` que fusiona la velocidad de la TTY con la facilidad de uso de una GUI. Haz clic de nuevo o presiona `Esc` para volver.

* **Ejercicio 2: Filtrado de Procesos y Vista Detallada**
    * **Escenario:** Quieres ver todos los procesos relacionados con el servicio `sshd` y examinar uno en detalle.
    * **Comando:** `btop`
    * **Desglose y Análisis:** Presiona `f`, escribe `sshd` y presiona `Enter`. La lista de procesos se filtrará. Selecciona un proceso con las flechas y presiona `Enter`. Se abrirá una vista detallada de ese PID, mostrando información granular como hilos, uso de memoria, y operaciones de I/O, un nivel de detalle que `top` no puede igualar.

* **Ejercicio 3: Personalización del Tema y Layout**
    * **Escenario:** Quieres adaptar la apariencia de `btop` a tu gusto personal.
    * **Comando:** `btop`
    * **Desglose y Análisis:** Presiona `ESC` para abrir el Menú, ve a `Options`. Aquí puedes cambiar el `Color theme` (ej. `tty`) y los `presets` de las cajas (ej. `CPU box preset`) para cambiar cómo se visualizan los gráficos. Esto demuestra la flexibilidad de `btop` para adaptarse al flujo de trabajo de cada usuario.

> **💡 Consejo Pro:** `btop` recuerda el último proceso que seleccionaste. Si estás monitorizando un proceso específico, puedes presionar `(Shift+)` o `(Shift-)` para moverlo arriba o abajo en la lista y mantenerlo siempre a la vista.

---

### Herramienta: `btm` (Bottom)

* **Descripción Corta:** Un monitor de sistema altamente visual y personalizable escrito en Rust, enfocado en un layout de widgets.
* **Análisis (Estabilidad y Mantenimiento):** Proyecto maduro y muy popular en la comunidad de Rust. Es extremadamente estable y rápido. Su desarrollo es activo y constante. Es una elección robusta y confiable.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt install -y bottom`

#### Ejercicios Prácticos con `btm`

* **Ejercicio 1: Enfocarse en un Widget Específico**
    * **Escenario:** La lista de procesos es muy pequeña. Quieres expandirla para verla en detalle.
    * **Comando:** `btm`
    * **Desglose y Análisis:** Presiona `dd` (dos veces la tecla 'd'). El widget de procesos se expandirá a pantalla completa. Esto es parte de la filosofía de `btm` de permitirte hacer "zoom" en la información que más te importa en cada momento. Presiona `dd` de nuevo o `Esc` para volver.

* **Ejercicio 2: Vista de Árbol y Búsqueda por Regex**
    * **Escenario:** Necesitas ver la jerarquía de procesos del sistema y luego encontrar todos los que terminen en "d".
    * **Comando:** `btm`
    * **Desglose y Análisis:** Presiona `t` para activar la vista de árbol. Vuelve a presionarla para desactivarla. Ahora, presiona `/`, escribe `d$` y presiona `Enter`. La lista se filtrará usando una expresión regular para mostrar solo los procesos que terminan en 'd' (daemons). `btm` tiene un potente motor de filtrado.

* **Ejercicio 3: Cambiar el Layout sobre la Marcha**
    * **Escenario:** Quieres un layout más simple que solo muestre CPU y Procesos.
    * **Comando:** `btm --basic`
    * **Desglose y Análisis:** Al ejecutar `btm` con el flag `--basic` o `-l`, se carga un layout simplificado. `btm` permite definir tus propios layouts en su archivo de configuración, lo que lo hace extremadamente personalizable para diferentes tareas de monitoreo.

> **💡 Consejo Pro:** Puedes moverte entre los widgets usando `H` (izquierda) y `L` (derecha), inspirado en las teclas de Vim. Esto agiliza la navegación sin necesidad de usar el Tab.

---
### Herramienta: `glances`

* **Descripción Corta:** Un monitor de sistema todo-en-uno escrito en Python. Su punto fuerte es la cantidad de plugins e información que puede mostrar, incluyendo sensores, Docker y alertas.
* **Análisis (Estabilidad y Mantenimiento):** Es uno de los monitores más veteranos y estables. Al estar escrito en Python, es ligeramente más pesado que las alternativas en Rust/C++, pero su extensibilidad es inigualable. El proyecto está muy bien mantenido.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt install -y glances`

#### Ejercicios Prácticos con `glances`

* **Ejercicio 1: Identificar Problemas con Alertas por Colores**
    * **Escenario:** Necesitas saber de un vistazo si algún subsistema del servidor está bajo estrés.
    * **Comando:** `glances`
    * **Desglose y Análisis:** Observa la barra lateral. `glances` colorea las métricas según su estado: Verde (OK), Azul (CUIDADO), Violeta (AVISO), Rojo (CRÍTICO). Esto te da un sistema de alerta temprana visual sin necesidad de interpretar números.

* **Ejercicio 2: Ordenar Procesos por Actividad de Disco (I/O)**
    * **Escenario:** El sistema está lento, la CPU está bien, sospechas del disco.
    * **Comando:** `glances`
    * **Desglose y Análisis:** Dentro de `glances`, presiona `i` para ordenar la lista de procesos por su actividad de I/O. El proceso que esté leyendo o escribiendo más en disco subirá a la cima. Esto es crucial para encontrar "culpables de disco" que `top` no mostraría.

* **Ejercicio 3: Modo Servidor/Cliente para Monitoreo Remoto**
    * **Escenario:** Quieres monitorizar un servidor remoto sin SSH, a través de la red.
    * **Comando en el Servidor:** `glances -s` (Modo Servidor)
    * **Comando en el Cliente:** `glances -c @ip_del_servidor` (Modo Cliente)
    * **Desglose y Análisis:** Esta es la característica estrella de `glances`. Permite centralizar el monitoreo de múltiples máquinas en una sola terminal sin necesidad de múltiples sesiones SSH.

> **💡 Consejo Pro:** `glances` puede mostrar información de contenedores Docker. Presiona `2` para activar/desactivar el plugin de Docker si está instalado.

---

### Herramienta: `zenith`

* **Descripción Corta:** Un monitor de sistema en Rust que brilla por sus gráficos de rendimiento históricos.
* **Análisis (Estabilidad y Mantenimiento):** Proyecto muy bien construido y estable. Aunque su desarrollo no es tan rápido como el de `btop`, es funcional y robusto. Es una excelente herramienta especializada en la visualización de datos a lo largo del tiempo.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Gestor de paquetes de Rust, `cargo`.
    * **Prerrequisitos:** `cargo`. Se instala con: `sudo apt install -y cargo`.
    * **Comando:** `cargo install zenith` (Luego, asegúrate que `$HOME/.cargo/bin` esté en tu `$PATH`).

#### Ejercicios Prácticos con `zenith`

* **Ejercicio 1: Diagnosticar Problemas Intermitentes**
    * **Escenario:** Los usuarios reportan picos de lentitud que duran solo unos segundos.
    * **Comando:** `zenith`
    * **Desglose y Análisis:** Deja `zenith` corriendo. A diferencia de otros monitores que solo muestran el estado actual, los gráficos de `zenith` te muestran los últimos 60 segundos. Podrás ver visualmente un pico de CPU, disco o red que ocurrió en el pasado reciente, permitiéndote correlacionar el evento con la queja.

* **Ejercicio 2: Monitorizar el Uso de la GPU**
    * **Escenario:** Estás en un servidor con una GPU NVIDIA y necesitas ver su rendimiento.
    * **Comando:** `zenith`
    * **Desglose y Análisis:** Si `zenith` detecta una GPU NVIDIA con los drivers correctos, mostrará automáticamente un panel dedicado para ella, con uso del procesador gráfico, memoria y temperatura. Integra la funcionalidad de `nvidia-smi` en un dashboard general.

* **Ejercicio 3: Cambiar el Intervalo de Actualización**
    * **Escenario:** Quieres una actualización más rápida o más lenta de los datos.
    * **Comando:** `zenith --db-update-interval 5000`
    * **Desglose y Análisis:** El flag `--db-update-interval` (en milisegundos) te permite controlar la frecuencia de muestreo. Un valor más bajo (ej. `1000` para 1s) te da más granularidad, mientras que un valor más alto (ej. `5000` para 5s) reduce la carga del propio monitoreo.

> **💡 Consejo Pro:** Usa `Tab` y `Shift+Tab` para navegar rápidamente entre los paneles de CPU, Memoria, Red, etc.
