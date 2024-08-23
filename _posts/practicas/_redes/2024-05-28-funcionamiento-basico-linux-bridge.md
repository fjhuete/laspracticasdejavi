---
title:  "Funcionamiento básico de Linux Bridge"
date:   2024-05-28 01:00:00 +0200
categories: redes
tag: [redes, Planificación y Administración de Redes, router, router Linux, bridge, virtualización]
---
Un bridge o puente en Linux es un dispositivo de red virtual que permite que las máquinas virtuales alojadas en una máquina física usen su tarjeta de red. En este post se muestra un ejemplo de configuración de un Bridge en una máquina Debian.

# Configuración de un bridge en una máquina Debian

Para configurar un Bridge en Linux que permita que la interfaz externa se pueda utilizar por máquinas virtuales se debe conocer, en primer lugar, el nombre de esta interfaz. En este caso, es ens3. Con esta información se puede crear un primer bridge que incluya esta interfaz. Para ello, se crea primero un bridge. A continuación, se le añade la interfaz externa del equipo físico. 

```bash
brctl addbr br0
brctl addif br0 ens3
```

Cuando se añade la interfaz al bridge, el equipo se desconecta de la red porque la interfaz bridge no está funcionando en ese momento. Para solucionarlo, sólo hay que levantar la interfaz

```bash
ip l set dev br0 up
```

Además se le puede asignar una dirección IP al bridge:

```bash
ip a add 10.0.0.166/24 dev br0
```

De esta manera, el direccionamiento queda definido en el dispositivo br0 y la interfaz ens3 está incluida como un puerto del mismo.

![](/assets/img/redes/practica19/img1.png)

Sin embargo, con esta configuración la máquina no se puede volver a conectar a la red y la interfaz ens3 queda inutilizada. Ante la imposibilidad de solucionar este problema, se opta por intentar hacer la práctica en otra máquina.

En este caso, tras instalar el paquete bridge-utils, se crea el bridge br0 directamente desde el fichero de configuración /etc/network/interfaces. De esta forma, además, la configuración del bridge se hace persistente a los reinicios.

```
auto br0
iface br0 inet dhcp
bridge_ports ens18
```

Y, posteriormente se levanta la interfaz con ifup br0:

```bash
ifup br0
```

La configuración establecida en el fichero se aplica, de manera que con el comando `brctl show` ya se puede ver el bridge br0 en el sistema:


![](/assets/img/redes/practica19/img2.png)

Además, la interfaz toma una configuración TCP/IP por DHCP y obtiene su propia dirección IP con la que la máquina tiene acceso a Internet.

![](/assets/img/redes/practica19/img3.png)

# Funcionamiento del bridge con una máquina virtual

Para comprobar el funcionamiento del bridge con una máquina virtual es necesario crear una en el equipo local en el que se ha creado el bridge. Primero se debe descargar el fichero de imagen .iso del sistema operativo que va a usar la máquina virtual.

```bash
wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.5.0-amd64-netinst.iso
```

A continuación se usa kvm para crear una imagen qcow2 de este sistema. Para poder trabajar con el virtualizador qemu/kvm en la máquina Debian primero se deben instalar los paquetes necesarios.

```bash
apt install qemu-utils qemu-system-x86 qemu-system-gui
```

Una vez que las herramientas de qemu/kvm se han instalado en el equipo se puede convertir la imagen ISO de Debian a un fichero de imagen de tipo qcow2.

```bash
qemu-img create -f qcow2 deb12.qcow2 10G
```

><i class="fas fa-info-circle" aria-hidden="true"></i> Para una información más detallada sobre la instalación de las herramientas de virutalización qemu/kvm y VirtManager puedes consultar [este post](/sistemas/instalar-qumu-kvm-virtualizacion-debian).
{: .notice--primary}

El siguiente paso es crear una interfaz de tipo tap y después se añade la interfaz al bridge.

```bash
tuntap add mode tap user user
brctl addif br0 tap0
ip l set dev tap0 up
```

Posteriormente, se genera una MAC aleatoria para la máquina virtual.

```bash
mac0=$(echo "02:"`openssl rand -hex 5 | sed 's/\(..\)/\1:/g; s/.$//'`)
```

Con esta información, ya se puede crear la máquina virtual.

```bash
kvm -m 512 -hda deb12.qcow2 -cdrom debian-12.5.0-amd64-netinst.iso -device virtio-net,netdev=n0,mac=$MAC0 -netdev tap,id=n0,ifname=tap0,script=no,downscript=no
```

Este comando lanza una ventana para la instalación del sistema operativo de la máquina virtual:

![](/assets/img/redes/practica19/img4.png)

Tras la instalación de la máquina virtual, ésta cuenta con una interfaz, ens3, que ha solicitado una dirección IP por DHCP en el arranque:

![](/assets/img/redes/practica19/img5.png)

Y a través de la cual se puede conectar a Internet:

![](/assets/img/redes/practica19/img6.png)
