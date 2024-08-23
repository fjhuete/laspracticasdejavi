---
title:  "Conceptos básicos del gestor de paquetes apt"
date:   2023-10-25 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, apt, paquetes, comandos, Implantación de Sistemas Operativos]
excerpt: En este post se recoge un breve resumen sobre la información relativa a los gestores de paquetes apt y aptitude incluida en el manual de referencia de Debian
---

# 1. Prerrequisitos de la gestión de paquetes Debian

## 1.1. Sistema de gestión de paquetes

El archivo de Debian está formado por nodos espejos a los que se puede acceder a través del sistema de gestión de paquetes apt (herramienta de empaquetado avanzado), que gestiona los 70312 paquetes disponibles en estos nodos de Debian.

Los tres comandos principales de apt son `apt`, para todas las operaciones de la línea de órdenes; `apt-get`, como opción de reserva en sistemas antiguos en los que apt no está disponible; y `aptitude`, para la gestión interactiva mediante la interfaz de texto.

## 1.2. Configuración de paquetes

Algunos paquetes importantes en la gestión de paquetes en Debian son:

- dpkg. Sistema de gestión de paquetes de bajo nivel para Debian.
- apt. Front-end de APT para administrar paquetes.
- aptitude. Front-end de APT para gestionar paquetes de forma interactiva con
- consola de pantalla completa.
- tasksel. Front-end de APT para instalar las tareas seleccionadas: tasksel(8).
- unattended-upgrades. Paquete mejorado de APT, para permitir la instalación automática de actualizaciones de seguridad.
- gnome-software. Centro de software para GNOME a través de interfaz gráfica.
- synaptic. Gestor de paquetes gráfico.
- apt-utils. Utilidades de APT.
- apt-listchanges. Herramienta de notificación de cambios en el histórico de paquetes.
- apt-listbugs. Relación de bugs críticos después de cada instalación APT.
- apt-file. Utilidad APT para la búsqueda de paquetes a través de la interfaz de línea de órdenes
- apt-rdepends. Relación de dependencias recursivas de los paquetes.

## 1.3. Precauciones principales

La guía de referencia de Debian recomienda usar la versión estable del SO y aplicar sólo actualizaciones de seguridad e indica que, hasta entender muy bien el sistema, se debe evitar incluir pruebas o versiones no estables en la lista de fuentes, mezclar Debian estándar con otros archivos no Debian (como Ubuntu) en la lista de fuentes, no crear el archivo “/etc/apt/preferences”, no modificar el comportamiento de la herramienta de gestión de paquetes a través de los archivos de configuración, no instalar paquetes aleatorios, no borrar ni modificar los archivos de “/var/lib/dpkg” y no instalar paquetes sin probarlos en un entorno seguro.

## 1.4. Conviviendo con actualizaciones continuas

Para usar versiones más nuevas, Debian recomienda elegir la versión testing y limita el uso de la versión unstable a un entorno para depurar paquetes por parte de los desarrolladores. La guía indica que los paquetes de la suite testing se actualizan con la suficiente frecuencia como para ofrecer las funciones más recientes. Para ello, recomienda establecer el nombre “trixie” de la versión testing en la lista de fuentes.

Además, recomienda que, como precaución, se use u sistema de arranque dual con la versión stable en otra partición del equipo, se tenga a mano el CD de instalación para poder hacer un arranque de recuperación del sistema si fuese necesario, se instale el paquete apt-listbugs para comprobar la información del sistema de seguimiento de errores antes de la actualización y se conozca la infraestructura del sistema de paquetes para poder solucionar los problemas que puedan surgir.

## 1.5. Fundamentos del archivo de Debian

El sistema APT permite acceder al archivo de Debian. APT especifica la fuente de los datos en el archivo de lista de fuentes. Este archivo se encuentra en el directorio /etc/apt/ con el nombre sources.list y sources.list.d. En él, cada línea define la fuente de datos para el sistema APT, la línea deb indica las fuentes de paquetes binarios y la línea deb-src indica la fuente de los paquetes fuente. En cada línea, el primer argumento es la URL root del espejo del archivo de Debian que se use y el segundo es el nombre de la suite o nombre en clave de la distribución. El tercer argumento y sucesivos recogen la relación de nombres de área válidos del archivo Debian.

En este sentido, la guía muestra varias advertencias. La primera es que solo la versión estable pura (bookworm) con las actalizaciones de seguridad proporciona la mejor estabilidad. También recomienda no mezclar la versión estable con paquetes de pruebas o inestables. Otra de las advertencias es que se debe evitar indicar más de una de las tres versiones “estable”, “en pruebas” o “inestable” en el archivo de lista de fuentes porque sólo la última es útil.

Por otra parte, también sugiere incluir el enlace a las actualizaciones de seguridad en la lista de fuentes para habilitar este tipo de actualizaciones.

El proceso para el desarrollo de los paquetes de Debian comienza con una entrega a la distribución inestable, después pasan a la distribución en pruebas, donde se comprueba el estado de los informes de errores y su compatibilidad con el último conjunto de paquetes de la distribución. Tras realizar intervenciones manuales para hacer la distribución en pruebas totalmente consistente y libre de errores se publica como estable. Puede ocurrir que en las versiones en prueba e inestable se generen errores porque se suben paquetes rotos al repositorio, hay retrasos en la recepción de nuevos paquetes, hay problemas con el tiempo de sincronización de los archivos o se realizan acciones manuales sobre el archivo para la eliminación de paquetes, por eso, la guía indica que si se quiere usar este tipo de archivos se debe ser capaz de arreglar este tipo de problemas.

## 1.6. Debian es 100% software libre

La guía de referencia de Debian explica que este SO es 100% software libre porque instala por defecto únicamente software libre, proporciona sólo software libre en el área principal (main), recomienda ejecutar únicamente el software libre del área principal y no hay paquetes en principal que dependa o recomienden paquetes de otras áreas. Sin embargo, hay paquetes fuera del sistema Debian que están alojados en los servidores de Debian en las áreas non-free, non-free-firmware y contrib para dar soporte a las necesidades de los usuarios. Estos paquetes implican restricciones a las libertades que sí recogen los paquetes de software libre, falta de soporte de Debian y contagio al 100% del sistema libre Debian.

## 1.7. Dependencias de paquetes

El sistema Debian establece relaciones de dependencia entre los paquetes. Algunos tipos de dependencia son:

- «Depende» (Depends). Declara una dependencia obligatoria y es obligatorio que todos los paquetes enumerados sean instalados al mismo tiempo o que estén instalados previamente.
- «Predepende» (Pre-depends). Son como las dependencias, con la excepción de que es obligatorio que estén instalados completamente con anterioridad.
- «Recomienda» (Recommends). Determina una dependencia fuerte, pero no obligatoria. La mayoría de los usuarios no querrán instalar el paquete al menos que todos los paquetes enumerados en este campo estén instalados.
- «Sugiere» (Suggests). Declara una dependencia débil. Muchos usuario podrían beneficiarse de su instalación si bien tendrán una funcionalidad suficiente sin ellos.
- «Mejora» (Enhances). Declara una dependencia débil como Sugerida pero va en la dirección contraria.
- «Rompe» (Breaks). Declara una incompatibilidad, generalmente con una versión concreta. La solución más común es actualizar todos los paquetes que se encuentran enumerados en este campo.
- «Incompatibles» (Conflicts). Declara su total incompatibilidad. Todos los paquetes enumerados en este campo deben ser eliminados para conseguir instalar el paquete.
- «Sustituye» (Replaces). Se declara cuando los archivos instalados por el paquete sustituyen a los archivos de los paquetes que se enumeran.
- «Proporciona» (Provides). Se declara cuando el paquete proporciona todos los archivos y funcionalidades de los paquetes enumerados.

## 1.8. Flujo de eventos de las órdenes de gestión de paquetes

La guía de referencia indica que el flujo que se debe seguir a la hora de gestionar los paquetes a través de `apt` es:

1. `Update` – Para recuperar metadatos del archivo remoto y actualizar la copia local de los metadatos de apt.
2. `Upgrade` – Para resolver las dependencias de los paquetes, recuperar los binarios  cuya versión candidata es diferente a la instalada, desempaquetar los binarios recuperados e instalarlos.
3. `Install` – Para seleccionar los paquetes indicados, resolver sus dependencias, recuperar los binarios del repositorio, desempcaquetarlos e instalarlos.
4. `Remove` – Para seleccionar los paquetes enumerados, resolver las dependencias, y eliminarlos excepto los archivos de configuración.
5. `Purge` – Para seleccionar los paquetes indicados, resolver las dependencias y eliminarlos, incluidos los archivos de instalación.

## 1.9. Soluciones a problemas básicos en la gestión de paquetes
Para buscar soluciones a problemas en la gestión de paquetes, la guía de Debian dirige a la documentación específica de Debian o del paquete, resolverlos a través de la página del sistema de seguimiento de errores de Debian o de los informes de errores del paquete o a través de una búsqueda en Google.

## 1.10. Cómo seleccionar paquetes Debian
Para elegir un paquete, la guía de Debian recomienda priorizar los esenciales sobre los que no lo son, los del área main sobre los de contrib y non-free, los de prioridad rquerida sobre los importantes, estándar, opcionales y extra, los que cumplen dependencias de otros, los que presentan mayor número de votos e instalaciones, los que cuentan con actualizaciones regulares del desarrollador, los que no presentan errores críticos, graves ni leves en el sistema de seguimiento de errores de Debian, los que cuentan con mayor atención de los desarrolladores a los informes de errores, los que cuentan con mayor número de errores solucionados recientemente y los que tienen menor número de errores en funcionalidades que no son nuevas.

## 1.11. Cómo hacer frente a los requisitos contradictorios
En caso de que se desee instalar un paquete que no está disponible para la suite de Debian que se esté usando, la guía de referencia recomienda primero intentar instalar estos programas en un sistema sandbox, ejecutarlos en entornos chroot y construir versiones de esos binarios que sean compatibles con el sistema Debian. A pesar de estas recomendaciones, reconoce que otras técnicas más “brutales” como apt-pinning, también permiten instalar este tipo de paquetes no compatibles.

# 2. Operaciones básicas en la gestión de paquetes

## 2.1. `apt` vs. `apt-get` / `apt-cache` vs. `aptitude`

`apt`, `apt-get` / `apt-cache` y `ptitude son las tres herramientas básicas para la gestión de paquetes en Debian. aptitude es muy amigable pero no es recomendable para actualizaciones del sistema entre distribuciones estables y puede recomendar la eliminación masiva de paquetes en las actualizaciones del sistema en pruebas o inestable.

`apt-get` y `apt-cache` son las herramientas más básicas de `apt`. Ofrecen únicamente infterfaz por línea de órdenes, son la opción más adecuada para la actualización principal del sistema, tienen un motor robusto para la resolución de dependencias, necesitan menos recursos de hardware, usan expresiones regulares sobre el nombre y la descripción del paquete y permiten gestionar varias versiones del mismo paquete.

`apt` es un interfaz de alto nivel para la gestión de paquetes. También opera desde la línea de órdenes, tiene una barra de progreso cuando se instalan paquetes mediante `apt install` y borra, por defecto, los paquetes .deb descargados en la caché después de instalarlos con éxito.

La orden `aptitude` es más flexible, con un interfaz interactivo a pantalla completa por línea de órdenes, pensado para la gestión interactiva de paquetes diaria. Necesita más recursos de hardware pero tiene un sistema de búsqueda mejorado y permite gestionar múltiples versiones de paquetes de forma más sencilla que `apt-get` y `apt-cache`.

## 2.2. Operaciones básicas de gestión de paquetes utilizando la línea de órdenes

Las órdenes apt, `apt-get` / `apt-cache` y aptitude se pueden mezclar sin problema. Aunque aptitude es una herramienta más sofisticada, con un mejor motor de resolución de dependencias de paquetes, puede causar algunos errores. En este caso, usar apt o `apt-get`/apt-caché puede solucionar el error.

## 2.3. Uso interactivo de aptitude

Con la orden sudo `aptitude -u` se puede iniciar aptitude en modo interactivo. Aptutiude ejecuta de manera automática las acciones pendientes. Estas son algunas de las operaciones básicas que se pueden realizar a través de las órdenes `apt`, `apt-get` / `apt-cache` y `aptitude` con sus correspondientes descripciones:

| Sintaxis de `apt` | Sintaxis de `aptitude` | Sintaxis de `apt-get`/`apt-cache` | Descripción |
|-------------------|------------------------|-----------------------------------|-------------|
| `apt update` | `aptitude update` | `apt-get update` | Actualiza la metainformación de los paquetes |
| `apt install foo` | `aptitude install foo` | `apt-get install foo` | Instala la versión candidata del paquete «foo» y sus dependecias |
| `apt upgrade` | `aptitude safe-upgrade` | `apt-get upgrade` | Actualiza los paquetes ya instalados a las nuevas versiones candidatas sin eliminar ningún paquete |
| `apt full-upgrade` | `aptitude full-upgrade` | `apt-get dist-upgrade` | Actualiza los paquetes ya instalados a las nuevas versiones candidatas y elimina los paquetes que necesite |
| `apt remove foo` | `aptitude remove foo` | `apt-get remove foo` | Elimina el paquete «foo» sin eliminar sus archivos de configuración |
| `apt autoremove` | N/A | `apt-get autoremove` | Elimina los paquetes autoinstalados que ya no son necesarios |
| `apt purge foo` | `aptitude purge foo` | `apt-get purge foo` | Elimina el paquete «foo» y sus archivos de configuración |
| `apt clean` | `aptitude clean` | `apt-get clean` | Limpia por completo el repositorio local de los archivos de paquetes descargados |
| `apt autoclean` | `aptitude autoclean` | `apt-get autoclean` | Limpia el repositorio local de los archivos de paquetes descargados que son obsoletos |
| `apt show foo` | `aptitude show foo` | `apt-cache show foo` | Muestra información detallada sobre el paquete «foo» |
| `apt search expresión_regular` | `aptitude search expresión_regular` | `apt-cache search expresión_regular` | Busca paquetes que concuerden con expresión_regular |
| N/A | `aptitude why expresión_regular` | N/A | Argumenta la razón por la que el paquete que concuerda con la expresión_regular debe ser instalado |
| N/A | `aptitude why-not expresión_regular` | N/A | Argumenta la razón por la que el paquete que concuerda con la expresión_regular no debe ser instalado |
| N/A | `aptitude search '~i!~M'` | `apt-mark showmanual` | Enumera los paquetes que se instalaron de forma manual|

## 2.4. Combinaciones de teclado con `aptitude`

La orden `aptitude` también permite examinar el estado de los paquetes a través del modo pantalla completa usando alguna de las combinaciones de teclado que se listan a continuación:

| Tecla | Función |
|-------|---------|
| F10 o Ctrl-t | Menú |
| ? | Muestra la ayuda de las combinaciones de teclas (una relación más completa) |
| F10 → Ayuda → Manual de usuario | Muestra el Manual de Usuario |
| u | Actualiza la información de archivo del paquete |
| + | Marca el paquete para ser actualizado o instalado |
| - | Marca el paquete para ser eliminado (mantiene los archivos de configuración) |
| _ | Marca el paquete para ser purgado (borra los archivos de configuración) |
| = | Marca el paquete para ser conservado (hold) |
| U | Marca todos los paquetes actualizables (sería el equivalente a una actualización completa) |
| g | Comienza la descarga y la instalación de los paquetes seleccionados |
| q | Sale de la pantalla actual y guarda los cambios |
| x | Sale de la pantalla actual sin guardar los cambios |
| Intro | Muestra la información de un paquete |
| C | Muestra el registro de cambios del paquete |
| l | Cambia el número de paquetes que se muestran |
| / | Busca la primera coincidencia |
| \ | Repite la última búsqueda |

## 2.5. Visualización de paquetes en `aptitude`

En el modo interactivo de pantalla completa de `aptitude`, los paquetes se muestran siguiendo este formato:

| idA | libsmbclient | -2220kB | 3.0.25a-1 | 3.0.25a-2 |
| Bandera* | Nombre del paquete | Variación del espacio de disco usado | Versión actual | Versión candidata |

> Bandera: estado actual (primera letra), acción planeada (segunda letra), automática (tercera letra)
{: .notice--primary}

La guía enumera diferentes formas de mostrar los paquetes en `aptitude`: vista del paquete, por defecto; recomendaciones de auditoría, que muestra una relación de paquetes que se recomiendan por algún paquete marcado para la instalación pero sin instalar aún; relación plana, lista de paquetes sin clasificar para usar con expresiones regulares; navegador de etiquetas debtags, que muestra la relación de paquetes clasificados según las etiquetas Debian; y vista del paquete fuente, que agrupa los paquetes en función de su paquete fuente.

Del modo por defecto, vista de paquetes, aclara que puede mostrar los paquetes actualizables, los paquetes nuevos, los instalados, los no instalados, los creados localmente que se hayan quedado obsoletos, los virtuales (relacionados con la misma función) y las tareas (paquetes con diferentes funciones que son necesarios para realizar una tarea).

## 2.6. Opciones del método de búsqueda con `aptitude`

La guía recoge las diferentes opciones con las que se pueden buscar paquetes usando `aptitude`, ya sea a través del intérprete de órdenes con los argumentos “search” o “show” después del comando `aptitude`, todo ello seguido de una expresión regular o el nombre del paquete respectivamente para conocer el estado de instalación, nombre del paquete y descripción corta o, bien, la descripción detallada del paquete; o bien a través del modo interactivo de pantalla completa usando las teclas 1 para limitar la vista de los paquetes a los coincidentes, / para buscar los paquetes, \ para buscar hacia atrás, n para encontrar el siguiente resultado en la búsqueda o N para encontrar el resultado anterior.

## 2.7. La fórmula de la expresión regular de `aptitude`

La orden `aptitude` permite el uso de fórmulas de expresión regular. La guía de referencia de Debian incluye la siguiente relación de expresiones regulares que se pueden usar en `aptitude`.

| Descripción de las reglas extendidas de encaje | Fórmula de la expresión regular |
|------------------------------------------------|---------------------------------|
| Nombre del paquete que Encaja | ~n nombre_de_la_expresión_regula |
| Encaja en la descripción | ~d descripcion_de_la_expresión_regular |
| Nombre de la tarea que Encaja | ~t expresión_regular_de_tareas |
| Encaja con las etiquetas debian | ~G expresion_regular_de_etiquetas |
| Encaja con el desarrollador | ~m expresión_regular_del_desarrollador |
| Encaja con la sección del paquete | ~s expresión_regular_de_sección |
| Encaja con la versión del paquete | ~V expresión_regular_de_la_versión |
| Encaja con la distribución | ~A {bookworm,trixie,sid} |
| Encaja con el origen | ~O {debian,…} |
| Encaja con la prioridad | ~p {extra,important,optional,required,standard} |
| Encaja con los paquetes esenciales | ~E |
| Encaja con paquetes virtuales | ~v | 
| Encaja con nuevos paquetes | ~N | 
| Encaja con acciones pendientes | ~a {install,upgrade,downgrade,remove,purge,hold,keep} |
| Encaja con paquetes instalados | ~i |
| Encaja con paquetes marcados con A-mark (paquetes auto-instalados) | ~M |
| Encaja con paquetes instalados sin la marca A (paquetes seleccionados por el administrador) | ~i!~M |
| Encaja con paquetes instalados y que se pueden actualizar | ~U |
| Encaja con paquetes eliminados pero no purgados | ~c |
| Encaja con paquete eliminados y purgados o que se pueden eliminar | ~g |
| Encaja con paquetes que declaran una dependencia rota | ~b |
| Encaja con paquetes que declaran una dependencia rota de un tipo | ~B type |
| Encaja el patrón sobre paquetes que tienen una dependencia tipo | ~D [tipo:]patrón |
| Encaja el patrón con paquetes que tienen una dependencia rota de tipo | ~DB [tipo:]patrón |
| Encaja con paquetes en los cuales el patrón encaja con paquetes que declaran una dependecia tipo | ~R [tipo:]patrón |
| Coinciden con paquetes a los que el paquete coincidente patrón declara dependencia rota tipo | ~RB [tipo:]patrón |
| Encaja con los paquetes con los que los paquetes instalados tienen dependencias | ~R~i |
| Encaja con los paquetes que no dependen de ningún paquete instalado | !~R~i |
| Encaja con los paquete que dependen o son recomendados por otros paquetes instalados | ~R~i &#124; ~Rrecommends:~i |
| Encaja con los paquetes según el patrón filtrados por la versión | ~S filtro patrón |
| Encaja con todos los paquetes (verdad) | ~T |
| No Encaja con ningún paquete (falso) | ~F |

## 2.8. Resolución de dependencias en `aptitude`
La guía de referencia indica que `aptitude` se puede configurar de manera que la selección de un paquete no solo marque a todos los definidos en su relación de dependencias, sino también a los incluidos entre sus recomendados. Si estos paquetes no van a ser necesarios en el futuro, `aptitude` los elimina de forma automática.

## 2.9. Registro de la actividad de los paquetes

La actividad de gestión de paquetes queda registrada en el sistema. La guía de referencia de Debian avisa de que no es fácil conseguir una comprensión rápida de estos archivos de registro. Hay tres registros importantes: /var/log/dpkg.log, que registra la actividad de dpkg para las acciones sobre paquetes; /var/log/apt/term.log, que registra las acciones genéricas de APT; y /var/log/aptitude, que registra las acciones de la orden `aptitude`.