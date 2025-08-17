# ⚙️ Módulo 2: Gestión Avanzada de Procesos

Un sistema operativo es, en esencia, un gestor de procesos. Dominar las herramientas para inspeccionarlos, filtrarlos y gestionarlos es una habilidad no negociable. Este módulo presenta las alternativas modernas que traen claridad y poder sobre el clásico `ps`.

---

### Herramienta: `procs`

* **Descripción Corta:** Un reemplazo moderno para `ps` escrito en Rust. Ofrece una salida coloreada y legible por defecto, con una sintaxis de búsqueda mucho más intuitiva.
* **Análisis (Estabilidad y Mantenimiento):** `procs` es un proyecto muy maduro y estable. Al estar escrito en Rust, es muy rápido y eficiente. Se mantiene activamente y es considerado por muchos como un reemplazo esencial para `ps` en el día a día.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Ninguno adicional.
    * **Comando:** `sudo apt update && sudo apt install -y procs`

#### Ejercicios Prácticos con `procs`

* **Ejercicio 1: Búsqueda Lógica Combinada (AND/OR)**
    * **Escenario:** Necesitas encontrar todos los procesos que pertenezcan al usuario `root` **Y** que contengan `systemd` en su nombre.
    * **Comando:**
        ```bash
        procs --and root systemd
        ```
    * **Desglose y Análisis:** `procs` te permite realizar búsquedas lógicas de forma nativa.
        * `--and`: Actúa como un operador lógico `Y`. Todos los términos que le siguen deben coincidir en el proceso.
        * `root`, `systemd`: Son los términos de búsqueda.
    * Esto es inmensamente superior a la engorrosa sintaxis de `ps aux | grep root | grep systemd`. También puedes usar `--or` para buscar procesos que cumplan una condición u otra.

* **Ejercicio 2: Ver Qué Procesos Están Escuchando en Puertos de Red**
    * **Escenario:** Quieres una lista rápida de los procesos que están actuando como servidores (escuchando en puertos), sin usar `netstat` o `ss`.
    * **Comando:**
        ```bash
        procs --tcp --udp
        ```
    * **Desglose y Análisis:** `procs` puede enriquecer la salida con contexto de red.
        * `--tcp`: Añade una columna que muestra los puertos TCP en los que el proceso está escuchando.
        * `--udp`: Hace lo mismo para los puertos UDP.
    * Con un solo comando, fusionas la funcionalidad de `ps` y `netstat -tulnp`, obteniendo una lista de procesos y los puertos que tienen abiertos de una forma clara y concisa.

* **Ejercicio 3: Visualizar la Jerarquía de Procesos en un Árbol**
    * **Escenario:** Un script está fallando y quieres entender qué proceso padre lo ha lanzado y si ha generado algún proceso hijo.
    * **Comando:**
        ```bash
        procs --tree
        ```
    * **Desglose y Análisis:**
        * `--tree`: Reorganiza toda la salida en un formato de árbol jerárquico, similar a `pstree`.
    * La ventaja sobre `pstree` es que `procs` te muestra toda la información útil (PID, Usuario, CPU%, Mem%) directamente en el árbol, dándote contexto completo de la relación y el consumo de recursos de cada proceso en la jerarquía.

> **💡 Consejo Pro:** Considera añadir `alias ps=procs` a tu archivo `~/.bashrc`. De esta forma, cada vez que por costumbre escribas `ps`, obtendrás la salida mejorada de `procs` sin tener que pensarlo.

---

### Herramienta: `htop`

* **Descripción Corta:** El estándar de oro moderno para el monitoreo interactivo de procesos. Aunque no es "nuevo", es la base contra la que se miden todas las demás herramientas y está lleno de funciones avanzadas.
* **Análisis (Estabilidad y Mantenimiento):** `htop` es el epítome de la estabilidad. Está disponible en prácticamente todas las distribuciones de Linux y ha sido probado en batalla durante años. Su desarrollo es continuo y se considera una herramienta esencial.
* **Instalación en Ubuntu 24.04 LTS:**
    * **Fuente:** Repositorio Oficial de Ubuntu.
    * **Prerrequisitos:** Generalmente viene preinstalado en Ubuntu Server. Si no, el comando es simple.
    * **Comando:** `sudo apt install -y htop`

#### Ejercicios Prácticos con `htop`

* **Ejercicio 1: Filtrado Interactivo de Procesos**
    * **Escenario:** Hay cientos de procesos y quieres ver en tiempo real solo los que pertenecen a tu servidor web (ej. `apache2` o `nginx`).
    * **Comando:** `htop`
    * **Desglose y Análisis:**
        1.  Una vez dentro de `htop`, presiona `F4` o `/`.
        2.  Aparecerá una barra de filtro en la parte inferior. Escribe `nginx`.
        3.  La lista se filtrará al instante para mostrar solo los procesos que coincidan. Esto es mucho más eficiente que salir, ejecutar `ps | grep` y volver a entrar. Para limpiar el filtro, presiona `F4` y `Esc`.

* **Ejercicio 2: "Seguir" un Proceso y Ver su Jerarquía**
    * **Escenario:** Quieres que la pantalla se centre automáticamente en un proceso específico mientras investigas otras cosas, y también quieres ver su árbol de dependencias.
    * **Comando:** `htop`
    * **Desglose y Análisis:**
        1.  Navega hasta el proceso que te interesa (ej. tu sesión `bash`).
        2.  Presiona `F` (de Follow/Seguir). Ahora, la selección seguirá a ese proceso aunque la lista se reordene.
        3.  Con el proceso aún seleccionado, presiona `F5` para activar la vista de árbol. Verás el proceso en su contexto jerárquico. Vuelve a presionar `F5` para desactivarla.

* **Ejercicio 3: Gestión de Múltiples Procesos a la Vez (Matar en Lote)**
    * **Escenario:** Un servicio se ha vuelto loco y ha generado docenas de procesos hijos que están consumiendo la CPU. Necesitas matarlos a todos de una sola vez.
    * **Comando:** `htop`
    * **Desglose y Análisis:**
        1.  Filtra la lista (`F4`) para ver solo los procesos problemáticos (ej. `php-fpm`).
        2.  Selecciona el primer proceso y presiona la **barra espaciadora**. Se marcará con un asterisco.
        3.  Usa la flecha hacia abajo para seleccionar el resto de procesos, presionando la barra espaciadora en cada uno.
        4.  Una vez que todos los procesos no deseados estén "etiquetados", presiona `F9` (Kill) y luego `9` (para SIGKILL) y `Enter`. `htop` enviará la señal a todos los procesos etiquetados simultáneamente. Esta es una de las características más potentes y que ahorran más tiempo en `htop`.

> **💡 Consejo Pro:** Presiona `F2` (Setup) para entrar al menú de configuración. Aquí puedes añadir, quitar y reordenar columnas. Por ejemplo, puedes añadir `IO_RATE` para ver qué procesos están usando más el disco, haciendo a `htop` aún más poderoso.
