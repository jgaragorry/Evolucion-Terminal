# 🐳 Módulo 8: Observabilidad de Contenedores

La contenerización ha revolucionado la forma en que desplegamos aplicaciones, pero también ha añadido una nueva capa de abstracción. Cuando una aplicación en un contenedor consume demasiada CPU, ¿cómo lo sabes? Este módulo te presenta la herramienta esencial para tener una visión clara del rendimiento de tus contenedores.

---

### Herramienta: `ctop`

* **Descripción Corta:** Una interfaz similar a `top` pero diseñada específicamente para métricas de contenedores. Es tu panel de control para ver el consumo de recursos de todos tus contenedores en tiempo real.
* **Análisis (Estabilidad y Mantenimiento):** `ctop` es una herramienta madura, estable y enfocada. Hace una cosa y la hace excepcionalmente bien: proporcionar una visión general instantánea de todos los contenedores que se ejecutan en un host. Soporta múltiples backends como Docker y Podman, lo que la hace muy versátil. Es una utilidad indispensable en el arsenal de cualquier desarrollador o sysadmin moderno.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Snap Store (método recomendado y más sencillo).
    * **Prerrequisitos:** Un motor de contenedores como Docker o Podman instalado y en funcionamiento. (Ej: `sudo apt install -y docker.io`).
    * **Comando:** `sudo snap install ctop`

#### Ejercicios Prácticos con `ctop`

* **Ejercicio 1: La "Torre de Control" de tus Contenedores**
    * **Escenario:** Tienes varios contenedores corriendo en tu máquina y necesitas una vista de pájaro para ver si alguno está consumiendo más CPU o memoria de la debida.
    * **Comando:** `sudo ctop` (Se necesita `sudo` si tu usuario no está en el grupo de Docker).
    * **Desglose y Análisis:** `ctop` te presenta una lista de todos tus contenedores activos con su estado, nombre, y las métricas vitales: CPU, Memoria, I/O de disco y Red. Para encontrar al culpable de un alto consumo, simplemente presiona `s` para cambiar el criterio de ordenamiento (`Sort`) hasta que se ordene por la columna que te interesa (CPU% o MEM%).

* **Ejercicio 2: "Pinchar" los Logs de un Contenedor al Instante**
    * **Escenario:** Un contenedor en la lista de `ctop` muestra un comportamiento errático (reinicios constantes o picos de CPU). Necesitas ver sus logs inmediatamente para diagnosticar el problema.
    * **Comando:** (Dentro de la interfaz de `ctop`)
    * **Desglose y Análisis:**
        1.  Usa las flechas **Arriba/Abajo** para seleccionar el contenedor problemático.
        2.  Presiona la tecla `l` (de logs).
    * La pantalla cambiará para mostrarte un `tail -f` de los logs del contenedor en tiempo real. Esto es increíblemente eficiente, ya que te evita tener que cambiar de terminal, listar los contenedores con `docker ps` y finalmente escribir `docker logs -f <nombre_del_contenedor>`.

* **Ejercicio 3: Gestión Rápida del Ciclo de Vida de un Contenedor**
    * **Escenario:** Has identificado un contenedor que se ha quedado "colgado" y necesitas reiniciarlo rápidamente.
    * **Comando:** (Dentro de la interfaz de `ctop`)
    * **Desglose y Análisis:**
        1.  Selecciona el contenedor que quieres gestionar.
        2.  En la parte inferior, verás un menú de acciones rápidas: `stop`, `restart`, `exec`.
        3.  Presiona la tecla `r` (restart). `ctop` te pedirá confirmación y reiniciará el contenedor.
    * `ctop` no es solo una herramienta de visualización pasiva; es un panel de gestión activa que te permite realizar las operaciones más comunes (`stop`, `start`, `restart`, `exec` para obtener un shell) sin salir de la interfaz, optimizando enormemente tu flujo de trabajo.

> **💡 Consejo Pro:** `ctop` tiene una vista de panel único. Selecciona un contenedor y presiona `Enter`. Entrarás en una vista detallada que muestra gráficos históricos del rendimiento de ESE contenedor específico, similar a como lo hace `zenith`, pero para el mundo de los contenedores.
