---
title:  "Convulsión en el mundo del software libre"
date:   2024-12-02 01:00:00 +0200
categories: administracion-sistemas
tag: [sistemas, redhat, linux, centOS, AlmaLinux, IBM, software libre, GPL, administracion-sistemas, Administración de Sistemas Operativos]
excerpt: En este post se repasa el conflicto generado en el mundo del software libre tras las decisiones de Red Hat sobre el proyecto CentOS y las consecuencias para otras distribuciones derivadas.
---

# Red Hat convulsiona el mundo del software libre

Red Hat es una empresa creada en 1993 que cuenta con una historia que ha sido convulsa desde sus inicios. En 1994 publica Red Hat Linux (RHL) y en menos de 10 años abandona el proyecto para lanzar Red Hat Enterprise Linux, más orientado a empresas.

## La relación entre Red Hat y CentOS

En este momento, empiezan a aparecer proyectos independientes como Fedora o CentOS. Red Hat apoya desde un primer momento a la comunidad de Fedora que se convierte en una suerte de versión de pruebas o rolling release de Red Hat donde se pueden usar y testear las versiones más recientes de los paquetes de la distribución.

Sin embargo, CentOS se mantiene completamente independiente hasta 2014 cuando Red Hat entra en el proyecto con el acuerdo de apoyarlo en un primer momento. Unos años después IBM anuncia la compra de Red Hat y esto supone un cambio en las condiciones de CentOS. 

Hasta este  momento, CentOS se había mantenido como una distribución libre, sin embargo, con la llegada de IBM al accionariado de Red Hat la empresa decide reconvertir el proyecto en CentOS Stream, una distribución menos estable y con una licencia más privativa que limita el acceso a la paquetería, al código fuente del software y, sobre todo, que impide que este código se distribuya de forma libre por otros proyectos derivados de CentOS, como AlmaLinux, RockyLinux o SUSE. Además, anuncia un recorte en el período durante el que se mantendrá la última versión de CentOS cuyo fin de vida útil (EoL) llegó a mediados de 2024.

><i class="fas fa-quote-right" aria-hidden="true"></i> El 30 de junio de 2024 será una fecha crucial para el mundo del software empresarial de Linux. Durante casi 20 años, CentOS Linux ha sido el sistema operativo preferido por muchos para las cargas de trabajo de los servidores. Sin embargo, esto cambiará cuando CentOS Linux 7, la última versión que seguía en pie del Community ENTerprise Operating System (sistema operativo para empresas basado en los aportes de la comunidad), llegue al final de su vida útil (EOL).  
Eric Hendricks - Red Hat
{: .notice--primary}

Además, Red Hat cambia la propia naturaleza del proyecto CentOS con este movimiento. Así, CentOS pasa de ser la alternativa libre y abierta a Red Hat pero 100% compatible con su paquetería y con una mirada similar a la estabilidad y robustez de la distribución a convertirse en un punto intermedio en el desarrollo de las versiones de Red Hat entre Fedora y RHEL.

## Las reacciones de la comunidad

Entre los meses de junio y julio de 2023, antes incluso de que pasasen 24 horas desde el anuncio de Red Hat, las distribuciones derivadas de CentOS empezaron a pronunciarse sobre la decisión de la empresa.

La primera en publicar su posicionamiento fue AlmaLinux, que emitió un comunicado en el que informaban del impacto que la decisión de Red Hat suponía para los proyectos forks y derivados de CentOS. Además, AlmaLinux denuncia en este comunicado que conocen la decisión de Red Hat porque un miembro del equipo de desarrollo no encuentra las actualizaciones de la versión 8 del sistema operativo en el repositorio git.centos.org, como era habitual hasta entonces, días antes del anuncio de la empresa.

En este mismo comunicado, el proyecto resalta la gravedad de la decisión de Red Hat, que afecta a todo el ecosistema de Red Hat y sus derivados y plantea un diálogo con el resto de proyectos derivados de CentOS para abordar la situación.

Por su parte, SUSE destacaba en su publicación la preocupación que esta decisión estaba provocando en la comunidad open source y apuntaba a lo mucho que RHEL le debe a los esfuerzos colaborativos para mantener proyectos entre muchos colaboradores, incluyendo el kernel de Linux.

><i class="fas fa-quote-right" aria-hidden="true"></i> El centro de nuestro mundo es innovar juntos. Trabajamos juntos para construir algo mayor que la suma de todas las partes. Somos interdependientes.  
SUSE
{: .notice--primary}

Otra empresa con intereses comerciales en CentOS es Oracle, que distribuye su propio sistema operativo basado en esta distribución. El 10 de julio emitían un duro comunicado contra la decisión de Red Hat en el que señalaban directamente a IBM, empresa competencia de Oracle, como responsables de esta decisión y de la gravedad de las consecuencias que tenía.

En su publicación, Oracle pone de manifiesto la importancia de las contribuciones que su equipo hace al kernel de Linux, así como a los sistemas de ficheros y herramientas que usa Red Hat y se muestran orgullosos de los beneficios que esas contribuciones suponen no sólo para los usuarios de Oracle, sino para todos los usuarios de Linux.

Ante la decisión de Red Hat, Oracle, como el resto de proyectos derivados de CentOS se posiciona firmemente con el planteamiento del ecosistema de software libre de Linux y se muestran abiertos a trabajar con distribuciones derivadas.

Tras la adquisición de Red Hat por parte de IBM la Rocky Enterprise Software Foundation, formada por uno de los creadores de CentOS, crea el proyecto Rocky Linux con el propósito de seguir los objetivos que originalmente se planteaba CentOS. Ante la decisión de Red Hat, este proyecto patrocinado por la empresa MontaVista amplió los períodos de soporte (EoL) de sus distribuciones hasta 2029 (Rocky Linux 8) y 2032 (Rocky Linux). 

Además, MontaVista incorporó en su cartera de servicios un programa de apoyo para aquellas empresas que quisiesen hacer la migración de sus servidores desde CentOS a Rocky Linux.

## Situación actual

Ante el fin del desarrollo de CentOS como un sistema operativo estable equivalente a Red Hat Enterprise Linux, aquellas empresas que usaban esta distribución en sus servidores han tenido que tomar la decisión de hacer una migración a un nuevo sistema operativo optando por una de los dos alternativas que se presentan a partir de esta situación.

Actualmente, lo que empezó siendo un entorno simbiótico en el que unos proyectos alimentaban a otros se encuentra dividido en dos visiones: por una parte la de los proyectos propietarios de Red Hat (RHEL, CentOS Stream) y proyectos apoyados por Red Hat como Fedora y, por otra, la de los proyectos derivados que se han mantenido fieles a los planteamientos del software libre (Rocky Linux, AlmaLinux, Oracle Linux, openSUSE...).

Así, algunas empresas, habrán optado por la solución que propone Red Hat: migrar los servidores que usan una distribución de CentOS a REHL. Esta migración es sencilla y muy similar a una actualización de la versión del sistema operativo. Para estas empresas, esta solución supone pasar de usar un sistema operativo libre y gratuito a tener que firmar una licencia y pagar por el uso de RHEL.

Otras, habrán optado en cambio por una opción alternativa y migrar sus servidores a otro sistema operativo. Uno de los derivados más común es Rocky Linux, que se ha posicionado como un sistema operativo capaz de suplir a CentOS en este tiempo. Por otra parte, AlmaLinux también ha conseguido tener una amplia difusión ante el fin de CentOS y cuenta con el apoyo de varias empresas importantes para su desarrollo. Oracle Linux, en cambio, ha seguido una estrategia comercial muy similar a la que tenía antes del fin de CentOS ofreciendo un sistema operativo robusto y compatible con otros productos de la compañía y trabajando en la implementación por adelantado de mejoras en el kernel de Linux.

Cabe destacar que, a pesar de la decisión tomada por IBM tras adquirir Red Hat, su modelo de licencia sigue estando basado en las licencias open source. De hecho, Red Hat Enterprise Linux es una distribución que se encuentra bajo la licencia GNU General Public License (GPL) que debe permitir usar, estudiar, modificar y copiar el código libremente.

De hecho, la propia licencia GPL bajo la que se distribuye el kernel de Linux obliga a usar licencias libres también para los sistemas operativos que lo usen. 

Sin embargo, tras el cierre de CentOS por parte de Red Hat se pone en cuestión si la licencia de sus distribuciones como Red Hat Enterprise Linux o CentOS Stream cumple realmente con los requisitos necesarios que [establece la Free Software Foundation](https://www.gnu.org/licenses/gpl-3.0.en.html#license-text) para las licencias GLP.

# Uso de Red Hat

## Registro en Red Hat

Para poder descargar, instalar y probar en una máquina el sistema operativo de Red Hat, Red Hat Enterprise Linux (REHL) es necesario crear una cuenta en la página web de la empresa y solicitar una versión de prueba de su distribución de Linux. Este versión de prueba permite usar el sistema operativo durante 60 días, acceso al portal de clientes de la compañía, acceso a todas las versiones disponibles de la distribución y acceso a otros productos de la empresa como Red Hat Satellite y Red Hat Insights.

Hay dos formas de acceder a una versión de prueba. Una de ellas es creando una cuenta en la web de Red Hat y solicitando la imagen iso para la instalación del sistema operativo y otra es contactando con el servicio de ventas de la empresa, que puede poner a disposición de los clientes versiones de prueba más completas que incluyan, por ejemplo, soporte por parte de la compañía. En cualquier caso, las condiciones de la licencia de pruebas de Red Hat impide implantar su sistema operativo en un servidor en producción si no se adquiere una licencia de pago incluso durante el período en el que la licencia de pruebas está vigente.

Al crear la cuenta de usuario en la web, Red Hat recopila una gran cantidad de información personal como el número de teléfono, dirección o sector profesional y puesto de la persona que solicita la versión de prueba de su sistema operativo.

Tras aportar la información solicitada, Red Hat envía un correo de validación con un enlace que lleva a una página de confirmación de la creación de la cuenta y que, directamente comienza la descarga de la versión de pruebas de RHEL desde el navegador web. En ese momento, comienzan a contar los 60 días del período de prueba.

## Instalación y funcionamiento del sistema operativo

### Instalación de Red Hat Enterprise Linux

Tras comenzar la descarga de la imagen iso de instalación de la versión de prueba de RHEL, la web de bienvenida enlaza a la [guía de instalación interactiva](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/interactively_installing_rhel_from_installation_media/index) del sistema operativo. En esta guía se incluye información sobre los requisitos básicos del equipo para poder instalar el sistema operativo, la forma de crear un medio de instalación arrancable, instrucciones para crear una máquina virtual en la que probar el sistema operativo, cómo arrancar el medio de instalación o personalizar las opciones de arranque o la instalación del sistema.

Al arrancar el instalador de Red Hat el sistema operativo muestra un menú de instalación escueto y poco amigable. De entrada, sólo muestra las opciones de instalar o probar e instalar el sistema operativo. En las opciones avanzadas se incluye una opción para comprobar si el equipo cumple con los requisitos del sistema operativo para su instalación o también cuenta con opciones para arrancar el equipo en modo de recuperación.

![](/assets/img/aso/practicaredhat/redhat1.png)

El primer paso tras elegir la opción de instalación en el menú principal de la iso de RHEL es elegir el idioma y la distribución del teclado.

![](/assets/img/aso/practicaredhat/redhat2.png)

En este caso, al acceder a la opción de instalación, el sistema también avisa de que existen ficheros de log disponibles en el directorio /tmp durante todo el proceso de instalación. Además, durante la instalación hay disponible una shell en la terminal TTY2.

A continuación, el instalador abre un resumen de la instalación en el que se listan todos los diferentes apartados sobre los que el usuario puede tomar decisiones de cara a la instalación del sistema operativo. Habitualmente, los instaladores de otros sistema operativos suelen hacer un recorrido por cada uno de estos apartados. En cambio, RHEL muestra directamente un resumen interactivo de la instalación en el que se puede acceder y configurar cada uno de los apartados del proceso de instalación.

![](/assets/img/aso/practicaredhat/redhat7.png)

Usando esta lista se puede acceder, por ejemplo, al menú de particionado. El apartado de particionado del instalador de RHEL contempla varias opciones como la selección del disco duro en el que se instala el sistema operativo, la opción de dejar espacio del disco sin usar durante la instalación o la selección de un particionado manual o automático. También se pueden añadir nuevos discos especializados o almacenamiento compartido en red. Si se elige la opción del particionado manual, se muestran diferentes menús de particionado posteriormente. 

![](/assets/img/aso/practicaredhat/redhat4.png)

Otro de los apartados de la instalación de RHEL, como en todos los sistemas operativos, es el de la creación de usuarios. En este caso, se muestran dos opciones diferentes: para acceder a la configuración del usuario privilegiado y para configurar el usuario normal. Además del nombre de usuario y contraseña, en esta página del instalador se puede especificar el directorio home de usuario, el uid y gid del usuario o añadirlo a varios grupos directamente desde este instalador. Además, se puede activar la opción de que el usuario tenga permisos de administración.

![](/assets/img/aso/practicaredhat/redhat5.png)

Por otra parte, desde el resumen de la instalación también se puede acceder al menú de selección del software que se añade durante el proceso de instalación del sistema operativo. RHEL ofrece varias configuraciones por defecto según el uso que se vaya a dar al equipo en el que se instala el sistema operativo: servidor con interfaz gráfica, servidor, equipo de escritorio, una instalación mínima o una instalación optimizada para virtualización, entre otros. 

Junto al tipo de instalación predeterminada para cada finalidad se pueden seleccionar también una lista de paquetes que añaden nuevas funcionalidades al sistema. Esta lista también es diferente según la finalidad de la instalación. Por ejemplo, en la instalación determinada para servidor se pueden añadir paquetes que instalan servidores de almacenamiento compartido, FTP, DNS o también el entorno gráfico de GNOME, así como herramientas de Debugging o de red.

![](/assets/img/aso/practicaredhat/redhat3.png)

La particularidad que tiene este menú de instalación que no tienen otras distribuciones de GNU/Linux es que durante el proceso de instalación se puede conectar a la cuenta de Red Hat del usuario o vincular al equipo una clave de activación del sistema operativo.

![](/assets/img/aso/practicaredhat/redhat6.png)

### Uso de Red Hat Enterprise Linux

Uno de los primeros aspectos que llama la atención del uso de Red Hat es la cantidad de servicios que se ofrecen constantemente cada vez que se inicia una terminal:

```
Activate the web console with: systemctl enable --now cockpit.socket
```

O, al acceder por SSH:

```
Web console: https://localhost:9090/ or https://172.22.1.45:9090/

Register this system with Red Hat Insights: rhc connect

Example:
# rhc connect --activation-key <key> --organization <org>

The rhc client and Red Hat Insights will enable analytics and additional
management capabilities on your system.
View your connected systems at https://console.Red Hat.com/insights

You can learn more about how to register your system 
using rhc at https://red.ht/registration
```

Muchos de estos servicios adicionales que ofrece la distribución requieren una suscripción de pago para su uso.

Uno de estos servicios es la consola web de RHEL, que aunque tiene algunas partes a las que sólo se puede acceder por suscripción también se puede usar desde la versión de prueba. Esta herramienta ofrece una interfaz web a la que se accede conectándose al puerto 9090 de la máquina RHEL y desde la que se puede gestionar una parte importante del sistema.

![](/assets/img/aso/practicaredhat/redhat8.png)

Esta herramienta permite ver la información básica del sistema y recopilar datos sobre su uso y funcionamiento. Además, también cuenta con diferentes apartados desde los que se puede acceder a los logs del sistema, configurar el almacenamiento o las redes, gestionar contenedores Podman o gestionar las cuentas y servicios contratados a Red Hat.

El sistema operativo RHEL usa dos herramientas diferentes para la gestión de la paquetería del sistema: `dnf` y `yum`. Estos gestores de paquetes son muy similares a otros gestores de paquetes por línea de comando que se usan en otras distribuciones basadas GNU/Linux como, por ejemplo, el gestor de paquetes `apt` en Debian. Como todas las distribuciones basadas en Red Hat, estos gestores usan paquetes .rpm. La particularidad que tiene este sistema operativo respecto a otras distribuciones basadas en el propio Red Hat es que cualquier interacción con el sistema de paquetería exige el uso de la licencia ya sea de pago o de prueba. Así, al intentar realizar una actualización de la paquetería del sistema, por ejemplo, con `dnf` o `yum` el sistema devuelve el siguiente error:

```
[usuario@localhost ~]$ sudo dnf update
Actualización de repositorios de Subscription Management.
No se pudo leer identidad del consumidor

This system is not registered with an entitlement server. You can use "rhc" or "subscription-manager" to register.

Error: No hay repositorios habilitados en "/etc/yum.repos.d", "/etc/yum/repos.d", "/etc/distro.repos.d".
```

La licencia de Red Hat se puede activar desde el proceso de instalación pero también una vez instalado el sistema desde la interfaz web de administración del sistema o desde la terminal usando las herramientas `subscription-manager` o `rhc`.

```
[usuario@localhost ~]$ sudo rhc connect
Connecting localhost.localdomain to Red Hat.
This might take a few seconds.

Username: fjhuete
Password: 

● Connected to Red Hat Subscription Management
● Connected to Red Hat Insights
● Activated the Remote Host Configuration daemon
● Enabled console.redhat.com services: insights, remediations, compliance, remote configuration

Successfully connected to Red Hat!

Manage your connected systems: https://red.ht/connector
```

Al conectar el equipo a la cuenta de Red Hat se desbloquean algunas funcionalidades del panel de gestión de la herramienta web como la actualización de paquetes. En esta pestaña de actualización de software se muestra el estado de la paquetería del sistema. Desde esta interfaz se puede activar la actualización automática tanto de paquetes como de parches en vivo del kernel.

Además, en esta pestaña se muestra en una lista todas las actualizaciones disponibles para el sistema. Esta lista incluye el nombre de cada paquete y su versión pero también una categoría (corrección de fallo, seguridad...) y, en el caso de las actualizaciones de seguridad un nivel de severidad. Junto a toda esta información se añade una descripción del contenido del paquete pendiente de actualizar.

![](/assets/img/aso/practicaredhat/redhat9.png)

Además, se pueden usar los gestores de paquetes `dnf` y `yum` para actualizar e instalar nueva paquetería en el sistema operativo.

```
[usuario@localhost ~]$ sudo dnf update
Actualización de repositorios de Subscription Management.
Red Hat Enterprise Linux 9 for x86_64 - BaseOS   11 MB/s |  38 MB     00:03    
Red Hat Enterprise Linux 9 for x86_64 - AppStre  13 MB/s |  46 MB     00:03    
Última comprobación de caducidad de metadatos hecha hace 0:00:07, el vie 29 nov 2024 11:57:54.
Dependencias resueltas.
================================================================================
 Paquete         Arq.   Versión          Repositorio                       Tam.
================================================================================
Instalando:
 kernel          x86_64 5.14.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms    2.0 M
Actualizando:
 bpftool         x86_64 7.4.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms    2.8 M
 buildah         x86_64 2:1.37.5-1.el9_5 rhel-9-for-x86_64-appstream-rpms  11 M
 containers-common
                 x86_64 2:1-93.el9_5     rhel-9-for-x86_64-appstream-rpms 144 k
 emacs-filesystem
                 noarch 1:27.2-10.el9_4  rhel-9-for-x86_64-appstream-rpms 9.3 k
 expat           x86_64 2.5.0-3.el9_5.1  rhel-9-for-x86_64-baseos-rpms    119 k
 kernel-tools    x86_64 5.14.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms    2.3 M
 kernel-tools-libs
                 x86_64 5.14.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms    2.0 M
 krb5-libs       x86_64 1.21.1-4.el9_5   rhel-9-for-x86_64-baseos-rpms    771 k
 libappstream-glib
                 x86_64 0.7.18-5.el9_4   rhel-9-for-x86_64-appstream-rpms 399 k
 libipa_hbac     x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     39 k
 libsmbclient    x86_64 4.20.2-2.el9_5   rhel-9-for-x86_64-baseos-rpms     75 k
 libsoup         x86_64 2.72.0-8.el9_5.2 rhel-9-for-x86_64-appstream-rpms 407 k
 libsss_certmap  x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     95 k
 libsss_idmap    x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     45 k
 libsss_nss_idmap
                 x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     49 k
 libsss_sudo     x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     38 k
 libwbclient     x86_64 4.20.2-2.el9_5   rhel-9-for-x86_64-baseos-rpms     44 k
 pam             x86_64 1.5.1-22.el9_5   rhel-9-for-x86_64-baseos-rpms    632 k
 pixman          x86_64 0.40.0-6.el9_3   rhel-9-for-x86_64-appstream-rpms 271 k
 podman          x86_64 4:5.2.2-9.el9_5  rhel-9-for-x86_64-appstream-rpms  16 M
 python-unversioned-command
                 noarch 3.9.19-8.el9_5.1 rhel-9-for-x86_64-appstream-rpms  11 k
 python3         x86_64 3.9.19-8.el9_5.1 rhel-9-for-x86_64-baseos-rpms     30 k
 python3-libs    x86_64 3.9.19-8.el9_5.1 rhel-9-for-x86_64-baseos-rpms    8.1 M
 python3-perf    x86_64 5.14.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms    2.1 M
 samba-client-libs
                 x86_64 4.20.2-2.el9_5   rhel-9-for-x86_64-baseos-rpms    5.3 M
 samba-common    noarch 4.20.2-2.el9_5   rhel-9-for-x86_64-baseos-rpms    175 k
 samba-common-libs
                 x86_64 4.20.2-2.el9_5   rhel-9-for-x86_64-baseos-rpms    104 k
 sssd            x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     29 k
 sssd-ad         x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms    220 k
 sssd-client     x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms    173 k
 sssd-common     x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms    1.6 M
 sssd-common-pac x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     99 k
 sssd-ipa        x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms    287 k
 sssd-kcm        x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms    114 k
 sssd-krb5       x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     77 k
 sssd-krb5-common
                 x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     98 k
 sssd-ldap       x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms    164 k
 sssd-proxy      x86_64 2.9.5-4.el9_5.1  rhel-9-for-x86_64-baseos-rpms     76 k
 tuned           noarch 2.24.0-2.el9_5   rhel-9-for-x86_64-baseos-rpms    442 k
 tzdata          noarch 2024b-2.el9      rhel-9-for-x86_64-baseos-rpms    841 k
 webkit2gtk3-jsc x86_64 2.46.3-2.el9_5   rhel-9-for-x86_64-appstream-rpms 4.4 M
 xfsdump         x86_64 3.1.12-4.el9_3   rhel-9-for-x86_64-baseos-rpms    327 k
Instalando dependencias:
 kernel-core     x86_64 5.14.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms     18 M
 kernel-modules  x86_64 5.14.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms     37 M
 kernel-modules-core
                 x86_64 5.14.0-503.15.1.el9_5
                                         rhel-9-for-x86_64-baseos-rpms     31 M

Resumen de la transacción
================================================================================
Instalar     4 Paquetes
Actualizar  42 Paquetes

Tamaño total de la descarga: 149 M
...
Actualizado:
  bpftool-7.4.0-503.15.1.el9_5.x86_64                                           
  buildah-2:1.37.5-1.el9_5.x86_64                                               
  containers-common-2:1-93.el9_5.x86_64                                         
  emacs-filesystem-1:27.2-10.el9_4.noarch                                       
  expat-2.5.0-3.el9_5.1.x86_64                                                  
  kernel-tools-5.14.0-503.15.1.el9_5.x86_64                                     
  kernel-tools-libs-5.14.0-503.15.1.el9_5.x86_64                                
  krb5-libs-1.21.1-4.el9_5.x86_64                                               
  libappstream-glib-0.7.18-5.el9_4.x86_64                                       
  libipa_hbac-2.9.5-4.el9_5.1.x86_64                                            
  libsmbclient-4.20.2-2.el9_5.x86_64                                            
  libsoup-2.72.0-8.el9_5.2.x86_64                                               
  libsss_certmap-2.9.5-4.el9_5.1.x86_64                                         
  libsss_idmap-2.9.5-4.el9_5.1.x86_64                                           
  libsss_nss_idmap-2.9.5-4.el9_5.1.x86_64                                       
  libsss_sudo-2.9.5-4.el9_5.1.x86_64                                            
  libwbclient-4.20.2-2.el9_5.x86_64                                             
  pam-1.5.1-22.el9_5.x86_64                                                     
  pixman-0.40.0-6.el9_3.x86_64                                                  
  podman-4:5.2.2-9.el9_5.x86_64                                                 
  python-unversioned-command-3.9.19-8.el9_5.1.noarch                            
  python3-3.9.19-8.el9_5.1.x86_64                                               
  python3-libs-3.9.19-8.el9_5.1.x86_64                                          
  python3-perf-5.14.0-503.15.1.el9_5.x86_64                                     
  samba-client-libs-4.20.2-2.el9_5.x86_64                                       
  samba-common-4.20.2-2.el9_5.noarch                                            
  samba-common-libs-4.20.2-2.el9_5.x86_64                                       
  sssd-2.9.5-4.el9_5.1.x86_64                                                   
  sssd-ad-2.9.5-4.el9_5.1.x86_64                                                
  sssd-client-2.9.5-4.el9_5.1.x86_64                                            
  sssd-common-2.9.5-4.el9_5.1.x86_64                                            
  sssd-common-pac-2.9.5-4.el9_5.1.x86_64                                        
  sssd-ipa-2.9.5-4.el9_5.1.x86_64                                               
  sssd-kcm-2.9.5-4.el9_5.1.x86_64                                               
  sssd-krb5-2.9.5-4.el9_5.1.x86_64                                              
  sssd-krb5-common-2.9.5-4.el9_5.1.x86_64                                       
  sssd-ldap-2.9.5-4.el9_5.1.x86_64                                              
  sssd-proxy-2.9.5-4.el9_5.1.x86_64                                             
  tuned-2.24.0-2.el9_5.noarch                                                   
  tzdata-20
```

Aunque RHEL y sus distribuciones derivadas mantienen el gestor de paquetes `yum` por compatibilidad con sistemas antiguos, el uso de este sistema está en desuso en la actualidad en favor de su sucesor, `dnf`.

El sistema de ficheros que usa, por defecto, RHEL es xfs. Llama la atención que, aunque se ha seleccionado el particionado automático en la instalación, el sistema se ha instalado en un volumen lógico y no en una partición. Además, se ha creado una partición separada, `/dev/vda1` para el directorio boot.

```
S.ficheros            Tipo     Tamaño Usados  Disp Uso% Montado en
devtmpfs              devtmpfs   4,0M      0  4,0M   0% /dev
tmpfs                 tmpfs      636M      0  636M   0% /dev/shm
tmpfs                 tmpfs      255M   5,0M  250M   2% /run
/dev/mapper/rhel-root xfs         17G   1,9G   16G  11% /
/dev/vda1             xfs        960M   307M  654M  32% /boot
tmpfs                 tmpfs      128M      0  128M   0% /run/user/1000
```

Como en otras muchas distribuciones de GNU/Linux, RHEL configura los dispositivos de red del equipo usando `NetworkManager`. Este gestor de redes está muy extendido en distribuciones que usan, por defecto, el entorno de escritorio de GNOME. `NetworkManager` crea un fichero por cada interfaz de red del equipo. Este fichero se almacena en el directorio /etc/NetworkManager/system-connections/ (anteriormente lo hacía en /etc/sysconfig/network-scripts/ pero esta configuración está desfasada actualmente). En este directorio se almacena la configuración de cada una de las interfaces de red en un fichero diferente con el siguiente formato:

```ini
[connection]
id=enp1s0
uuid=4cc774cc-d170-30b4-98e9-a92067acec23
type=ethernet
autoconnect-priority=-999
interface-name=enp1s0
timestamp=1732866148

[ethernet]

[ipv4]
method=auto

[ipv6]
addr-gen-mode=eui64
method=auto

[proxy]
```

Para gestionar procesos, como en la mayor parte de las distribuciones GNU/Linux actualmente, este sistema operativo usa `systemd`.

```
[usuario@localhost ~]$ sudo systemctl status NetworkManager
● NetworkManager.service - Network Manager
     Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled; p>
     Active: active (running) since Fri 2024-11-29 09:24:13 CET; 30min ago
       Docs: man:NetworkManager(8)
   Main PID: 740 (NetworkManager)
      Tasks: 3 (limit: 7851)
     Memory: 8.0M
        CPU: 382ms
     CGroup: /system.slice/NetworkManager.service
             └─740 /usr/sbin/NetworkManager --no-daemon

nov 29 09:24:23 localhost.localdomain NetworkManager[740]: <info>  [1732868663.>
nov 29 09:24:23 localhost.localdomain NetworkManager[740]: <info>  [1732868663.>
nov 29 09:24:23 localhost.localdomain NetworkManager[740]: <info>  [1732868663.>
nov 29 09:24:23 localhost.localdomain NetworkManager[740]: <info>  [1732868663.>
nov 29 09:24:23 localhost.localdomain NetworkManager[740]: <info>  [1732868663.>
nov 29 09:24:23 localhost.localdomain NetworkManager[740]: <info>  [1732868663.>
nov 29 09:24:23 localhost.localdomain NetworkManager[740]: <info>  [1732868663.>
nov 29 09:25:15 localhost.localdomain NetworkManager[740]: <info>  [1732868715.>
nov 29 09:25:45 localhost.localdomain NetworkManager[740]: <info>  [1732868745.>
nov 29 09:29:45 localhost.localdomain NetworkManager[740]: <info>  [1732868985.
```

# Sistemas operativos derivados

## CentOS Stream

La imagen iso para la instalación de la versión más reciente de CentOS Stream (9) está disponible en la [sección de descargas](https://www.centos.org/download/) de la web de CentOS. CentOS Stream es una distribución derivada de RHEL, de manera que tanto la manera de instalar el sistema operativo como el funcionamiento del mismo es muy similar al descrito previamente.

### Instalación de CentOS Stream

El proceso de instalación de CentOS es muy parecido al de RHEL. De hecho, la única diferencia que se aprecia en el menú de instalación de este sistema operativo respecto al anterior es que aquí no hay ninguna opción para registrar la licenica. El resto de opciones del menú principal de instalación son equivalentes.

El menú de arranque del instalador de CentOS es idéntico al que usa la distribución RHEL. El procedimiento de arranque hasta llegar al menú de instalación también es el mismo en ambos casos. Y este menú de instalación también es muy parecido con pequeñas modificaciones estéticas, principalmente.

![](/assets/img/aso/practicaredhat/centos1.png)

Así, antes de comenzar la instalación CentOS Stream ofrece la posibilidad de configurar todos los parámetros del proceso. Por ejemplo, se pueden selecionar diferentes instalaciones básicas según el uso que vaya a tener el equipo, tal y como ocurre en el caso de RHEL. Aglunas de estas instalaciones predefinidas son la instalación para servidor, la instalación para servidor con entorno gráfico, una instalación minimalista, la instalación para equipos de escritorio o la instalación especializada para virtualización.

![](/assets/img/aso/practicaredhat/centos2.png)

En otros de los menús se puede configurar el particionado del disco duro. Este menú es idéntico al de RHEL y también permite particionar el disco de forma automática o manual o configurar otros discos especiales o almacenamiento en red durante la instalación del sistema operativo.

![](/assets/img/aso/practicaredhat/centos3.png)

El menú de creación de usuarios de CentOS es análogo al de RHEL y también permite definir el nombre de usuario y contraseña, asignar al usuario un directorio home, uid y gid específicos, incluirlo en varios grupos o darle privilegio de administrador.

![](/assets/img/aso/practicaredhat/centos4.png)

### Uso de CentOS Stream

El funcionamiento interno de CentOS Stream es muy similar al de RHEL y el resto de características analizadas en ambos sistemas operativos es análogo. CentOS había sido un proyecto proyecto independiente pero desde hace muchos años es propiedad de la compañía Red Hat y esto ha hecho que ambos sistemas operativos se hayan desarrollado de forma paralela hasta que, tras la compra de Red Hat por parte de IBM la nueva empresa decide convertir el proyecto CentOS es una especie de versión de pruebas de Red Hat, en un punto intermedio entre su rolling release, Fedora, y su distribución más estable, Red Hat Enterprise Linux.

Esto hace que tanto el gestor de paquetes, como el sistema de ficheros que usa por defecto, así como la gestión de las interfaces de red o de los procesos del sistema sea, prácticamente idéntica en ambas distribuciones.

Así, cabe destacar que CentOS Stream usa, al igual que RHEL, los gestores de paquetes `dnf` y `yum` (actualmente en desuso). A diferencia de lo que ocurre al instalar RHEL, en el caso de CentOS se puede actualizar la paquetería del sistema sin necesidad de validar ninguna licencia ni iniciar sesión con la cuenta de Red Hat.

```
[usuario@localhost ~]$ sudo dnf update
CentOS Stream 9 - BaseOS                        2.2 MB/s | 8.3 MB     00:03    
CentOS Stream 9 - AppStream                     3.3 MB/s |  21 MB     00:06    
CentOS Stream 9 - Extras packages                18 kB/s |  19 kB     00:01    
Dependencias resueltas.
Nada por hacer.
¡Listo!
```

Otra similitud que tiene CentOS con RHEL, así como con otras distribuciones libres derivadas de Red Hat que se analizan más adelante en esta práctica es la interfaz web de administración del servidor. Accediendo al puerto 9090 de la máquina en la que se ejecuta el sistema operativo, se puede hacer uso de una interfaz gráfica idéntica a la que ofrece RHEL para administrar la máquina.

![](/assets/img/aso/practicaredhat/centos5.png)

A través de esta herramienta, al igual que en los equipos que usan RHEL como sistema operativo, se puede consultar la información básica sobre el sistema y su configuración, se puede gestionar el almacenamiento del servidor o sus interfaces de red, también se puede acceder a los logs y registros del sistema, así como gestionar las cuentas y servicios de Red Hat contratados. Además, se pueden ejecutar actualizaciones del sistema operativo o comprobar si hay paquetes pendientes de actualizar, así como realizar volcados del kernel, informes de diagnóstico o acceder a una terminal.

El sistema de ficheros que usa, por defecto, CentOS es el mismo que emplea RHEL: xfs. Como ocurre con la distribución principal de Red Hat, en el caso de CentOS, aunque se ha seleccionado el particionado automático en la instalación, el sistema se ha instalado en un volumen lógico y no en una partición. Además, se ha creado una partición separada, `/dev/vda1` para el directorio boot.

```
S.ficheros          Tipo     Tamaño Usados  Disp Uso% Montado en
devtmpfs            devtmpfs   4,0M      0  4,0M   0% /dev
tmpfs               tmpfs      635M      0  635M   0% /dev/shm
tmpfs               tmpfs      254M   3,9M  251M   2% /run
/dev/mapper/cs-root xfs         17G   2,0G   15G  12% /
/dev/vda1           xfs        960M   308M  653M  33% /boot
tmpfs               tmpfs      127M      0  127M   0% /run/user/1000
```

CentOS también replica a RHEL en la gestión de los dispositivos e interfaces de red del equipo. Con esta finalidad, usa el gestor de redes `NetworkManager`, propio de muchas otras distribuciones en las que es habitual encontrar el entorno de escritorio de GNOME. Como ocurre con la distribución principal de Red Hat, en CentOS `NetworkManager` también crea un fichero para cada interfaz de red del equipo que se almacena en el directorio /etc/NetworkManager/system-connections y que contiene la configuración de la interfaz.

```ini
[connection]
id=enp1s0
uuid=9c791151-2181-3146-83ae-228271556413
type=ethernet
autoconnect-priority=-999
interface-name=enp1s0
timestamp=1732896220

[ethernet]

[ipv4]
method=auto

[ipv6]
addr-gen-mode=eui64
method=auto

[proxy]
```

Por último, como todas las distribuciones analizadas en esta práctica, cabe destacar que CentOS también usa la herramienta `systemd` para gestionar los procesos del sistema.

```
[usuario@localhost ~]$ sudo systemctl status cockpit.service 
● cockpit.service - Cockpit Web Service
     Loaded: loaded (/usr/lib/systemd/system/cockpit.service; static)
     Active: active (running) since Sat 2024-11-30 20:01:56 CET; 12min ago
TriggeredBy: ● cockpit.socket
       Docs: man:cockpit-ws(8)
    Process: 1743 ExecStartPre=/usr/libexec/cockpit-certificate-ensure --for-co>
   Main PID: 1757 (cockpit-tls)
      Tasks: 2 (limit: 7848)
     Memory: 4.7M
        CPU: 1.294s
     CGroup: /system.slice/cockpit.service
             └─1757 /usr/libexec/cockpit-tls

nov 30 20:01:55 localhost.localdomain systemd[1]: Starting Cockpit Web Service.>
nov 30 20:01:56 localhost.localdomain systemd[1]: Started Cockpit Web Service.
nov 30 20:01:56 localhost.localdomain cockpit-tls[1757]: cockpit-tls: gnutls_ha
```

## openSUSE Leap

OpenSUSE distribuye a través de su [página de descargas](https://get.opensuse.org/server/) varias versiones de su sistema operativo. Por una parte, algunas de las versiones de la distribución están optimizadas para su uso en ordenadores de escritorio mientras otras están optimizadas para servidores. Dentro del caso de los servidores, existen también varias versiones de openSUSE. 

La distribución Tumbleweed es una distribución de tipo rolling release en la que el proyecto openSUSE libera las versiones más recientes de la paquetería del sistema operativo. Por otra parte, openSUSE Leap es una distribución más estable orientada a su uso en servidores y que cuenta con actualizaciones periódicas pero no tan constantes como en el caso de Tumbleweed. OpenSUSE cuenta, además, con otras distribuciones como MicroOS o Leap Micro orientadas a sistemas ligeros, virtualización y contenedores.

### Instalación de OpenSUSE

El proceso de instalación de OpenSUSE se divide en dos partes. A diferencia del instalador de Debian, que instala el sistema operativo en el equipo de forma interactiva mientras va solicitando la información necesaria para ejecutar las acciones al usuario, OpenSUSE lanza primero todas las preguntas y, cuando toda la instalación se ha configurado entonces ejecuta el proceso de instalación del sistema operativo en el equipo. De hecho, antes de comenzar con el proceso de instalación se muestra una página de resumen en la que el usuario puede comprobar que toda la configuración es correcta y modificar aquellos parámetros que no sean adecuados.

El primer menú del instalador de OpenSUSE permite modificar algunos parámetros como el idioma o indicar algunas opciones de instalación desde la línea de arranque del propio instalador.

![](/assets/img/aso/practicaredhat/suse1.png)

A continuación, se muestra una ventana desde la que se configuran todos los parámetros del instalador. Durante la primera parte del proceso, el instalador solicita la información al usuario para finalmente mostrar un resumen de la configuración y ejecutar la instalación.

Durante la fase de preparación de la instalación se configura la red y, de forma automática, se ejecutan algunas actualizaciones en el instalador, se inicializan los repositorios, se activa la red y se analiza el sistema. De forma interactiva, el instalador pide al usuario que confirme los repositorios que se van a consultar durante la instalación.

![](/assets/img/aso/practicaredhat/suse2.png)

Tras configurar los repositorios también se debe eligir el tipo de entorno de escritorio que se instala con el sistema operativo. Aunque OpenSUSE es un sistema operativo muy orientado al trabajo en servidores, permite instalar entornos de escritorio como KDE Plasma, GNOME, XFCE o un entorno genérico más ligero propio de la distribución.

![](/assets/img/aso/practicaredhat/suse3.png)

El siguiente paso en la configuración del instalador es el particionado del disco. Por defecto, OpenSUSE crea una tabla de particiones GPT y una partición de arranque de 8MB. Además crea dos particiones más, una para el sistema, que usa un sistema de ficheros btrfs y una para la swap. En este paso también se puede acceder al particionado manual o al modo experto.

![](/assets/img/aso/practicaredhat/suse4.png)

Por último, se configura el huso horario, se sincroniza el reloj y se crea el usuario y contraseña para acceder a la máquina tras la instalación del sistema operativo.

![](/assets/img/aso/practicaredhat/suse5.png)

Tras establecer toda la configuración, el instalador muestra resumen de los parámetros indicados anteriormente. Algunos de ellos cuentan con botones que permiten modificarlos de nuevo en este punto de la instalación. Cuando toda la configuración sea correcta, se puede ejecutar el instalador que instala el sistema operativo en el equipo.

![](/assets/img/aso/practicaredhat/suse6.png)


### Uso de OpenSUSE

OpenSUSE usa el sistema de gestión de paquetería `YaST`. Este gestor de paquetes usa interfaces gráficas para la interacción con el usuario. En el caso de que el sistema operativo esté instalado sin un entorno de escritorio, YaST usa ncourses a modo de interfaz gráfica desde la terminal.

![](/assets/img/aso/practicaredhat/suse7.png)

Tal y como se ha configurado durante el proceso de instalación del sistema operativo, OpenSUSE usa btrfs como sistema de ficheros para su estructura de directorios.

```
S.ficheros     Tipo     Tamaño Usados  Disp Uso% Montado en
/dev/vda2      btrfs       19G   2,3G   16G  13% /
devtmpfs       devtmpfs   4,0M      0  4,0M   0% /dev
tmpfs          tmpfs      987M      0  987M   0% /dev/shm
tmpfs          tmpfs      395M   6,0M  389M   2% /run
/dev/vda2      btrfs       19G   2,3G   16G  13% /.snapshots
/dev/vda2      btrfs       19G   2,3G   16G  13% /boot/grub2/i386-pc
/dev/vda2      btrfs       19G   2,3G   16G  13% /boot/grub2/x86_64-efi
/dev/vda2      btrfs       19G   2,3G   16G  13% /home
/dev/vda2      btrfs       19G   2,3G   16G  13% /srv
/dev/vda2      btrfs       19G   2,3G   16G  13% /opt
/dev/vda2      btrfs       19G   2,3G   16G  13% /tmp
/dev/vda2      btrfs       19G   2,3G   16G  13% /usr/local
/dev/vda2      btrfs       19G   2,3G   16G  13% /var
/dev/vda2      btrfs       19G   2,3G   16G  13% /root
tmpfs          tmpfs      198M   4,0K  198M   1% /run/user/1000
```

Para la configuración de la red OpenSUSE se puede habilitar el uso de NetworkManager (también disponible en Ubuntu) o Wicked. Esta instalación usa la herramienta `wicked`. Esta herramienta es compatible con los ficheros /etc/sysconfig/network/ifcfg-* muy comunes en alguna distribuciones de Linux. Esta herramienta se basa en un conjunto de scripts que gestionan la configuración de red del equipo.

```
/etc/wicked
├── client.xml
├── common.xml
├── extensions
│   ├── dispatch
│   ├── firewall
│   ├── hostname
│   ├── ibft
│   ├── netconfig
│   └── redfish-config
├── ifconfig
├── nanny.xml
├── scripts
│   └── redfish-update
└── server.xml
```

Cada interfaz de red cuenta con su propio fichero. Por ejemplo, la interfaz eth0 está configurada en el fichero /var/run/netconfig/eth0/netconfig0:

```
CREATETIME='28'
SERVICE='wicked-dhcp-ipv4'
INTERFACE='eth0'
TYPE='dhcp'
FAMILY='ipv4'
UUID='a4bc4567-f92a-0000-4603-000004000000'
IPADDR='172.22.1.229/16'
NETMASK='255.255.0.0'
NETWORK='172.22.0.0'
BROADCAST='172.22.255.255'
PREFIXLEN='16'
GATEWAYS='172.22.0.1'
DNSDOMAIN='gonzalonazareno.org'
DNSSERVERS='172.22.0.1 192.168.0.1'
DNSSEARCH='gonzalonazareno.org 41011038.41.andared.ced.junta-andalucia.es'
CLIENTID='ff:00:67:57:c2:00:01:00:01:2e:d8:73:c7:52:54:00:67:57:c2'
SERVERID='172.22.0.1'
SENDERHWADDR='bc:24:11:87:0b:93'
ACQUIRED='1732623523'
LEASETIME='85014'
RENEWALTIME='42506'
REBINDTIME='74387'
BOOTSERVERADDR='172.22.0.1'
BOOTFILE='iventoy_loader_16000'
```

Otros ficheros de configuración de red están ubicados en el directorio /etc/dbus-1/system.d/.

```
/etc/dbus-1/system.d/
├── com.Red Hat.tuned.conf
├── cups.conf
├── org.opensuse.Network.AUTO4.conf
├── org.opensuse.Network.conf
├── org.opensuse.Network.DHCP4.conf
├── org.opensuse.Network.DHCP6.conf
├── org.opensuse.Network.Nanny.conf
└── org.opensuse.Snapper.conf
```

Sin embargo, el directorio principal para la configuración de red en OpenSUSE es /etc/sysconfig/network, en el que hay diferentes ficheros para la configuración de cada una de las interfaces de red del equipo.

```
/etc/sysconfig/network/
├── config
├── dhcp
├── ifcfg-eth0
├── ifcfg-eth0.bak
├── ifcfg-lo
├── ifcfg.template
├── if-down.d
├── if-up.d
├── providers [error opening dir]
└── scripts
    └── functions.netconfig
```

El contenido del fichero /etc/sysconfig/network/ifcfg-eth0 es el siguiente:

```
BOOTPROTO='dhcp'
STARTMODE='auto'
ZONE=public
```

El parámetro `BOOTPROTO`, que en este caso tiene el valor `dhcp` indica cómo consigue su IP la interfaz. En las configuraciones de red manuales, este parámetro toma el valor `static`. Otros parámetros que se pueden usar en este fichero son `IPADDR` para indicar la IP estática de una interfaz, `GATEWAY` para indicar la IP del router de la red, `DOMAIN` para indicar un nombre de dominio o `DNS1` para indicar la IP o el nombre del servidor DNS de la red.

Wicked se integra con systemd de manera que para ejecutar acciones sobre la configuración de red se pueden usar los comandos propios de `systemctl` indicando el servicio `network.service`. Por ejemplo:

```
systemctl start network.service
systemctl stop network.service
systemctl restart network.service
```

Por último, cabe destacar que OpenSUSE usa el sistema de control de procesos `systemd` para gestionar los procesos del sistema, al igual que ocurre en otras muchas distribuciones de GNU/Linux.

## AlmaLinux

### Instalación de AlmaLinux

AlmaLinux también ofrece la imagen iso para la instalación de la distribución en su [página de descargas](https://almalinux.org/get-almalinux/). Este sistema operativo es una réplica muy fidedigna de Red Hat Enterprise Linux y los menús del instalador son muy similares en los dos casos. De hecho, las únicas diferencias son algunos detalles estéticos de la hoja de estilo y que en el caso de AlmaLinux no se incluye en el resumen de la instalación la opción de registrar una licencia como ocurre en el caso de Red Hat.

Al arrancar el instalador de AlmaLinux el sistema operativo muestra un menú de instalación casi idéntico al de RHEL, escueto y poco amigable. De entrada, sólo muestra las opciones de instalar o probar e instalar el sistema operativo. En las opciones avanzadas ya se puede comprobar si el equipo cumple con los requisitos del sistema operativo para su instalación o también arrancar el equipo en modo de recuperación.

![](/assets/img/aso/practicaredhat/alma1.png)

En este caso, al acceder a la opción de instalación, el sistema también avisa de que existen ficheros de log disponibles en el directorio /tmp durante todo el proceso de instalación. Además, durante la instalación hay disponible una shell en la terminal TTY2.

![](/assets/img/aso/practicaredhat/alma2.png)

Al igual que en el caso de RHEL o CentOS Stream, el instalador de AlmaLinux muestra, en primer lugar un menú de selección de idioma y distribución del teclado y, a partir de ahí, muestra un menú con los diferentes apartados del proceso de instalación.

![](/assets/img/aso/practicaredhat/alma3.png)

Uno de estos apartados permite seleccionar el software que se instala durante el proceso de puesta en marcha del sistema operativo. Este paso es similar al que ejecuta `tasksel` en el instalador de Debian. En este caso, AlmaLinux permite seleccionar entre varias opciones: una instalación para servidor con interfaz gráfica, una instalación para servidor sin la interfaz gráfica, una instalación minimalista que ofrece las funcionalidades básicas del sistema operativo, una instalación destinada a ordenadores de escritorio y portátiles con entorno de escritorio o una instalación específicamente destinada a la virtualización.

Cada una de estas opciones incluye una lista de software adicional que se puede incluir durante el proceso de instalación del sistema operativo.

![](/assets/img/aso/practicaredhat/alma4.png)

Por otra parte se puede también configurar el particionado del disco duro en el apartado destino de la instalación. En él se puede elegir entre un particionado auotmático o personalizado. También se pueden encriptar los datos del sistema durante la instalación. Desde la interfaz gráfica del instalador también se pueden elegir los discos en los que se instala el sistema operativo, crear volúmenes lógicos, añadir discos de red, etc.

![](/assets/img/aso/practicaredhat/alma5.png)

La creación de usuarios también se hace de forma interactiva a través de la interfaz gráfica del instalador. En el menú de resumen de la instalación hay dos apartados dedicados a este punto. En uno de ellos se configura el usuario privilegiado, su nombre, contraseña, si se permite conectar a través de SSH con el nombre de usuario y la contraseña del root.

En el apartado de configuración de usuario no sólo se puede especificar el nombre, nombre de usuario y contraseña sino que, además, se puede indicar el directorio home del usuario o especificar un id de usuario y grupo de forma manual. Además, se puede añadir al usuario a más grupos desde este menú.

![](/assets/img/aso/practicaredhat/alma6.png)

### Uso de AlmaLinux

De forma análoga a RHEL, el sistema operativo AlmaLinux usa el gestor de paquetes `dnf` o `yum` para la gestión del software del equipo. 

```
[usuario@localhost ~]$ sudo dnf update 
AlmaLinux 9 - AppStream                         2.9 MB/s | 8.6 MB     00:02    
AlmaLinux 9 - BaseOS                            2.3 MB/s | 3.4 MB     00:01    
AlmaLinux 9 - Extras                             16 kB/s |  13 kB     00:00    
Última comprobación de caducidad de metadatos hecha hace 0:00:01, el jue 28 nov 2024 14:15:57.
Dependencias resueltas.
================================================================================
 Paquete                Arq.      Versión                    Repositorio   Tam.
================================================================================
Instalando:
 kernel                 x86_64    5.14.0-503.14.1.el9_5      baseos       2.0 M
Actualizando:
 bpftool                x86_64    7.4.0-503.14.1.el9_5       baseos       2.8 M
 expat                  x86_64    2.5.0-3.el9_5.1            baseos       115 k
 kernel-tools           x86_64    5.14.0-503.14.1.el9_5      baseos       2.3 M
 kernel-tools-libs      x86_64    5.14.0-503.14.1.el9_5      baseos       2.0 M
 libsoup                x86_64    2.72.0-8.el9_5.2           appstream    387 k
 pam                    x86_64    1.5.1-22.el9_5             baseos       548 k
 python3-perf           x86_64    5.14.0-503.14.1.el9_5      baseos       2.1 M
 webkit2gtk3-jsc        x86_64    2.46.3-1.el9_5             appstream    4.4 M
Instalando dependencias:
 kernel-core            x86_64    5.14.0-503.14.1.el9_5      baseos        18 M
 kernel-modules         x86_64    5.14.0-503.14.1.el9_5      baseos        36 M
 kernel-modules-core    x86_64    5.14.0-503.14.1.el9_5      baseos        30 M

Resumen de la transacción
================================================================================
Instalar    4 Paquetes
Actualizar  8 Paquetes

Tamaño total de la descarga: 101 M
...
Actualizado:
  bpftool-7.4.0-503.14.1.el9_5.x86_64                                           
  expat-2.5.0-3.el9_5.1.x86_64                                                  
  kernel-tools-5.14.0-503.14.1.el9_5.x86_64                                     
  kernel-tools-libs-5.14.0-503.14.1.el9_5.x86_64                                
  libsoup-2.72.0-8.el9_5.2.x86_64                                               
  pam-1.5.1-22.el9_5.x86_64                                                     
  python3-perf-5.14.0-503.14.1.el9_5.x86_64                                     
  webkit2gtk3-jsc-2.46.3-1.el9_5.x86_64                                         
Instalado:
  kernel-5.14.0-503.14.1.el9_5.x86_64                                           
  kernel-core-5.14.0-503.14.1.el9_5.x86_64                                      
  kernel-modules-5.14.0-503.14.1.el9_5.x86_64                                   
  kernel-modules-core-5.14.0-503.14.1.el9_5.x86_64                              

¡Listo!
```
Este gestor de paquetes es muy similar a otros gestores de paquetes por línea de comando que se usan en otras distribuciones basadas GNU/Linux como, por ejemplo, el gestor de paquetes `apt` en Debian.

En este punto, llama la atención que mientras que la actualización de los paquetes del sistema operativo instalado usando la imagen iso de RHEL fue de 46 paquetes, en este caso AlmaLinux sólo instala o actualiza 10. Aquí reside una de las principales claves del conflicto generado a partir de la decisión de limitar el acceso a los repositorios de CentOS: las distribuciones libres basadas en Red Hat tienen más dificultades para acceder a la nueva paquetería y, por tanto, las actualizacioes de los paquetes del sistema operativo se producen en menor medida y con más retraso.

Además, RHEL incluye una gran cantidad de funcionalidades adicionales sobre el sistema operativo básico que no ofrecen otras distribuciones libres como AlmaLinux y que también implican una instalación y actualización de un mayor número de paquetes en el sistema operativo.

Al igual que RHEL y CentOS, AlmaLinux usa la herramienta `cockpit` para ofrecer al usuario la posibilidad de administrar el servidor desde una interfaz gráfica. A esta herramienta web se puede acceder desde cualquier navegador conectando al puerto 9090 de la máquina AlmaLinux.

![](/assets/img/aso/practicaredhat/alma7.png)

La diferencia más llamativa entre las opciones que ofrece esta interfaz web y las versiones de la misma herramienta en RHEL y CentOS es que en este caso no aparece el menú desde el que se puede comprobar el estado de la paquetería del sistema y ejecutar las actualizaciones del mismo. Esto puede ser consecuencia, precisamente, de las limitaciones que recientemente ha puesto Red Hat para el acceso a los repositorios del proyecto CentOS a otras distribuciones derivadas.

El sistema de ficheros que usa, por defecto, la distribución AlmaLinux es xfs. Como ocurre durante la instalación de RHEL, aunque se ha seleccionado el particionado automático en la instalación, el sistema se ha instalado en un volumen lógico y no en una partición. Además, se ha creado una partición separada, `/dev/vda1` para el directorio boot.

```
S.ficheros                 Tipo     Tamaño Usados  Disp Uso% Montado en
devtmpfs                   devtmpfs   4,0M      0  4,0M   0% /dev
tmpfs                      tmpfs      636M      0  636M   0% /dev/shm
tmpfs                      tmpfs      255M   5,1M  249M   2% /run
/dev/mapper/almalinux-root xfs         17G   1,8G   16G  11% /
/dev/vda1                  xfs        960M   362M  599M  38% /boot
tmpfs                      tmpfs      128M      0  128M   0% /run/user/1000
```

De la misma forma que RHEL, AlmaLinux configura los dispositivos de red del equipo usando `NetworkManager`. Este gestor de redes está muy extendido en distribuciones que usan, por defecto, el entorno de estrictorio de GNOME. En el caso de este sistema operativo, `NetworkManager` también crea un fichero por cada interfaz de red del equipo y este fichero también se almacena en el directorio /etc/NetworkManager/system-connections/ (anteriormente lo hacía en /etc/sysconfig/network-scripts/ pero esta configuración está desfasada actualmente). Como pasa en RHEL, en este directorio se almacena la configuración de cada una de las interfaces de red en un fichero diferente con el siguiente formato:

```ini
[connection]
id=enp1s0
uuid=643c642c-6e56-3f92-bd39-792f01f9f52a
type=ethernet
autoconnect-priority=-999
interface-name=enp1s0
timestamp=1732796007

[ethernet]

[ipv4]
method=auto

[ipv6]
addr-gen-mode=eui64
method=auto

[proxy]
```

Para gestionar los procesos del sistema, AlmaLinux usa systemd como la mayor parte de distribuciones basadas en GNU/Linux.

```
[usuario@localhost ~]$ sudo systemctl restart NetworkManager
[usuario@localhost ~]$ sudo systemctl status NetworkManager
● NetworkManager.service - Network Manager
     Loaded: loaded (/usr/lib/systemd/system/NetworkManager.service; enabled; p>
     Active: active (running) since Thu 2024-11-28 14:36:23 CET; 5s ago
       Docs: man:NetworkManager(8)
   Main PID: 45807 (NetworkManager)
      Tasks: 4 (limit: 7852)
     Memory: 7.2M
        CPU: 66ms
     CGroup: /system.slice/NetworkManager.service
             └─45807 /usr/sbin/NetworkManager --no-daemon

nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
nov 28 14:36:23 localhost.localdomain NetworkManager[45807]: <info>  [173280098>
```