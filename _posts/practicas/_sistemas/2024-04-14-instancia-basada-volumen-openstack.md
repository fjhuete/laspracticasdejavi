---
title:  "Cómo crear una instancia basada en un volumen en OpenStack"
date:   2024-04-14 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos, sistema de ficheros, virtualización, OpenStack, Implantación de Sistemas Operativos]
---
OpenStack es un proyecto de cloud computing (computación en la nube) de software libre y código abierto. Ofrece una esctructura como servicio (IaaS) y permite virtualizar equipos en los servidores en los que esté configurado. En este post se muestra, a través de un ejemplo práctico cómo se puede crear una instancia basada en un volumen en OpenStack y cómo se puede volcar el contenido de un sistema a un nuevo volumen con mayor capacidad de almacenamiento.

# Creación y configuración de la instancia en OpenStack

## Creación del volumen

Para crear un volumen en Openstack se usa la opción “crear volumen” del menú volúmenes y en la ventana emergente se indica el nombre del volumen, la descripción, el tamaño y, para cargar en él la imagen de un sistema operativo, se marca la opción “Imagen” del menú “origen del volumen” y, a continuación, se elige la imagen que se debe cargar en el volumen, en este caso, la de Debian 12.

![](/assets/img/sistemas/practica10/img0.png)
![](/assets/img/sistemas/practica10/img1.png)

## Creación de la instancia

Para crear la instancia a partir del volumen anterior, se lanza una instancia de la forma habitual desde el botón “lanzar instancia” del menú “computación” → “instancias” de la forma habitual con la excepción de que en la ventana “origen” se selecciona la opción “volumen” como origen de arranque y se asigna al volumen creado anteriormente.

![](/assets/img/sistemas/practica10/img2.png)
![](/assets/img/sistemas/practica10/img3.png)

Como sabor se usa, en este caso, el m1.normal, que está optimizado para funcionar con un disco de 10GB, el tamaño del volumen que se está usando.

![](/assets/img/sistemas/practica10/img4.png)

><i class="fas fa-exclamation-circle" aria-hidden="true"></i> Los sabores (flavour) pueden variar entre cada instalación de OpenStack.
{: .notice--primary}

## Configuración de la instancia

Para garantizar el acceso a la instancia a pesar de los posibles problemas que puedan suceder durante la práctica se pueden tomar varias medidas. La primera de ellas es poner contraseña tanto al root como al usuario Debian.

```bash
passwd debian
passwd root
```

Además, se puede configurar el fichero /etc/default/grub de manera que el menú de arranque aparezca durante unos segundos antes de iniciar la máquina para poder seleccionar la opción de arrancar en el modo recuperación si fuese necesario.

```
GRUB_DEFAULT=0
GRUB_TIMEOUT=5
```

# Creación y configuración de un nuevo disco en OpenStack

## Creación del nuevo disco

Para crear un nuevo disco duro, en este caso de 30GB, se repite [el procedimiento seguido anteriormente](#creación-del-volumen) indicando esta vez el nuevo tamaño del disco y que el origen es un volumen vacío.

![](/assets/img/sistemas/practica10/img5.png)

Para poder trabajar con él, se asocia a la instancia que se está usando para esta práctica desde el menú “gestionar asociaciones” del volumen o desde el menú “asociar volumen” de la instancia, de manera que en el sistema aparece este nuevo volumen con el nombre vdb:

![](/assets/img/sistemas/practica10/img6.png)

><i class="fas fa-lightbulb" aria-hidden="true"></i> El proceso de particionado y configuración de un disco duro se explica con más detalle en [este otro post](/sistemas/gestion-sistema-ficheros-debian).
{: .notice--primary}

## Particionado del disco

En primer lugar, conviene determinar el tamaño que se debe asignar a cada una de las particiones. Como referencia, se puede tomar el particionado hecho en prácticas anteriores con características similares. Adaptando estos tamaños a un disco de 30GB se puede determinar que los siguientes tamaños son adecuados para cada partición, sin tener más información sobre el uso que va a tener este sistema en producción:

- /boot/efi: 100MB
- /boot: 2 GB
- /: 10GB
- /home: 5GB
- /var: 5GB
- /usr: 6GB
- swap: 2GB 

Para trasladar esta información a la tabla de particiones del nuevo disco duro se pueden usar comandos como `fdisk` o `gdisk`. En este caso, se usa `fdisk` para particionar el disco. Para ello se crea en primer lugar una nueva tabla de particiones con el comando `g`. Posteriormente, se crea cada partición y se le asigna su número, primer sector y tamaño con el comando `n`.

Tras crear todas las particiones necesarias, se les puede asignar un tipo con el comando `t` hasta terminar con esta información en la tabla de particiones del disco:

![](/assets/img/sistemas/practica10/img7.png)

## Asignación del sistema de ficheros

Para continuar volcando el sistema al nuevo disco, el siguiente paso es asignar a cada partición un sistema de ficheros acorde al contenido que debe almacenar. En este caso, todas las particiones tienen un sistema de ficheros de tipo ext4 excepto la partición UEFI, que cuenta con un sistema de ficheros FAT32 y la partición swap, que se debe configurar como tal.

Empezando con la partición EFI, el comando `mkfs.vfat -F 32 -n EFI /dev/vdb1` permite asignarle un sistema de ficheros FAT32 a esta partición. Para el resto de particiones, se usa el comando `mkfs.ext4`, por ejemplo, para la partición boot se usa `mkfas.ext4 -L boot /dev/vdb2`, para la partición raíz del sistema se usa `mkfas.ext4 -L sistema /dev/vdb3`, para la partición home el comando es `mkfas.ext4 -L home /dev/vdb4` y así sucesivamente con el resto de particiones.

Finalmente, para dotar de un sistema de ficheros de tipo swap a la partición swap se usa el comando `mkswap -L swap /dev/vdb7`.

Finalmente, el disco queda particionado de esta forma:

![](/assets/img/sistemas/practica10/img8.png)

## Montaje del nuevo disco

Para volcar la información del sistema al nuevo disco hay dos opciones: montarlo en el propio sistema o generar una nueva instancia en la que se monten los dos volúmenes como discos duros y copiar la información de uno a otro. En este caso, se monta el nuevo disco duro en la instancia que ya está funcionando. Para ello se crean en el directorio /mnt un nuevo directorio para cada una de las particiones del disco con el comando `mkdir -p /mnt/vdb/vdb{1..7}` y con `mount -v /dev/vdbn /mnt/vdb/vdbn` (donde n es el número de la partición) se montan todas ellas en el directorio /mnt de la instancia.

De esta manera, todas las particiones quedan montadas en el sistema:

![](/assets/img/sistemas/practica10/img9.png)

## Volcado de la información al nuevo disco

Con el nuevo disco montado en el sistema se puede comenzar a volcar la información desde el disco original al nuevo volumen. Para ello se usará el comando `cp`.

```bash
cp -ra /var/* /mnt/vdb/vdb5
cp -ra /usr/* /mnt/vdb/vdb6
cp -ra /home/* /mnt/vdb/vdb4
```

Para los directorios /boot y /EFI se debe tener en cuenta que el segundo está dentro del primero, de manera que los comandos que se deben ejecutar son:

```bash
cp -ra /boot/* /mnt/vdb/vdb2
cp -ra /boot/efi/* /mnt/vdb/vdb1
```

Puesto que el contenido de /boot/efi ya está en la partición vdb1, no es necesario que ese directorio esté duplicado dentro de la partición vdb2, así que se puede eliminar.

```bash
rm -r /mnt/vdb/vdb2/efi/
```

Por último, sólo queda volcar el contenido del directorio raíz al nuevo disco duro. En este caso, no se puede hacer un copiado masivo de todo el directorio puesto que alguno de los directorios que contiene generarían un bucle de copia recursiva que colapsaría el sistema.  Por ejemplo, se debe evitar copiar el director /mnt al nuevo disco puesto que éste ya está montado en el propio directorio /mnt. Además, tampoco se deben volver a copiar al nuevo disco duro los directorios que ya tienen su propia partición.

De esta manera, se puede ejecutar el volcado del contenido del directorio raíz al nuevo disco duro de manera un poco más compleja a través del uso de una secuencia de comandos:

```bash
cp -ra /bin /mnt/vdb/vdb3
cp -ra /dev /mnt/vdb/vdb3
cp -ra /etc /mnt/vdb/vdb3
cp -ra /lib* /mnt/vdb/vdb3
cp -ra /media /mnt/vdb/vdb3
cp -ra /opt /mnt/vdb/vdb3
cp -ra /proc /mnt/vdb/vdb3
cp -ra /r* /mnt/vdb/vdb3
cp -ra /s* /mnt/vdb/vdb3
```

## Configuración del fichero fstab

El último paso para tener una réplica del volumen original en el nuevo disco duro de 30GB es configurar el fichero fstab del nuevo sistema, que está alojado en el directorio /mnt/vdb/vdb3/etc/fstab. En él se deben modificar los puntos de montaje de las particiones del volumen original por los puntos de montajes de las nuevas particiones del disco duro de 30GB. Esto se puede hacer redirigiendo la salida del comando `blkid` al fichero para guardar los UUID de todas las particiones y editándolo posteriormente en un editor de texto como nano. Con el siguiente comando se consigue redirigir al fichero fstab, en primer lugar, sólo las líneas relativas a las particiones del nuevo disco de 30GB (vdb) y, además, sólo el campo necesario de cada partición: el UUID.

```bash
blkid | grep vdb | cut -d “ “ -f 3
```

![](/assets/img/sistemas/practica10/img10.png)

# Creación de una instancia basada en el nuevo volumen en Openstack

Tras configurar el sistema y volcarlo al nuevo volumen, este disco se puede desasociar de la instancia y usar para generar una nueva.

En primer lugar, en el menú “desasociar un volumen” se selecciona la opción correspondiente al volumen de 30GB. Además, para que este volumen se pueda usar como origen de nuevas instancias se debe marcar como arrancable. Esto se puede hacer desde el menú “editar volumen” la ventana de volúmenes. En él se debe marcar la opción “arrancable”, que indica que el volumen puede ser usado para iniciar un instancia.

Posteriormente, se lanza una nueva instancia siguiendo los mismos pasos que [anteriormente](#creación-de-la-instancia) pero eligiendo esta vez como origen de la instancia el volumen sobre el que se ha estado trabajando previamente.

![](/assets/img/sistemas/practica10/img11.png)

En este punto se produce un incidencia relevante: la nueva instancia no se puede crear porque es necesario actualizar el sistema de arranque en el nuevo disco. Al intentar ejecutar una actualización del grupo, faltan las variables EFI, que, en este caso, no están presentes en la imagen que se usa para la creación de instancias en Openstack.

Sin variables EFI no se puede actualizar el grub para que se adapte al sistema volcado en el nuevo disco. Esta incidencia hace inviable la práctica en este escenario.

><i class="fas fa-exclamation-circle" aria-hidden="true"></i> Las imágenes en las que se basan las instancias de OpenStack son diferentes en cada instalación y este error puede no aparecer en otros casos.
{: .notice--warning}

><i class="fas fa-circle-info" aria-hidden="true"></i> Para continuar el ejemplo práctico planteado al inicio del post se migra el trabajo a la plataforma de virtualización Proxmox.
{: .notice--primary}

# Volcado del sistema al nuevo disco en Proxmox

## Particionado del nuevo disco

El particionado y volcado del contenido del disco de 10GB al de 30GB se hace en Proxmox de forma análoga a la empleada en el caso de Openstack, ya documentado [anteriormente](#creación-y-configuración-de-un-nuevo-disco-en-openstack) en este post.

De esta manera, se pueden establecer los siguientes tamaños de particiones, teniendo en cuenta que, en este caso, el nuevo disco es de 32GB:

- /boot/efi: 100MB
- /boot: 2 GB
- /: 12GB
- /home: 5GB
- /var: 5GB
- /usr: 6GB
- swap: 2GB

Para trasladar esta información a la tabla de particiones del nuevo disco duro se vuelve a usar `fdisk` para particionar el disco. Para ello se crea en primer lugar una nueva tabla de particiones con el comando `g`. Posteriormente, se crea cada partición y se le asigna su número, primer sector y tamaño con el comando `n`. Tras crear todas las particiones necesarias, se les puede asignar un tipo con el comando `t` hasta terminar con esta información en la tabla de particiones del disco:

![](/assets/img/sistemas/practica10/img12.png)

## Asignación del sistema de ficheros

Para continuar volcando el sistema al nuevo disco, el siguiente paso es asignar a cada partición un sistema de ficheros acorde al contenido que debe almacenar. En este caso, todas las particiones tienen un sistema de ficheros de tipo ext4 excepto la partición UEFI, que cuenta con un sistema de ficheros FAT32 y la partición swap, que se debe configurar como tal.

```bash
mkfs.vfat -F 32 -n EFI /dev/vdb1
mkfs.ext4 -L boot /dev/vdb2
mkfs.ext4 -L sistema /dev/vdb3
mkfs.ext4 -L home /dev/vdb4 
mkfs.ext4 -L var /dev/vdb5
mkfs.ext4 -L usr /dev/vdb6
mkswap -L swap /dev/vdb7
```

Finalmente, el disco queda particionado de esta forma:

![](/assets/img/sistemas/practica10/img13.png)

## Montaje del nuevo disco

Para volcar la información del sistema al nuevo disco hay dos opciones: montarlo en el propio sistema o generar una nueva instancia en la que se monten los dos volúmenes como discos duros y copiar la información de uno a otro. En este caso, se monta el nuevo disco duro en la instancia que ya está funcionando. Para ello se crean en el directorio /mnt un nuevo directorio para cada una de las particiones del disco con el comando `mkdir -p /mnt/destino{efi,boot,sistema,home,var}` y con `mount -v /dev/vdbn /mnt/destino/partición` (donde n es el número de la partición y partición el nombre descriptivo de su contenido) se montan todas ellas en el directorio /mnt de la instancia.

De esta manera, todas las particiones quedan montadas en el sistema:

![](/assets/img/sistemas/practica10/img14.png)

## Volcado de la información al nuevo disco

Con el nuevo disco montado en el sistema se puede comenzar a volcar la información desde el disco original al nuevo volumen. Para ello se usarán los comandos `rsync` y `cp`.

```bash
cp -ra /var/* /mnt/destino/var
cp -ra /usr/* /mnt/destino/usr
cp -ra /home/* /mnt/destino/home
```

Para los directorios /boot y /EFI se debe tener en cuenta que el segundo está dentro del primero, de manera que los comandos que se deben ejecutar son:

```bash
rsync -ra --exclude=efi /boot/* /mnt/destino/boot
cp -ra /boot/efi/* /mnt/destino/efi
```

Puesto que el contenido de /boot/efi ya está dentro del directorio /boot, no es necesario que ese directorio esté duplicado dentro de la partición vdb2, así que se puede no copiar excluyéndolo con la opción `--exclude` del comando `rsync`.

Por último, sólo queda volcar el contenido del directorio raíz al nuevo disco duro. En este caso, no se puede hacer un copiado masivo de todo el directorio puesto que alguno de los directorios que contiene generarían un bucle de copia recursiva que colapsaría el sistema.  Por ejemplo, se debe evitar copiar el director /mnt al nuevo disco puesto que éste ya está montado en el propio directorio /mnt. Además, tampoco se deben volver a copiar al nuevo disco duro los directorios que ya tienen su propia partición.

De esta manera, se puede ejecutar el volcado del contenido del directorio raíz al nuevo disco duro usando el comando rsync, que permite excluir parte de los ficheros del origen: 

```bash
rsync -ra --exclude ‘boot’ --exclude ‘home’ --exclude ‘mnt’ --exclude ‘usr’ --exclude ‘var’ /* /mnt/destino/sistema/.
```

## Configuración del fichero fstab

El último paso para tener una réplica del volumen original en el nuevo disco duro de 30GB es configurar el fichero fstab del nuevo sistema, que está alojado en el directorio /mnt/destino/sistema/etc/fstab. En él se deben modificar los puntos de montaje de las particiones del volumen original por los puntos de montajes de las nuevas particiones del disco duro de 30GB. Esto se puede hacer redirigiendo la salida del comando `blkid` al fichero para guardar los UUID de todas las particiones y editándolo posteriormente en un editor de texto como nano. Con `blkid | grep vdb | cut -d “ “ -f 3` se consigue redirigir al fichero fstab, en primer lugar, sólo las líneas relativas a las particiones del nuevo disco de 30GB (vdb) y, además, sólo el campo necesario de cada partición: el UUID.

![](/assets/img/sistemas/practica10/img15.png)

## Actualización del arranque

Para actualizar el arranque del sistema en el nuevo disco se debe cambiar el directorio raíz del sistema al directorio del nuevo disco /mnt/destino/sistema. Antes de ejecutar el comando chroot es necesario montar los directorios del sistema /dev, /proc y /sys con la opción --bind.

```bash
mount --bind /dev /mnt/destino/sistema/dev/
mount --bind /proc /mnt/destino/sistema/proc/
mount --bind /sys /mnt/destino/sistema/sys/
```

Con `chroot /mnt` se cambia la raíz temporalmente al nuevo disco duro:

![](/assets/img/sistemas/practica10/img16.png)

Además, antes de poder usar el nuevo disco duro como sistema se debe actualizar el arranque con el comando `update-grub`.

Posteriormente, el comando `efibootmgr` permite actualizar las entradas del menú de arranque del firmware de la EFI.

En este punto de la instalación se puede apagar el equipo y modificar el orden de arranque en la interfaz de Proxmox para iniciar la máquina virtual con el nuevo disco duro. Al experimentar errores en el arranque y comprobar que el sistema no era capaz de arrancar desde el nuevo disco se buscan posibles inconsistencias en los ficheros de configuración del grub. Y, aunque en el fichero /boot/grub/grub.cfg la entrada del menú de arranque ya recoge el UUID del disco duro de 32GB tras la actualización de grub:

![](/assets/img/sistemas/practica10/img17.png)

En el fichero /boot/efi/EFI/debian/grub.cfg el UUID del dispositivo con el que arranca el equipo no se había actualizado y esto estaba provocando que el sistema arrancase desde el disco duro original.

![](/assets/img/sistemas/practica10/img18.png)

Tas actualizar esta línea del fichero, ahora la máquina arranca desde el disco duro de 32GB sin el disco original de 10GB.

![](/assets/img/sistemas/practica10/img19.png)