---
title:  "Configurar una agregación de enlaces (bonding) en bridges Linux"
date:   2024-05-28 01:00:00 +0200
categories: redes
tag: [redes, Planificación y Administración de Redes, router, router Linux, bridge, virtualización, bonding]
toc: false
---
Una agregación de enlaces, link aggregation o port bonding combina varias interfaces de red de forma paralela para aumentar el ancho de banda de una conexión y aportar redundancia ante fallos de una de ellas.

En este post se reutilizará la máquina virtual debian1, empleada en [una entrada anterior](/redes/configurar-vlan-bridge-linux). Esta máquina ya cuenta con dos interfaces de red: ens3 y ens4, conectadas a las interfaces tap0 y tap1 de la máquina anfitriona. Estas interfaces están conectadas bridges diferentes y pertenecen a VLAN diferentes. Por tanto, para preparar el escenario para este ejercicio, se debe eliminar la interfaz tap1 de la VLAN 2 y se debe desconectar del bridge br1 y conectarse a br0.

Así, en primer lugar se saca la interfaz tap1 de la VLAN 2 y se deja únicamente en la VLAN por defecto a la que pertenecen todas las interfaces.

```bash
bridge vlan del dev tap1 vid 2 pvid untagged master
```

Después, se lleva esta interfaz del bridge br1 a br0 para eliminarla del bridge br1 y para conectarla al bridge br0.

```bash
brctl delif br1 tap1
brctl addif br0 tap1
```

Tras realizar estas modificaciones, la máquina debian1 está conectada al bridge br0 a través de dos interfaces diferentes: tap0 y tap1.

# Configuración de la agregación de enlaces entre las dos interfaces

Para configurar el bonding en debian1 se arranca la máquina con el comando usado en el [post anterior](/redes/configurar-vlan-bridge-linux):

```bash
kvm -m 512 -hda deb12-01.qcow2 -device virtio-net,netdev=n0,mac=$mac0 -netdev tap,id=n0,ifname=tap0,script=no,downscript=no -device virtio-net,netdev=n0,mac=$mac2 -netdev tap,id=n0,ifname=tap2,script=no,downscript=no &
```

Desde esta máquina, con conexión a Internet a través del bridge br0, que cuenta con la interfaz ens18 de la máquina anfitriona, se actualiza la paquetería y se instala el paquete ifenslave.

```bash
sudo apt update 
sudo apt install ifenslave
```

Después, se puede configurar el bonding en el fichero /etc/network/interfaces. En este caso se debe declarar la interfaz de tipo bonding, por ejemplo, bond0 y configurar los diferentes parámetros como los de cualquier otra interfaz como la IP, máscara de red o puerta de enlace pero también parámetros propios de las interfaces de tipo bonding como las interfaces que forman parte del bonding o el modo.

```
auto bond0
iface bond0 inet static
    address 172.22.0.28
    netmask 255.255.0.0
    network 172.22.0.0
    gateway 172.22.0.1
    bond-slaves ens3 ens4
    bond-mode 4
```

Para conseguir que el bonding tenga redundancia ante fallos y mejore el ancho de banda disponible, se configura la interfaz en el modo de bonding 4, que utiliza el estándar 802.3ad para la agregación de enlaces. Este estándar permite configurar bonding con alta disponibilidad y aumento de la velocidad.

Al establecer esta configuración y reiniciar el servicio networking de la máquina virtual, se levanta la interfaz bond0 y toma la IP indicada en la configuración:

![](/assets/img/redes/practica19/img11.png)