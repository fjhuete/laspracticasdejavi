---
title:  "Instalación de Wireshark y GNS3 en Debian 12 y Windows 10"
date:   2023-10-31 01:00:00 +0200
categories: redes
tag: [Wireshark, GNS3, redes, Planificación y Administración de Redes]
excerpt: Wireshark y GNS3 son dos programas fundamentales para el trabajo de un administrador de redes pero instalarlos puede suponer algunos desafíos. Aquí se incluye una guía de instalación de Wireshark y GNS3 en Windows 10 y en Debian 12
---

# 1. Instalación de Wireshark

## 1.1. Instalación en Debian 12

El paso previo al inicio de la instalación del software requerido para esta práctica es, como en cualquier proceso de instalación en Debian, actualizar los paquetes instalados en la máquina a través de las órdenes `apt update` y `apt upgrade`.

Con el software del equipo actualizado, se puede comenzar con la instalación, en primer lugar, de Wireshark. Para ello, sólo hay que ejecutar en la terminal la orden `apt install wireshark`, tal y como se explica en la documentación técnica disponible en la web del software y, posteriormente, aceptar la ejecución del proceso de instalación.

Cuando finalice la ejecución de esta orden, Wireshark estará instalado en Debian 12 y listo para usarse.

## 1.2. Instalación en Windows 10

El primer paso para la instalación de Wireshark en Windows 10 es acceder a la web del software y descargar el instalador.

![]({{ site.baseurl }}/assets/img/redes/practica3/img1.png)

Una vez finalizada la descarga, al ejecutar el archivo de instalación, Windows pide conceder permisos a la aplicación para que haga cambios en el dispositivo.

![](/assets/img/redes/practica3/img2.png)

Después de concederle esos permisos, lanza un asistente de instalación en el que se puede navegar por varias pantallas para configurar los diferentes elementos de la instalación hasta que, finalmente, se puede ejecutar el proceso.

El instalador muestra una primera pantalla de bienvenida, seguido de una pantalla en la que se recoge la licencia del software con fines informativos, otra nueva pantalla en la que se pueden elegir los componentes que se desean instalar, otra en la que se eligen los accesos directos que se van a generar durante la instalación, en la siguiente se debe indicar la ruta de la carpeta del programa, después se ofrece la posibilidad de instalar junto a Wireshark también Packet Capture y en la siguiente ventana USB Capture. Finalmente, se ejecuta el proceso de instalación.

![](/assets/img/redes/practica3/img3.png)

Finalmente, tras completarse el proceso de instalación, se debe reiniciar el equipo.

![](/assets/img/redes/practica3/img4.png)

# 2. Instalación de GNS3

## 2.1. Instalación en Debian

El proceso de instalación de GNS3 no es tan sencillo como el de Wireshark. Además, la documentación oficial del proyecto GNS3 no recoge las instrucciones de instalación para Debian 12, que no son exactamente iguales a las que se debe seguir en distribuciones anteriores del mismo sistema operativo.

El primer paso es la instalación de varios paquetes relacionados con Python.

`apt install python3-pip python3-venv python3-pyqt5.qtsvg python3-pyqt5.qtwebsockets`

A continuación, se deben instalar varios paquetes relacionados con qemu, según la documentación oficial, tales como qemu, qemu-kvm, qemu-utils, libvirt-clients, libvirt-daemon-system y virtinst. Muchos de estos paquetes generan problemas a la hora de su instalación en Debian 12.

Gracias a la orden `apt search qemu`, aparece una opción, qemu-system, con la que se deben instalar estos paquetes en el equipo y que sí es compatible con Debian 12. Por tanto, en este punto, se puede probar esta opción ejecutando `apt install qemu-system`.

Esta instalación puede devolver algunos errores. Para tratar de solventarlos, se ejecutan las órdenes `apt install qemu-system --fix-missing` y `apt --fix-broken install`.

![](/assets/img/redes/practica3/img5.png)

Sin embargo, aunque parece que algunos errores se han podido solucionar, está claro que aún quedan errores de dependencias que persisten.

![](/assets/img/redes/practica3/img6.png)

De momento, estos errores no impiden continuar con los pasos indicados en el proceso de instalación. El siguiente es la instalación de una nueva serie de paquetes: wireshark, xtightvncviewer, apt-transport-https, ca-certificates, curl, gnupg2 y software-properties-common.

Y hasta aquí llegan las instrucciones incluidas en la documentación oficial de GNS3 para su instalación en Debian. 

Pero la realidad es que aún quedan varios pasos más hasta poder instalar y, por fin, ejecutar este software. Concretamente, se debe crear y activar un entorno virtual de Python e instalar una serie de paquetes desde PyPI, según las instrucciones indicadas por un usuario del foro superuser.com. Para crear el entorno virtual se ejecuta la orden `python3 -m venv gns3env` y para activarlo `source gns3env/bin/activate`. Entonces, se puede instalar el primer paquete: pyqt5. Finalmente, se instalan también los paquetes gns3-server y gns3-gui.

    pip install pyqt5
    pip install gns3-server
    pip install gns3-gui

## 2.2. Instlación en Windows 10

La instalación de GNS3 en Windows no presenta tantas dificultades. En primer lugar, se debe acceder a la web de GNS3 para crear una cuenta y, con ella, descargar el archivo ejecutable que va a guiar la instalación.

![](/assets/img/redes/practica3/img7.png)

Tras crear la cuenta, descargar y ejecutar el instalador, el sistema pregunta si se debe conceder permiso al software para hacer cambios en el dispositivo.

![](/assets/img/redes/practica3/img9.png)

Una vez concedidos los permisos, se lanza el ejecutable. Primero muestra una pantalla de bienvenida y, a continuación, muestra también la licencia. En la siguiente pantalla se debe seleccionar una carpeta del menú de inicio o crear una nueva para el programa y después se eligen los componentes que se van a instalar, la ruta en la que se instalará el programa y el tipo de máquina virtual en las sucesivas pantallas.

![](/assets/img/redes/practica3/img10.png)

Finalmente, se ejecuta el proceso de instalación y, una vez finalizado, se muestra una última pantalla de confirmación de la instalación.

![](/assets/img/redes/practica3/img11.png)

# 3. Instalación de GNS3 VM

La instalación de GNS3 VM es un proceso sencillo que sigue los mismos pasos tanto si se instala en un SO Windows como si se hace en Debian. Se puede hacer de forma autónoma o a través del asistente de instalación incluido en GNS3.

## 3.1. Instalación de GNS3 VM de forma autónoma en Debian 12

En este primer ejemplo, se instalará GNS3 VM en una máquina que usa Debian 12 como sistema operativo. Para hacerlo, no se seguirán los pasos indicados en el asistente de instalación que se puede lanzar desde GNS3 sino que se seguirán las instrucciones que se recogen en la documentación del programa en su web.

En primer lugar se debe descargar el archivo de la web de GNS3.

![](/assets/img/redes/practica3/img12.png)

Posteriormente, se debe descomprimir el archivo e importarlo a VirtualBox desde el menú archivo → importar servicio virtualizado.

![](/assets/img/redes/practica3/img13.png)

Esta acción lanza un asistente para la configuración en el que se debe indicar, en primer lugar, la ubicación del archivo que se desea importar.

![](/assets/img/redes/practica3/img14.png)

Finalmente, en la siguiente ventana, se muestran las propiedades que se van a configurar en el proceso de importación del archivo.

![](/assets/img/redes/practica3/img15.png)

Tras pulsar en terminar, la importación y configuración ha finalizado.

![](/assets/img/redes/practica3/img16.png)

## 3.2. Instalación de GNS3 VM con asistente en Windows 10

Si se prefiere usar el asistente de instalación, el proceso para conseguir hacer funcionar GNS3 VM es ligeramente más sencillo. Desde GNS3 se debe lanzar el asistente de instalación, que te indica que debes instalar VirtualBox o VMWare en el equipo. Una vez realizado este paso, el asistente descarga automáticamente el archivo con extensión .ova al equipo y lanza VirtualBox. En la nueva ventana, solo hay que seleccionar la opción de importar.

![](/assets/img/redes/practica3/img17.png)

A continuación, el asistente añade la ruta al archivo con extensión .ova que se va a importar para configurar GNS3 VM.

![](/assets/img/redes/practica3/img18.png)

Después, en la siguiente ventana, se muestran las preferencias del servicio, algunas de las cuales se pueden modificar.

![](/assets/img/redes/practica3/img19.png)

Finalmente, tras pulsar en terminar, GNS3 VM está configurado.

![](/assets/img/redes/practica3/img20.png)