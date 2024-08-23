---
title:  "Configurar redes virtuales (VLAN) en bridges Linux"
date:   2024-05-28 01:00:00 +0200
categories: redes
tag: [redes, Planificación y Administración de Redes, router, router Linux, bridge, virtualización, VLAN]
---
Este post pretende demostrar el funcionamiento y guiar en la configuración de redes virtuales en un bridge creado en una máquina Debian. Parte del escenario creado en [el post anterior](/redes/funcionamiento-basico-linux-bridge), en el que se cuenta con un bridge en el equipo anfitrión que incluye la interfaz que conecta la máquina anfitriona con la Internet (ens3) y una interfaz virtual (tap0) que se ha usado para comprobar la conectividad de la máquina virtual creada en la demostración del funcionamiento del bridge.

También existe en la máquina anfitriona un fichero de imagen qcow2 con una versión comprimida de un sistema operativo debian 12 ya instalado.

Por tanto, para crear y configurar las dos máquinas virtuales necesarias para el desarrollo de esta parte de la práctica se usa la funcionalidad de aprovisionamiento ligero (thin provisioning) de las imágenes qcow2, que permite usar una imagen base para la creación de varias máquinas virtuales sin tener que repetir el proceso de instalación del sistema operativo en cada una de ellas.

A partir de la imagen comprimida creada en la parte anterior de la práctica, deb12-min.qcow2, se pueden crear nuevos ficheros de imagen de aprovisionamiento ligero para las máquinas necesarias para esta parte de la práctica. En una primera aproximación, se necesitan dos máquinas con dos interfaces de red cada una. Por tanto, se crean dos nuevos ficheros de imagen a partir del fichero base.

```bash
qemu-img create -b deb12-min.qcow2 -F qcow2 -f qcow2 deb12-01.qcow2
qemu-img create -b deb12-min.qcow2 -F qcow2 -f qcow2 deb12-02.qcow2.
```

Antes de lanzar las nuevas máquinas virtuales se deben configurar también sus interfaces de red. El primer ejercicio pide que ambas máquinas tengan conectividad hacia la red principal a través del bridge br0. Para ello, es necesario que cada una tenga asociada una interfaz virtual tap y que ambas interfaces formen parte de este bridge. Por tanto, se crean dos interfaces de tipo tap propiedad del usuario debian.

```bash
ip tuntap add mode tap user debian
ip tuntap add mode tap user debian
ip tuntap list
```

En primer lugar, las máquinas virtuales deben tener conexión no sólo entre ellas sino también hacia la red principal, por tanto, estas interfaces se deben incluir en el bridge br0 y se deben levantar. ip l set dev tap0 up y  ip l set dev tap up.

```bash
brctl addif br0 tap0
brctl addif br0 tap1
ip l set dev tap0 up
ip l set dev tap up
```

Finalmente, cada interfaz necesita una dirección MAC aleatoria que se puede asignar a una variable.

```bash
mac0=$(echo "02:"`openssl rand -hex 5 | sed 's/\(..\)/\1:/g; s/.$//'`)
mac1=$(echo "02:"`openssl rand -hex 5 | sed 's/\(..\)/\1:/g; s/.$//'`)
```

Con los ficheros de imagen qcow2 y las interfaces en el bridge se pueden lanzar las máquina virtuales necesarias para este caso práctico. 

```bash
#Debian1
kvm -m 512 -hda deb12-01.qcow2 -device virtio-net,netdev=n0,mac=$mac0 -netdev tap,id=n0,ifname=tap0,script=no,downscript=no &
#Debian2
kvm -m 512 -hda deb12-0.qcow2 -device virtio-net,netdev=n0,mac=$mac1 -netdev tap,id=n0,ifname=tap1,script=no,downscript=no &
```

><i class="fas fa-lightbulb" aria-hidden="true"></i> El carácter `&` al final de cada comando hace que se ejecuten en segundo plano. De esta manera, desde una sola ventana de la terminal se pueden ejecutar varias máquinas virtuales.
{: .notice--primary}

Las dos máquinas arrancan sus interfaces de red con una dirección IP en la misma red de la máquina anfitriona obtenida por DHCP: 172.22.5.225/16 para debian1 y 172.22.3.77/16 para debian2. Ambas se pueden comunicar entre ellas y con Internet a través de la red principal.

# Supuestos para trabajar con las VLAN

## Creación del bridge br1 sin conectividad con el exterior

Para que un bridge no tenga conectividad con el exterior sólo hay que tener en cuenta que no cuenta con ninguna interfaz conectada a Internet. En este caso, la única interfaz que tiene conexión a Internet es la ens18 del equipo anfitrión, por tanto, no debe formar parte de este bridge.

Hay dos formas de crear este segundo bridge: a través de `brctl addbr br1` o, para hacerlo persistente a los reinicios, editando el fichero de configuración /etc/network/interfaces.

## Añadir una segunda interfaz a las máquinas virtuales y conectarlas a br1

Para que la máquinas virtuales creadas en el ejercicio anterior se conecten a través del nuevo bridge necesitan una segunda interfaz. El procedimiento para generarlas es el mismo que se ha seguido hasta ahora.

```bash
ip tuntap add mode tap user debian
ip tuntap add mode tap user debian
ip tuntap list
ip l set dev tap2 up
ip l set dev tap3 up
```

Además, para que estas interfaces permitan a las máquinas virtuales conectarse a través del bridge br1, se deben añadir ambas.

```bash
brctl addif br1 tap2
brctl addif br1 tap3
brctl show
```

Finalmente, cada interfaz necesita una dirección MAC para poder asociarse a una máquina virtual.

```bash
mac0=$(echo "02:"`openssl rand -hex 5 | sed 's/\(..\)/\1:/g; s/.$//'`)
mac1=$(echo "02:"`openssl rand -hex 5 | sed 's/\(..\)/\1:/g; s/.$//'`)
```

Para que las máquinas estén efectivamente conectadas a estas interfaces, se deben lanzar con estos comandos, que presentan ciertas modificaciones respecto a los empleados en el ejercicio anterior:

```bash
#Debian1
kvm -m 512 -hda deb12-01.qcow2 -device virtio-net,netdev=n0,mac=$mac0 -netdev tap,id=n0,ifname=tap0,script=no,downscript=no -device virtio-net,netdev=n0,mac=$mac2 -netdev tap,id=n0,ifname=tap2,script=no,downscript=no &
#Debian2
kvm -m 512 -hda deb12-0.qcow2 -device virtio-net,netdev=n0,mac=$mac1 -netdev tap,id=n0,ifname=tap1,script=no,downscript=no -device virtio-net,netdev=n0,mac=$mac3 -netdev tap,id=n0,ifname=tap3,script=no,downscript=no &
```

Con esta configuración debian1 y debian2 están conectadas tanto entre ellas a través de los bridges br0 y br1 como hacia el exterior a través de br0.

## Crear dos máquinas virtuales con una interfaz de red

De nuevo, los pasos que se deben seguir para crear dos máquinas virtuales con una interfaz de red cada una son los mismos que se han repetido en ejercicios anteriores de esta práctica.

Primero, se tienen que crear y levantar las interfaces:

```bash
ip tuntap add mode tap user debian
ip tuntap add mode tap user debian
ip l set dev tap4 up
ip l set dev tap5 up
```

Después, se generan los ficheros de imagen para estas máquinas:

```bash
qemu-img create -b deb12-min.qcow2 -F qcow2 -f qcow2 deb12-03.qcow2
qemu-img create -b deb12-min.qcow2 -F qcow2 -f qcow2 deb12-04.qcow2
```

Posteriormente, se tienen que generar direcciones MAC aleatorias necesarias para la creación de la máquina virtual. Y finalmente, se ejecuta la máquina virtual.

```bash
mac4=$(echo "02:"`openssl rand -hex 5 | sed 's/\(..\)/\1:/g; s/.$//'`)
mac5=$(echo "02:"`openssl rand -hex 5 | sed 's/\(..\)/\1:/g; s/.$//'`)
#Debian3
kvm -m 512 -hda deb12-03.qcow2 -device virtio-net,netdev=n0,mac=$mac4 -netdev tap,id=n0,ifname=tap4,script=no,downscript=no &
#Debian4
kvm -m 512 -hda deb12-04.qcow2 -device virtio-net,netdev=n0,mac=$mac5 -netdev tap,id=n0,ifname=tap5,script=no,downscript=no &
```

## Conectar debian1 con debian3 y debian2 con debian4 en el bridge br1 usando una VLAN

Para conectar las cuatro máquinas en el bridge br1 todas ellas tienen que tener alguna de sus interfaces en este bridge. Por tanto, el primer paso imprescindible para realizar este ejercicio es añadir al bridge br1 las interfaces de debian3 y debian4.

```bash
brctl addif br1 tap4
brctl addif br1 tap5
```

Posteriormente hay que activar en este bridge la funcionalidad de filtrado por vlan.

```bash
ip l set br0 type bridge vlan_filtering 1
```

Además, hay que indicar la VLAN a la que pertenece cada una de las interfaces del bridge br1. Para cumplir con los requisitos del ejercicio, debian1 y debian3 deben pertenecer a una VLAN, por ejemplo, la VLAN 2 y debian2 y debian4 a otra, por ejemplo, la VLAN 3. Así, debian1 y debian 3 se añaden a la VLAN 2:

```bash
bridge vlan add dev tap1 vid 2 pvid untagged master
bridge vlan add dev tap4 vid 2 pvid untagged master
```

Y debian2 y debian4 a la VLAN 3:

```bash
bridge vlan add dev tap3 vid 3 pvid untagged master
bridge vlan add dev tap5 vid 3 pvid untagged master
```

En primer lugar se tratará de demostrar la conectividad entre debian1 y debian3 a través de br1. Para ello, se configuran las interfaces de estas máquinas virtuales forman parte de este bridge. En debian1 es la ens4 y en debian3 la ens3. Se es asigna manualmente una dirección IP en el fichero de configuración /etc/network/interfaces: la 192.168.0.10 para debian1 y la 192.168.0.30 para debian3.

![](/assets/img/redes/practica19/img7.png)

De la misma forma se puede demostrar la conectividad entre debian2 y debian2 a través de br1 configurando las interfaces de estas máquinas virtuales que forman parte de este bridge (ens4 en debian2 y ens3 en debian4). Se asigna manualmente una dirección IP en el fichero de configuración /etc/network/interfaces: la 192.168.0.20 para debian2 y la 192.168.0.40 para debian4.

![](/assets/img/redes/practica19/img8.png)

Finalmente, se demuestra que no hay conectividad entre debian3 y debian4, que pertenecen a VLAN diferentes en el bridge br1.

![](/assets/img/redes/practica19/img9.png)

Finalmente, cabe destacar que tanto debian1 como debian2 (en la imagen) pueden conectarse a Internet a través del bridge br0, debian3 y debian4 (en la imagen), que no forman parte de ese bridge, no pueden hacerlo.

![](/assets/img/redes/practica19/img10.png)