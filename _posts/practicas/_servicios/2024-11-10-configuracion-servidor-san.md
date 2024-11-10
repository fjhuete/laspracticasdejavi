---
title:  "Configuración de un servidor SAN"
date:   2024-11-10 01:00:00 +0200
categories: servicios
tag: [SAN, Almacenamiento, Dispositivos de bloque, iSCSI, tgt, Administración de Sistemas, servicios, Servicios de Red e Internet]
excerpt: En este post se muestran, a través de un caso práctico, algunos ejemplos de configuración de un servidor SAN.
---

# Gestión de volúmenes en el servidor SAN

El servidor SAN cuenta con 3 discos duros de 2G cada uno. A partir de estos discos se crea un Raid5 y se convierte en un grupo de volúmenes. En este grupo de volúmenes se crean un volumen de 1G que se comparte con un cliente GNU/Linux y dos volúmenes de 512M que se comparten con un cliente Windows.

```
mdadm --create /dev/md0 --level=5 --raid-devices=3 /dev/vdb /dev/vdc /dev/vdd
mdadm --detail --scan --verbose >> /etc/mdadm/mdadm.conf
update-initramfs -u -k all

vgcreate vg1 /dev/md0

lvcreate -L 1G -n lv1 vg1
lvcreate -L 512M -n lv2 vg1
lvcreate -L 512M -n lv3 vg1
```

# Creación de un target para compartir con un cliente GNU/Linux

## Creación del target en el servidor SAN

El primer paso es crear el target en el servidor SAN. Es necesario instalar previamente la herramienta `tgtadm` del paquete `tgt`.

```
sudo apt install tgt
sudo tgtadm --lld iscsi --op new --mode target --tid 1 -T iqn.2024-10.org.javi:target1
```

Además, para compartir el volumen lógico con el cliente hay que añadirlo como Unidad Lógica (LUN) al target recién creado.

```
sudo tgtadm --lld iscsi --op new --mode logicalunit --tid 1 --lun 1 -b /dev/vg1/lv1
```

Ahora hay disponible en el servidor un target con una LUN que se puede compartir.

```
vagrant@san:~$ sudo tgtadm --lld iscsi --op show --mode target
Target 1: iqn.2024-10.org.javi:target1
    System information:
        Driver: iscsi
        State: ready
    I_T nexus information:
    LUN information:
        LUN: 0
            Type: controller
            SCSI ID: IET     00010000
            SCSI SN: beaf10
            Size: 0 MB, Block size: 1
            Online: Yes
            Removable media: No
            Prevent removal: No
            Readonly: No
            SWP: No
            Thin-provisioning: No
            Backing store type: null
            Backing store path: None
            Backing store flags: 
        LUN: 1
            Type: disk
            SCSI ID: IET     00010001
            SCSI SN: beaf11
            Size: 1074 MB, Block size: 512
            Online: Yes
            Removable media: No
            Prevent removal: No
            Readonly: No
            SWP: No
            Thin-provisioning: No
            Backing store type: rdwr
            Backing store path: /dev/vg1/lv1
            Backing store flags: 
    Account information:
    ACL information:
```

Antes de acceder al target desde el cliente (192.168.0.5) hay que hacer que esté disponible a través de la red.

```
sudo tgtadm --lld iscsi --op bind --mode target --tid 1 -I 192.168.0.5
```

Esta configuración desaparece con el apagado de la máquina. Para hacerla persistente a reinicios se debe volcar a un fichero de configuración. Esta operación se puede realizar con la herramienta `tgt-admin`.

```
tgt-admin --dump > /etc/tgt/conf.d/san.conf
```

## Configuración del volumen en el cliente

Para configurar un target como volumen en el cliente es necesario usar la herramienta `iscasiadm`, disponible en el paquete `open-iscsi`. La instalación de esta herramienta genera el fichero de configuración /etc/iscsi/initiatorname.iscsi, que crea un initiator en el cliente para permitirle usar los volúmenes que comparte el servidor.

```
sudo apt install open-iscsi
```

Antes de configurar la conexión, es necesario conocer cuáles son los targets que ofrece el servidor SAN (192.168.0.4) disponibles en la red para el cliente.

```
vagrant@cliente:~$ iscsiadm --mode discovery --type sendtargets --portal 192.168.0.4
192.168.0.4:3260,1 iqn.2024-10.org.javi:target1
```

Con el nombre del target que se recibe desde el servidor se puede configurar la conexión.

```
vagrant@cliente:~$ iscsiadm --mode node -T iqn.2024-10.org.javi:target1 --portal 192.168.0.4 --login
Logging in to [iface: default, target: iqn.2024-10.org.javi:target1, portal: 192.168.0.4,3260]
Login to [iface: default, target: iqn.2024-10.org.javi:target1, portal: 192.168.0.4,3260] successful.
```

El volumen ya está disponible en el cliente.

```
vagrant@cliente:~$ lsblk
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0    1G  0 disk 
```

También se puede crear un sistema de ficheros en este disco duro.

```
vagrant@cliente:~$ sudo mkfs.ext4 /dev/sda
mke2fs 1.47.0 (5-Feb-2023)
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: 631106dd-f545-4129-840c-f4e4560504bd
Superblock backups stored on blocks: 
    32768, 98304, 163840, 229376

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
```

## Montar el volumen de forma persistente en el cliente usando `systemd mount`

Para que el proceso de descubrimiento y login del target sea persistente a los reinicios es necesario comprobar que el parámetro `node.startup` del fichero de configuración /etc/iscsi/iscsid.conf tiene el valor `automatic`.

```
#*****************
# Startup settings
#*****************

# To request that the iscsi service scripts startup a session, use "automatic":
 node.startup = automatic
#
# To manually startup the session, use "manual". The default is manual.
#node.startup = manual
```

Además, hay que añadir el nodo.

```
vagrant@cliente:~$ iscsiadm --mode node -T iqn.2024-10.org.javi:target1 --portal 192.168.0.4 -o new
New iSCSI node [tcp:[hw=,ip=,net_if=,iscsi_if=default] 192.168.0.4,3260,-1 iqn.2024-10.org.javi:target1] added
```

Y, en el registro del nodo cambiar el valor del parámetro `discovery.sendtargets.use_discoveryd` a `Yes`.

```
vagrant@cliente:~$ iscsiadm --mode node -T iqn.2024-10.org.javi:target1 --portal 192.168.0.4 -n discovery.sendtargets.use_discoveryd -v Yes
```

Por último, se debe establecer también un valor al parámetro `discovery.sendtargets.discoveryd_poll_inval`. En este caso, `30`.

```
iscsiadm --mode node -T iqn.2024-10.org.javi:target1 --portal 192.168.0.4 -n discovery.sendtargets.discoveryd_poll_inval -v 30
```

Una vez que el volumen es accesible desde el cliente se puede crear un punto de montaje con systemd para hacerlo persistente. Systemd es un demonio que gestiona y adminisra el sistema y los servicios, entre ellos, el montaje de discos.

Para hacer un montaje persisten con systemd es necesario un archivo de montaje que se crea en el directorio /etc/systemd/system/\<nombre\>.mount.

Al igual que en el fichero /etc/fstab, en el fichero de montaje se pueden especificar el punto de montaje del disco, el sistema de ficheros que usa y las opciones de montaje.

```
sudo nano /etc/systemd/system/san.mount


[Unit]
Description=Nuevo disco montado-san
After=iscsid.service
Requires=iscsid.service
[Mount]
What=/dev/sda
Where=/san
Type=ext4
Options=defaults
[Install]
WantedBy=multi-user.target
```

Para que systemd detecte los cambios se reinicia el daemon.

```
sudo systemctl daemon-reload
```

Una vez que el montaje del dispositivo está configurado a través de systemctl este volumen se puede montar con el siguiente comando:

```
sudo systemctl start san.mount
```

Para hacer persistente a los reinicios el montaje del dispositivo simplemente hay que habilitarlo de la misma forma que se habilitan otros servicios en systemctl.

```
vagrant@cliente:~$ sudo systemctl enable san.mount
Created symlink /etc/systemd/system/multi-user.target.wants/san.mount → /etc/systemd/system/san.mount.
```

Además, se pueden usar otras funciones de systemctl como el status para ver el estado del dispositivo. Así, tras reiniciar el cliente, se puede comprobar que el dispositivo se ha vuelto a montar correctamente.

```
vagrant@cliente:~$ sudo systemctl status san.mount 
● san.mount - Nuevo disco montado-san
     Loaded: loaded (/etc/systemd/system/san.mount; enabled; preset: enabled)
     Active: active (mounted) since Fri 2024-11-01 19:20:34 CET; 22s ago
      Where: /san
       What: /dev/sda
      Tasks: 0 (limit: 1098)
     Memory: 160.0K
        CPU: 4ms
     CGroup: /system.slice/san.mount

nov 01 19:20:34 cliente systemd[1]: Mounting san.mount - Nuevo disco montado-san...
nov 01 19:20:34 cliente systemd[1]: Mounted san.mount - Nuevo disco montado-san.
```

# Creación de un target para compartir con un cliente Windows

## Creación del target en el servidor SAN

El target que se va a compartir con el cliente Windows cuenta con dos unidades lógicas de 512M cada una.

```
sudo tgtadm --lld iscsi --op new --mode target --tid 2 -T iqn.2024-11.org.javi:target2
sudo tgtadm --lld iscsi --op new --mode logicalunit --tid 2 --lun 1 -b /dev/vg1/lv2
sudo tgtadm --lld iscsi --op new --mode logicalunit --tid 2 --lun 2 -b /dev/vg1/lv3
sudo tgtadm --lld iscsi --op bind --mode target --tid 2 -I 192.168.0.6
```

La autenticación CHAP se puede habilitar a través de la línea de comandos con la herramienta `tgtadm` pero la herramienta `tgt-admin` no es capaz de volcar esta información al fichero de configuración persistente, así que para poder configurar la autenticación es necesrio editar directamente el fichero de configuración.

En la declaración del target en este fichero hay que añadir el parámetro `incominguser` que tiene dos valores. El primero de ellos es el usuario y el segundo la contraseña.

```
sudo tgt-admin --dump > /etc/tgt/conf.d/san.conf

sudo nano /etc/tgt/conf.d/san.conf

<target iqn.2024-11.org.javi:target2>
        backing-store /dev/vg1/lv2
        backing-store /dev/vg1/lv3
        initiator-address 192.168.0.6
        incominguser usuario usuariousuario
</target>
```

Para cargar esta modificación en el fichero hay que reiniciar el servicio.

```
sudo systemctl restart tgt
```

## Configuración del volumen en el cliente

Para configurar un volumen compartido en un cliente Windows se usa la herramienta iSCSI initiator, que está instalada en el sistema operativo por defecto.

Al iniciar esta herramienta se muestra un mensaje en el que se avisa de que el servicio iSCSI de Microsoft no se ha activado y pregunta si se quiere iniciar. Tras iniciar el servicio se abre la herramienta del iniciador o cliente iscsi en la que se puede configurar una nueva conexión.

Con esta herramienta se puede establecer una conexión rápida al servidor iscsi con la que se puede detectar un destino e inicar sesión en él indicando el nombre de domino o la dirección IP del servidor.

![](/assets/img/servicios/san/p3_c1.png)

Tras detectar los targets creados en el servidor y compartidos con el cliente, estos se muestran en la lista de destinos detectados. Para conectarse al target hay que seleccionarlo y pulsar el botón "conectar".

![](/assets/img/servicios/san/p3_c2.png)

Este botón abre una ventana emergente en la que se establece la conexión al servidor SAN. En esta ventana hay que seleccionar las opciones avanzadas y marcar la casilla de autenticación CHAP. Igualmente, es necesario indicar también el nombre de usuario y la contraseña que se han establecido para este target en la configuración del servidor. El cliente iscsi de Windows requiere que estas contraseñas tengan una longitud de, al menos, 12 caracteres.

![](/assets/img/servicios/san/p3_c3.png)

Tras establecer la conexión, en la herramienta de administración de discos ya aparecen los dos volúmenes asociados al target compartido por el servidor. Desde esta herramienta se pueden formatear y montar en el cliente Windows.

En este caso se formatean como dos nuevos volúmenes simples identificados por las letras `E:` y `F:`. Tras montarlos y formatearlos ya son accesibles y utilizables desde el cliente y se puede comenzar a almacenar ficheros en ellos.

![](/assets/img/servicios/san/p3_c4.png)