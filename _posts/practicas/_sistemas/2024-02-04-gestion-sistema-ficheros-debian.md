---
title:  "Creación y configuración de un sistema de ficheros en Debian 12"
date:   2024-02-04 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos, sistema de ficheros, Implantación de Sistemas Operativos]
---
En este post se plantea un caso práctico en el que se emplean varios comandos para la configuración de sistemas de ficheros en Debian. En concreto, se trata de un ejemplo en el que se instala Debina 12 en una máquina con recursos limitados en VirtManager y, posteriormente, se amplía el espacio de almacenamiento añadiendo un nuevo disco duro y clonando el sistema en él.

><i class="fas fa-lightbulb" aria-hidden="true"></i> Para este post se usan qemu/kvm y VirtManager como software de virtualización. Aquí te explico [cómo instalarlo en Debian](/sistemas/instalar-qemu-kvm-virtualizacion-debian).
{: .notice--primary}

# Particionado de los discos

## Disco de origen

Durante el proceso de instalación de Debian12 en la máquina virtual se definen las siguientes particiones:

- Partición efi: 50MB.
- Partición /: 2GB.
- Swap: el resto de espacio sobrante.

## Disco de destino

Para crear un nuevo disco duro en VirtManager es necesario acceder a la ventana “añadir hardware” desde la configuración de la máquina virtual. A continuación, se debe indicar el tamaño del dispositivo y, por último, pulsar el botón de crear. Tras seguir estos pasos, el nuevo disco duro se habrá añadido a la máquina virtual y ya estará disponible en la lista de dispositivos que aparece en la ventana de configuración.

Al listar los dispositivos de bloque con el comando `lsblk -f` también deberían aparecer en la máquina tanto el disco duro original (vda), de 2GB, como el segundo disco duro (vdb), de 10GB.

Para crear el particionado del disco duro en Debian se pueden usar aplicaciones como `fdisk` o `gdisk`. En este caso se ha optado por el uso del comando `fdisk`. En primer lugar, se debe establecer una nueva tabla de particiones GPT vacía con el comando `g`. 

![](/assets/img/sistemas/practica9/img1.png)

Posteriormente, para cada partición, se crea, se le asigna un número, primer y último sector (tamaño).

![](/assets/img/sistemas/practica9/img2.png)

Tras crear todas las particiones necesarias en el disco duro, el comando `fdisk` permite también asignarles un tipo determinado hasta llegar a tener una tabla de particiones como la siguiente:

![](/assets/img/sistemas/practica9/img3.png)

# Creación del sistema de ficheros

Para asignar un sistema de ficheros a cada partición se usa el comando `mkfs` seguido de `.fs` donde "fs" es el sistema de ficheros que se busca asignar a cada partición. La primera de las particiones listadas anteriormente usará un sistema de ficheros de tipo vfat y el resto uno de tipo ext4. Para poder asignar el sistema de ficheros FAT32 a la partición de tipo EFI se debe instalar previamente el paquete `dosfstools`.

Tras la instalación de este paquete se pueden usar los comandos `mkfs.vfat` y `mkfs.ext4` para dar formato a cada una de las particiones. Por último, para darle un sistema de ficheros de tipo swap a la última partición del nuevo disco duro se puede usar el comando `mkswap`.

```bash
mkfs.vfat /deb/vdb1
mkfs.ext4 /deb/vdb2
mkfs.ext4 /deb/vdb3
mkfs.ext4 /deb/vdb4
mkfs.ext4 /deb/vdb5
mkfs.ext4 /deb/vdb6
mkswap /dev/vdb7
```

# Montaje del nuevo disco

El primer paso para montar el nuevo disco en la máquina es crear un punto de montaje. Para ello, se usará el directorio /mnt y en él, se habilitarán nuevos directorios con `mkdir` que sirvan de punto de montaje para cada una de las particiones del nuevo disco duro.

```bash
mkdir -p /mnt/vdb/vdb{1..7}
```

A continuación, se pueden montar las diferentes particiones sobre cada uno de sus nuevos puntos de montaje y también se puede habilitar la nueva partición swap en el equipo.

```bash
mount /dev/vdb1 /mnt/vdb/vdb1
mount /dev/vdb2 /mnt/vdb/vdb2
mount /dev/vdb3 /mnt/vdb/vdb3
mount /dev/vdb4 /mnt/vdb/vdb4
mount /dev/vdb5 /mnt/vdb/vdb5
mount /dev/vdb6 /mnt/vdb/vdb6
swapon /dev/vdb7
```

En este punto, todas las particiones del nuevo disco duro están montadas en sus respectivos puntos de montaje y funcionando con el sistema de ficheros adecuado que se le ha asignado previamente durante este proceso.

![](/assets/img/sistemas/practica9/img4.png)

# Volcado de información al nuevo disco

Una vez que el nuevo disco está funcionando en la máquina, se debe trasladar la información a sus nuevas ubicaciones. Para ello se usará el comando cp para copiar los contenidos de los diferentes directorios del disco original (vda), de 2GB al nuevo disco (vdb) de 10GB.

## Directorio /home

El primer directorio que se trasladará al nuevo disco será el directorio /home. Para ello, el primer paso debe ser copiar el contenido del directorio /home del disco duro original en la partición 4 del nuevo disco duro.

```bash
cp -r /home/* /mnt/vdb/vdb4
```

A continuación, se habilitará como punto de montaje de esta partición el directorio /home del sistema en el fichero de configuración /etc/fstab con el formato

```
PARTLABEL="/home"   /home   ext4    defaults    0   0
```

Adicionalmente, se puede copiar el contenido de este directorio en el disco duro original con el nombre /home.old para garantizar que no se pierde esta información.

```bash
cp -r /home /home.old
```

## Directorio /var

Para volcar la información del directorio /var al nuevo disco se debe seguir el mismo procedimiento. De esta manera, en primer lugar se copiará la información del directorio a la partición del disco que se montará a continuación en este punto, en este caso la partición 5.

```bash
cp -r /var/* /mnt/vdb/vdb5
```

El siguiente paso es añadir este nuevo punto de montaje al fichero de configuración /etc/fstab.

## Directorio /usr

De la misma forma que en los casos anteriores, para volcar la información de este directorio al nuevo disco se usa el comando `cp`.

```bash
cp -r /usr/* /mnt/vdb/vdb6
```

A continuación, se configura este nuevo punto de montaje en el fichero /etc/fstab.

## Partición EFI

Una vez que todos los directorios están copiados y montados en sus nuevos puntos de montaje, se debe repetir la misma operación con la EFI, el directorio de arranque /boot, en el que está el kernel del sistema operativo y el resto de contenidos del directorio raíz.

Para trasladar la EFI a su nuevo disco, se procede de la misma forma. La información de arranque del sistema se encuentra en el directorio /boot/efi, por lo que será éste el que se copie a la partición 1 del nuevo disco duro.

```bash
cp -r /boot/efi/* /mnt/vdb/vdb1
```

Y, al igual que en los casos anteriores, se indicará el nuevo punto de montaje de la EFI en el fichero de configuración /etc/fstab. En este caso, además para evitar duplicidades con el punto de montaje, se debe comentar la línea correspondiente a la partición EFI del anterior disco duro.

## Directorio /boot

La siguiente modificación que se puede aplicar es la del directorio /boot. Para ello se procederá como con el resto de directorios que ya se han trasladado con éxito al nuevo disco duro.

En primer lugar se copia el contenido del directorio a la partición correspondiente del nuevo disco.

```bash
cp -r /boot/* /mnt/vdb/vdb2
```

Y, a continuación, se indica el nuevo punto de montaje en el fichero de configuración fstab.

## Directorio raíz

En este punto llega el momento de trasladar todo el resto del sistema al nuevo disco duro.  Para esta tarea se puede usar la opción `-t` del comando `cp`, que copia todos los ficheros indicados como origen en un directorio marcado como destino con esa opción. Pero durante el proceso de copia de todos estos directorios, que suponen un gran volumen de información, el comando `cp` devuelve los siguientes mensajes de error debido a la falta de espacio en el dispositivo.

Se da una situación llamativa puesto que el espacio del directorio raíz completo en el disco original es de sólo 1,8GB, mientras que en el nuevo disco duro parece que ya se han ocupado los 3,4GB destinados a esta partición.

![](/assets/img/sistemas/practica9/img5.png)

Para solucionar este problema, se puede arrancar la máquina en modo recuperación con sistema operativo live y, desde el intérprete de comandos, montar, por una parte, el directorio raíz del antiguo disco duro, en este caso en el directorio /mnt/vda/vda3, y, por otra parte, la partición del nuevo disco duro donde se quiere trasladar esa información, en este caso, en el directorio /mnt/vdb/vdb3. Con ambas particiones montadas en el directorio /mnt del sistema operativo live, ya se puede ejecutar el comando de copia como se muestra en la siguiente captura de pantalla.

```bash
cp -r /mnt/vda/vda3/bin/ /mnt/vda/vda3/dev/ /mnt/vda/vda3/etc/ /mnt/vda/vda3/initrd.img /mnt/vda/vda3/initrd.img.old /mnt/vda/vda3/lib/ /mnt/vda/vda3/lib32/ /mnt/vda/vda3/lib64/ /mnt/vda/vda3/libx32/ /mnt/vda/vda3/lost+found/ /mnt/vda/vda3/media/ /mnt/vda/vda3/mnt/ /mnt/vda/vda3/opt/ /mnt/vda/vda3/proc/ /mnt/vda/vda3/root/ /mnt/vda/vda3/run/ /mnt/vda/vda3/sbin/ /mnt/vda/vda3/srv/ /mnt/vda/vda3/sys/ /mnt/vda/vda3/tmp/ /mnt/vda/vda3/vmlinuz /mnt/vda/vda3/vmlinuz.old -t /mnt/vdb/vdb3
```

Tras esta copia, todo el contenido del directorio raíz del disco duro original se ha copiado en la partición del nuevo disco duro que debe albergar todo ese contenido, excepto los directorios que ya se han movido a sus correspondientes nuevas particiones.

En este caso, también resulta llamativa la diferencia de tamaño entre el directorio raíz del disco duro original y el directorio raíz del nuevo disco duro: 1,7GB frente a sólo 3,8MB. Esta diferencia se puede explicar, en parte, porque en este nuevo directorio raíz ya no están directorios como el /var o el /usr, que contienen una parte importante de la información del sistema.

![](/assets/img/sistemas/practica9/img6.png)

El sistema operativo live puede ser un buen entorno para terminar de conformar el nuevo fichero de configuración /etc/fstab en este segundo disco duro antes de volver a arrancar la máquina.

Si tenemos en cuenta que este debería ser el nuevo fichero fstab del equipo, cabe eliminar las líneas que dirigían a particiones del anterior disco duro e indicar, exclusivamente, en qué punto del sistema se deben montar las nuevas particiones que, en este punto de la instalación, deberían contener ya toda la información para el funcionando del equipo.

![](/assets/img/sistemas/practica9/img7.png)

# Arrancar con el nuevo disco duro y resolución de problemas

Para el siguiente arranque de la máquina se configura el nuevo disco duro como dispositivo de arranque pero, al intentar arrancar la máquina sin el disco original, el equipo no consigue comenzar a funcionar.

![](/assets/img/sistemas/practica9/img8.png)

Tras comprobar que todos los ficheros y directorios del sistema se han copiado correctamente a sus particiones de destino se encuentran dos errores importantes para el funcionamiento del sistema.

En primer lugar, durante el proceso de volcado de la información al nuevo disco duro no se habían creado los directorios /boot, /home, /var y /usr en el directorio raiz y, por tanto, estas particiones no tenían un punto en el que montarse. Para crear estos directorios que servirán como punto de montaje a las diferentes particiones se puede montar el dispositivo de bloques en el entorno del instalador de un sistema operativo live.

![](/assets/img/sistemas/practica9/img9.png)

Se debe montar la partición que contiene la raiz, en este caso /dev/vdb3 y, a continuación, crear en ella los directorios indicados anteriormente.

```bash
mkdir -p /mnt/vdb/vdb3/boot
mkdir -p /mnt/vdb/vdb3/home
mkdir -p /mnt/vdb/vdb3/var
mkdir -p /mnt/vdb/vdb3/usr
```

Tras haber creado estos directorios en el sistema del nuevo disco duro, ya se puede ejecutar este sistema de archivos desde una terminal en el sistema operativo live. Pero al consultar los dispositivos de bloque montados en el sistema llama la atención que en el punto de montaje /boot/efi no se monta la partición del nuevo disco duro, sino la del disco original de 2GB.

![](/assets/img/sistemas/practica9/img10.png)

Esto lleva a comprobar el fichero de configuración /etc/fstab en el que se indican los puntos de montaje de cada una de las partes del sistema. Parece que todas las particiones están correctamente configuradas, en concreto, la partición EFI no presenta ningún error que llame la atención en un primer momento y, como se puede ver, la partición del disco duro original está comentada, por lo que no se debería montar.

Un forma alternativa de montar la partición puede ser referirse a ella por UUID en vez de usar el nombre del partlabel. Pero, al consultar este identificador, se descubre cuál es el problema que estaba haciendo que se montase la partición EFI del disco vda, en vez de la del disco vdb: las etiquetas partlabel se habían intercambiado en el fichero fstab. Como muestra la salida del comando `blkid`, la etiqueta de la partición EFI nueva es “UEFI” y no “efi”, como indicaba el fichero fstab y que, en realidad, estaba indicando que se debía montar la partición EFI del dispositivo de bloques /dev/vda.

![](/assets/img/sistemas/practica9/img11.png)

Tras localizar el error, la solución es tan sencilla como cambiar la etiqueta de la partición en el fichero de configuración.

![](/assets/img/sistemas/practica9/img12.png)

><i class="fas fa-exclamation-circle" aria-hidden="true"></i> Es recomendable extremar la precaución a la hora de usar el campo PARTLABEL para identificar particiones o usar métodos alternativos como el identificador únio UUID para evitar este tipo de errores.
{: .notice--warning}

Una vez hecho esto, se debe reinstalar el cargador de arranque grub en el nuevo sistema. En este caso, se puede hacer desde la interfaz gráfica del modo rescate del sistema operativo live.

![](/assets/img/sistemas/practica9/img13.png)

Tras realizar estos pasos, se puede comprobar que, al reiniciar el equipo el sistema ya lanza el grub con las diferentes opciones de arranque.

![](/assets/img/sistemas/practica9/img14.png)

Además, si se accede a la configuración del firmware de la UEFI ya aparece como primera opción en el menú de arranque el sistema operativo que se ha configurado a lo largo de esta práctica en el nuevo disco duro. Pero este sistema aún no permite el arranque. Al lanzarlo se muestra un error que indica que no se encuentra uno de los dispositivos y tampoco localiza uno de los ficheros que debería estar ubicado en el directorio /boot.

![](/assets/img/sistemas/practica9/img15.png)

Para localizar el dispositivo que está resultando problemático se puede ejecutar el sistema usando un sistema operativo live y, una vez que se esté trabajando con él se puede buscar a qué dispositivo hace referencia ese UUID.

![](/assets/img/sistemas/practica9/img16.png)

En concreto, se trata del disco /dev/vda3, que es la partición del disco original que contiene el sistema. Este disco no debería montarse durante el arranque, puesto que debería producirse con el nuevo disco configurado.

En segundo lugar, el fichero que no se ha podido encontrar debería estar en la partición que contiene el directorio boot en el nuevo disco duro. Al montar esta partición en un entorno de instalador y listar su contenido, el fichero no encontrado aparece ubicado correctamente en la partición del disco duro.

![](/assets/img/sistemas/practica9/img17.png)

También en la partición que contiene la raíz se encuentra el enlace que apunta al fichero que no se había encontrado en el directorio /boot.

![](/assets/img/sistemas/practica9/img18.png)

Para intentar solucionar estos errores, se puede intentar reinstalar el grub desde la terminal que proporciona este sistema operativo live con el sistema del nuevo disco duro arrancado.

```bash
grub-install
```

![](/assets/img/sistemas/practica9/img19.png)

Durante el proceso se indica que el sistema no soporta las variables EFI. Para solventar este problema se deben cargar estas variables.

```bash
mount -t efivarfs none /sys/firmware/efi/efivars/
```

En este caso, el grub se instala sin devolver ninguna advertencia.

El siguiente paso necesario tras la instalación del grub es su actualización. Durante este proceso, parece que el grub ha conseguido encontrar los ficheros que, en el anterior inicio no se habían podido localizar.

```bash
update-grub
```

Finalmente, después de haber reinstalado y actualizado el grub a través de la terminal, el nuevo sistema ha arrancado en el siguiente reinicio sin ningún problema y se han montado correctamente todas las particiones en los puntos de montaje indicados.

En este punto, el sistema se ha trasladado completamente al nuevo disco duro, que ya ofrece un sistema 100% funcional con el espacio de almacenamiento más ampliado.

![](/assets/img/sistemas/practica9/img20.png)