---
title:  "Instalación de Debian 12 bajo LVM en Proxmox"
date:   2024-04-04 01:00:00 +0200
categories: fundamentos-hardware
tag: [Fundamentos de Hardware, debian, linux, instalación, lvm, comandos, snapshot, volúmenes lógicos]
---
Proxmox Virtual Environment, o Proxmox VE, entorno de virtualización de servidores de código abierto basado en Debian. En este post se usa este entorno para crear una máquina virtual en la que se instala un sistema operativo Debian 12 bajo volúmenes lógicos (lvm).

# Instalación del sistema operativo

## Creación de la máquina

En primer lugar se crea la máquina con el botón “Crear VM” de proxmox. A continuación, se le asigna un nombre, en este caso, lvm1. 

![](/assets/img/fundamentos/practica7/img1.png)

Posteriormente se elige el sistema operativo de la máquina. Por ejemplo, Debian 12 con la versión firmware UEFIx86_64.

![](/assets/img/fundamentos/practica7/img2.png)

Para este ejemplo se añaden dos discos: virtio0, de 3GiB y virtio1, de 2GiB.

Tras configurar y crear la máquina virtual en proxmox, se ejecuta el instalador de Debian 12. El proceso de instalación es el habitual en cuanto a la selección del idioma y la creación de usuarios y contraseñas. En la sección de particionado del proceso de instalación se crea, en primer lugar, la partición de 50MB para el arranque efi:

![](/assets/img/fundamentos/practica7/img3.png)

Con la partición creada, se arranca el gestor de de volúmenes lógicos para crear el grupo de volúmenes vg01 con el espacio restante en el disco 1:

![](/assets/img/fundamentos/practica7/img4.png)

## Añadir un volumen lógico

El sistema se monta en un volumen lógico dentro del grupo de volúmenes vg01 recién creado. Para ello se elige la opción de crear volumen lógico en el menú del gestor de volúmenes lógicos del particionado de discos de Debian y se configuran las características determinadas para este ejemplo.

![](/assets/img/fundamentos/practica7/img5.png)

Posteriormente, se asigna al volumen lógico creado un sistema de ficheros ext4, el punto de montaje “/” y la etiqueta “sistema”.

![](/assets/img/fundamentos/practica7/img6.png)

De esta manera, el disco queda particionado con la siguiente configuración:

![](/assets/img/fundamentos/practica7/img7.png)

Para terminar con la instalación del sistema operativo, se instalan el sistema básico, el gestor de paquetes y los paquetes por defecto incluidos en la distribución. Después, se carga el gestor de arranque y se reinicia el sistema.

# Configuración del volumen lógico y creación de un snapshot

## Añadir disco al grupo vg01

Para añadir un nuevo disco duro a un grupo de volúmenes el primer paso es crear el volumen físico que se va a usar en el grupo de volúmenes con el comando `pvcreate`.

```bash
pvcreate /dev/vdb
```

A continuación, se añade el nuevo volumen físico al grupo de volúmenes con el comando `vgextend`.

```bash
vgextend vg01 /dev/vdb
```

En este punto el nuevo volumen físico ya forma parte del grupo de volúmenes vg01.

## Crear un snapshot

Para crear un snapshot de un volumen lógico se usa el comando `lvcreate` con la opción `-s`.

```bash
lvcreate -L 1G -n snapsistema -s /dev/vg01/sistema
```

Con `mkfs.ext4` se instala el sistema de archivos en el snapshot.

```bash
mkfs.ext4 /dev/vg01/snapsistema
```

Finalmente, se crea el directorio /mnt/snapsistema y se monta el snapshot en él:

```bash
mkdir -p /mnt/snapsistema
mount /dev/vg01/snapsistema /mnt/snapsistema
```

## Prevenir que el snapshot se quede sin espacio

Para evitar que el snapshot se quede sin espacio hay que editar el fichero de configuración /etc/lvm/lvm.conf. En él se habilitan las opciones snapshot_autoextend_threshold y snapshot_autoextend_percent. En este caso, se asigna un 70% al umbral y un 20% al porcentaje, de manera que si el tamaño del snapshot aumenta de 700 MB (el 70% del tamaño asignado), su tamaño aumentará de manera automática a 1,2 GB (un 20% más del tamaño asignado).

![](/assets/img/fundamentos/practica7/img8.png)

## Información de los dispositivos de bloque

Hay dos discos duros disponibles. El primero de ellos cuenta con una partición efi y otra que forma parte del grupo de volúmenes vg01. El segundo disco está usado en su totalidad por el grupo de volúmenes vg01. En ese grupo de volúmenes hay dos volúmenes lógicos, un en el que está instalado el sistema y que está montado en el directorio raíz y otro en el que está la imagen del sistema y que está montado en el directorio /mnt/snapsistema.

En este caso hay un grupo de volúmenes, el vg01, de 4,95 GiB, de los cuales 3,95 GiB están ocupados y 1020 MiB aún están libres.

![](/assets/img/fundamentos/practica7/img9.png)

El volumen lógico sistema está creado sobre el grupo de volúmenes vg01, su tamaño es de 2,95 GiB y es el origen del snapshot snapsistema, que está activo. El volumen lógico sistema está disponible.

![](/assets/img/fundamentos/practica7/img10.png)

El volumen lógico snapsistema está creado sobre el grupo de volúmenes vg01, su tamaño es de 1 GiB y el 11,32% está en uso. Es el destino del snapshot del volumen sistema y está disponible.

![](/assets/img/fundamentos/practica7/img11.png)

# Utilización del snapshot

## Instalación de paquetes

Para generar cambios en el sistema se añaden al fichero de repositorios sources.list los repositorios de la versión testing de Debian y se ejecuta una actualización de la paquetería. La actualización de los más de 300 paquetes disponibles en estos repositorios sobrepasaría, de largo, la capacidad del snapshot pero el update ya supone un cambio importante en el sistema. Además, se instalan algunos paquetes como screen, tree o tldr y algunos servidores como apache, nginx, mariadb o postgre. Con todo esto, el snapshot ya alcanza el 70% de su capacidad inicial y aumenta automáticamente su tamaño hasta 1,20GiB.

## Volver al estado inicial de la máquina

Con el comando `lvconvert` y la opción `--merge` se restaura el estado de la máquina al previo a la creación del snapshot.

# Conclusión

El uso de volúmenes lógicos para la instalación de sistemas operativos es una opción muy práctica en aquellos entornos en los que los sistemas informáticos experimenten cambios con ciertas frecuencia puesto que ofrece unas grandes posibilidades en cuanto a la escalabilidad que facilitan la tarea de la gestión y administración de estos sistemas.

Además, en combinación con esta potente herramienta, el uso de instantáneas o snapshots otorga un seguro importante a la hora de aplicar cambios relevantes y de calado en los sistemas, incluso en el caso de sistemas en producción, puesto que garantiza que, ante un problema en la implementación de un nuevo elemento en el sistema se podrá revertir la situación al punto anterior en el que todo funcionaba a la perfección.