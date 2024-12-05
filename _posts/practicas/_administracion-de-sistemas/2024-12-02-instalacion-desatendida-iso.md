---
title:  "Instalación desatendida de Debian12"
date:   2024-11-21 01:00:00 +0200
categories: administracion-sistemas
tag: [sistemas, debian, linux, instalación, preseed, instalación desatendida, debian installer, administracion-sistemas, Administración de Sistemas Operativos]
excerpt: Aquí se recoge una breve guía para la creación de una iso instalable de Debian usando un fichero de preseed.
---
# Creación del fichero de preseed

Para configurar una instalación desatendida de Debian 12 se usa un fichero de configuración llamado preseed. Este fichero usa las diferentes órdenes del comando debian-installer para responder de forma automática a las preguntas que se muestran durante el proceso habitual de instalación del sistema operativo.

La distribución ofrece una [amplia documentación](https://www.debian.org/releases/bullseye/amd64/apb.es.html) sobre el procedimiento para llevar a cabo la instalación desatendida del sistema operativo. Además, también ofrece un [fichero de ejemplo](https://www.debian.org/releases/stable/example-preseed.txt) extensamente comentado que se puede usar como guía en el proceso de elaboración de un fichero personalizado.

Además, en el [punto 4](https://www.debian.org/releases/bullseye/amd64/apbs04.es.html) del apéndice del manual del administrador de Debian sobre instalación desatendida se desarrolla, punto por punto, cada una de las partes que debe contener este fichero.

Como se explica en la documentación oficial, el fichero de preseed siempre debe comenzar con la línea `#_preseed_V1`. A continuación se incluyen las diferentes órdenes que ejecuta durante el proceso de instalación el comando debian-installer para responder a las preguntas del instalador.

El primer segmento del documento configura el lenguaje, país y distribución de teclas del teclado.

```yaml
### Localization
# Selección del lenguale, país y configuración de "locale"
d-i debian-installer/locale string es_ES

# Selección del teclado
d-i keyboard-configuration/xkb-keymap select es
```

A continuación, se añaden las órdenes relacionadas con la configuración de red. Entre ellas, se establece la interfaz predeterminada para la instalación, se establece el nombre de host y de dominio de la máquina y se puede indicar que se cargue el firmware non-free si es necesario para detectar y configurar el hardware de red. Este opción puede ser necesaria para algunas tarjetas de red.

```yaml
### Network configuration
# Netcfg elige automáticamente una interfaz de red con conexión para evitar que 
# se muestre la lista de interfaces si hay más de una
d-i netcfg/choose_interface select auto

# Respuestas a las preguntas sobre nombre de host y dominio
d-i netcfg/get_hostname string deb-12
d-i netcfg/get_domain string gonzalonazareno.org

# Disable that annoying WEP key dialog.
d-i netcfg/wireless_wep string

# Intenta cargar el non-free firmware si es necesario para el hardware de red.
d-i hw-detect/load_firmware boolean true
```

El siguiente apartado del fichero configura el mirror que se utiliza posteriormente en el proceso de instalación para cargar la configuración de la paquetería del sistema a través de APT.

```yaml
### Mirror settings
# Mirror protocol (http por defecto):
d-i mirror/country string ES
d-i mirror/http/hostname string ftp.es.debian.org
d-i mirror/http/directory string /debian
d-i mirror/http/proxy string
```

Después se crean los usuarios en el equipo. Existen varias opciones de configuración análogas a las que se pueden establecer de manera interactiva durante la instalación del sistema. Por ejemplo, se puede indicar que no se cree un usuario root y el usuario normal tendrá privilegios a través de sudo. También se pueden crear una cuenta para root y otra par el usuario. En ambos casos la contraseña se puede indicar en texto claro o encriptada usando un crypt hash.

Para generar contraseña cifrada que se puede usar en el fichero de preseed se puede usar el siguiente comando:

```
mkpasswd -m sha-512
```

Así, un ejemplo de configuración de las cuentas de usuario en el sistema puede ser el siguiente:

```yaml
### Account setup
# Contraseña de root en texto claro:
#d-i passwd/root-password password r00tme
#d-i passwd/root-password-again password r00tme
# o encriptada usando un crypt(3)  hash.
d-i passwd/root-password-crypted password $6$IJQob.S9vlI2AuBC$Uc9b6E3/aqIH8RwP56KPxsp1PiZ1zZYakuafyzG.XXUS/ecQtStwtM.zwcQpUwWunEQHalP.J8q/sMnQ82Zrk.

# Para crear una cuenta de usuario
d-i passwd/user-fullname string debian
d-i passwd/username string debian
# La contraseña de usuario se puede indicar en texto claro
#d-i passwd/user-password password insecure
#d-i passwd/user-password-again password insecure
# o encriptada usando un crypt(3) hash.
d-i passwd/user-password-crypted password $6$SwwB64plkjSfzkWG$m0XWCN69GBiozd3gE.we2KFZVviFYif6L0LMF6ds3ZewvsLAWknH/beeSUDr.HbC4nbtaWdA2lnI7BSKly1VS1
```

A continuación se configuran también el reloj y la zona horaria

```yaml
### Clock and time zone setup
# Controla si el reloj de hardware se establece en UTC
d-i clock-setup/utc boolean true

# Este campo deve ser cualquier valor válido para $TZ; los valores válidos
# se pueden consultar en el directorio /usr/share/zoneinfo/
d-i time/zone string Europe/Madrid

# Controla el uso de NTP para establecer la hora del reloj durante la instalación
d-i clock-setup/ntp boolean true
```

Uno de los apartados más críticos del fichero de preseed es el del particionado del disco. Desde la configuración de este fichero se pueden elegir tres modos de particionado: el normal, que usa particiones del disco duro; el particionado con volúmenes lógicos, que usa LVM para particionar el disco; y el particionado encriptado, que genera particiones encriptadas.

En este caso, se usa la opción lvm para crear las particiones en un grupo de volúmenes.

En este fichero también se puede definir la cantidad de espacio usada por el grupo de volúmenes, en este caso, el máximo disponible.

Además, también se puede configurar la eliminación automática de los LVM y RAID que existan en el disco antes de la instalación del sistema operativo.

```yaml
### Partitioning

# Los modos de particionado disponibles actualmente son:
# - regular: usa los tipos de particiones habituales de la arquitectura
# - lvm:     usa LVM para particionar el disco
# - crypto:  usa LVM con particiones encriptadas
d-i partman-auto/method string lvm

# Crear un grupo de volúmenes
#d-i partman-auto-lvm/new_vg_name string vg1

# Se define la cantidad de espacio usada por el grupo de volúemenes. Puede ser
# un tamaño en unidades, un porcentaje del espacio libre o la plabra
# clave 'max'
d-i partman-auto-lvm/guided_size string max

# Eliminación automática de LVM y RAID si existen
# Si alguno de los discos que se van a particionar contiene una
# configuración LVM previa, el usuario recibiría un aviso. Esto lo evita
d-i partman-lvm/device_remove_lvm boolean true
# Lo mismo ocurre con los RAID preexistentes
d-i partman-md/device_remove_md boolean true
# Y lo mismo con la confirmación para escribir las particiones lvm
d-i partman-lvm/confirm boolean true
d-i partman-lvm/confirm_nooverwrite boolean true

# Esto hace que partman particione el disco automáticamente sin confirmación
# usando uno de los métodos anteriores
d-i partman-partitioning/confirm_write_new_label boolean true
d-i partman/choose_partition select finish
d-i partman/confirm boolean true
d-i partman/confirm_nooverwrite boolean true
d-i partman/ignore_warnings boolean true
```

El particionado del disco se define usando lo que debian-installer llama "recetas". Una receta es una guía que establece el número, tamaño y tipo de las particiones que se crean en el disco durante la instalación del sistema operativo.

Con el fichero de preseed de debian-installer se pueden usar cuatro opciones de particionado: la opción atomic genera una única partición en la que se incluyen todos los ficheros del sistema, la opción home crea una partición separada de la raíz para el directorio /home y la opción multi instala el sistema operativo con particiones separadas para los directorios /home, /var y /tmp. Adicionalmente, debian-installer ofrece la opción de indicar una receta personalizada para establecer un esquema de particionado definido por el usuario.

En el [repositorio de debian-installer](https://salsa.debian.org/installer-team/debian-installer/tree/master/doc/devel) hay disponible documentación adicional específica sobre el uso de las recetas personalizadas para el particionado.

En este caso, se define una partición efi de pequeño tamaño con sistema de ficheros fat32 que se monta en el directorio /boot/efi. Después se indica la creación de la partición /boot ya con sistema de ficheros ext4 y con un tamaño de 512M. Dentro del grupo de volúmenes se crean varios volúmenes lógicos para montar en ellos diferentes directorios del sistema. El menor tamaño se le asigna a la swap y el mayor tamaño se le asigna a la partición raíz. Con tamaños intermedios se crean también las particiones /home y /var.

```yaml
# Se puede elegir una de las tres recetas de particionado predefinidas:
# - atomic: todos los ficheros en una partición
# - home:   partición /home separada
# - multi:  particiones /home, /var y /tmp separadas
# o una receta de experto definida a continuación
d-i partman-auto/choose_recipe select bootlvm

# Receta para el particionado con LVM
d-i partman-auto/expert_recipe string                         \
bootlvm ::                                                    \
    256 256 256 fat32                                         \
            $primary{ } $bootable{ }                          \
            method{ format } format{ }                        \
            use_filesystem{ } filesystem{ fat32 }             \
            mountpoint{ /boot/efi }                           \
            set_flags{ boot }                                 \
    .                                                         \
    512 512 512 ext4                                          \
        $primary{ } $bootable{ }                              \
        method{ format } format{ }                            \
        use_filesystem{ } filesystem{ ext4 }                  \
        mountpoint{ /boot }                                   \
    .                                                         \
    256 256 512 linux-swap                                    \
        $defaultignore{ } $lvmok{ }                           \
        lv_name{ swap }                                       \
        in_vg { vg1 }                                         \
        method{ swap } format{ }                              \
    .                                                         \
    4096 10000 1000000 ext4                                   \
        $defaultignore{ } $lvmok{ }                           \
        lv_name{ raiz }                                       \
        in_vg { vg1 }                                         \
        method{ format } format{ }                            \
        use_filesystem{ } filesystem{ ext4 }                  \
        mountpoint{ / }                                       \
    .                                                         \
    1024 5000 10000000 ext4                                   \
        $defaultignore{ } $lvmok{ }                           \
        lv_name{ var }                                        \
        in_vg { vg1 }                                         \
        method{ format } format{ }                            \
        use_filesystem{ } filesystem{ ext4 }                  \
        mountpoint{ /var }                                    \
    .                                                         \
    1024 5000 10000000 ext4                                   \
        $defaultignore{ } $lvmok{ }                           \
        lv_name{ home }                                       \
        in_vg { vg1 }                                         \
        method{ format } format{ }                            \
        use_filesystem{ } filesystem{ ext4 }                  \
        mountpoint{ /home }                                   \
  .
```

Tras el particionado, se indican diferentes parámetros para la configuración del gestor de paquetes apt, que realiza en este punto de la instalación la primera instalación de la paquetería básica del equipo. Entre otros, se puede indicar si se deben escanear medios adicionales de instalación, si se debe instalar software que sea non-free o contrib o definir los servicios de actualización que se añadirán al fichero /etc/apt/sources.list.

```yaml
### Apt setup
# Elige si se escanean medios de instalación adicionales (por defecto: false)
d-i apt-setup/cdrom/set-first boolean false
# Descomenta esto para usar un mirror de red
d-i apt-setup/use_mirror boolean false
# Instalación de non-free firmware.
d-i apt-setup/non-free-firmware boolean true
# Instalación de non-free and contrib software.
d-i apt-setup/non-free boolean true
d-i apt-setup/contrib boolean true
# Descomenta esta línea si no quieres tener la entrada de la imagen de
# instalación de DVD/BD en sources.list activa en el sistema instalado
d-i apt-setup/disable-cdrom-entries boolean true
# Elige qué servicios de actualización usar; define los mirrors a usar
d-i apt-setup/services-select multiselect security, updates
d-i apt-setup/security_host string security.debian.org
```

Para terminar, se seleccionan los paquetes que instala tasksel al final del proceso de instalación del sistema operativo, se acepta o rechaza la participación en el concurso de popularidad de los paquetes de apt y se instala el gestor de arranque grub.

```yaml
### Package selection
tasksel tasksel/first multiselect standard, ssh-server
# Selección de la opción de participar en el concurso de popularidad de Debian
popularity-contest popularity-contest/participate boolean false

### Boot loader installation
# Instalación automática del grub en la partición UEFI si no hay otro sistema
# operativo detectado en la máquina
d-i grub-installer/only_debian boolean true

# Esto hace que grup-installer instale en la partición UEFI el grub aunque haya
# otro sistema operativo en la máquina, lo que es menos seguro porque puede
# hacer que no arranque el otro sistema operativo
d-i grub-installer/with_other_os boolean true
# Debido principalmente al uso de memorias USB, no siempre se puede determinar 
# con seguridad la unidad de disco principal, por lo que necesita especificar 
# lo siguiente:
#d-i grub-installer/bootdev  string /dev/sda
# Para instalar el cargador arranque en la unidad de disco principal 
# (asumiendo que no sea una memoria USB):
d-i grub-installer/bootdev string default

### Finishing up the installation
# Evita el último mensaje sobre la instalación completa
d-i finish-install/reboot_in_progress note
```

# Creación de la iso 

Para llevar esta configuración a la máquina hay que incluir el fichero de preseed en una imagen iso instalable junto al sistema operativo. Como punto de partida se puede usar la imagen de instalación del sistema operativo que se puede descargar desde su [página web oficial](https://www.debian.org/distrib/netinst). También se puede usar, si es el caso, una imagen iso modificada. En la [documentación de la herramienta debian-installer](https://wiki.debian.org/DebianInstaller/Preseed/EditIso) se recogen de manera detallada las instrucciones para llevar a cabo este procedimiento.

El primer paso es extraer el contenido de la imagen iso distribuida por Debian a un directorio del sistema para poder trabajar con sus contenidos. Esto se puede hacer de diferentes formas y usando diferentes herramientas. Una de ellas es `xorriso`.

```
sudo apt install xorriso
```

Esta herramienta permite trabajar de diferentes formas con ficheros de imagen iso. En este caso, se usa para extraer el contenido del fichero iso a un directorio del sistema de manera que se pueda navegar a través de él como se haría con cualquier otro esquema de directorios. Así, los contenidos del archivo de imagen se hacen accesibles y se pueden modificar para incluir en ellos la información necesaria para que el proceso de instalación se ejecute de forma automática.

```
xorriso -osirrox on -indev debian-12.1.0-amd64-netinst.iso -extract / ./iso
```

En este caso, el contenido de la imagen iso se extrae a un directorio llamado iso. Este directorio contiene todos los ficheros de la imagen iso distribuida por Debian. Para que el proceso de instalación se ejecute de forma desatendida, hay que incluir las órdenes del fichero de preseed en el fichero /install.amd/initrd de la iso. Se trata de un fichero comprimido así que hay que darle permisos de escritura y descomprimirlo. Después se vuelca el contenido del fichero de preseed al fichero initrd y, finalmente, se vuelve a comprimir. Antes de terminar, se le devuelven los permisos originales.

```
chmod +w -R iso/install.amd
gunzip iso/install.amd/initrd.gz
echo preseed.cfg | cpio -H newc -o -A -F iso/install.amd/initrd
gzip iso/install.amd/initrd
chmod -w -R iso/install.amd
```

Como se ha cambiado el contenido de un fichero de la imagen iso, el hash de verificación del fichero también será diferente, por tanto, es necesario regenerar el fichero md5sum.txt que verifica la integridad de los ficheros que componen la imagen iso. Para ello, desde el directorio de la iso se le otorgan permisos de escritura sobre el fichero al usuario, se genera un nuevo fichero con los hashes correspondientes a los ficheros actualizados y se devuelven los permisos originales.

```
cd iso/
chmod +w md5sum.txt
sudo rm md5sum.txt
sudo find -follow -type f ! -name md5sum.txt -print0 | xargs -0 md5sum > md5sum.txt
sudo chmod -w md5sum.txt
```

Durante la ejecución de este comando se muestra una advertencia que no afecta al proceso.

```
find: Se ha detectado un bucle en el sistema de ficheros; ‘./debian’ es parte del mismo bucle de sistema de ficheros que ‘.’.
```

El último paso es generar una nueva imagen iso que sea arrancable desde un equipo a partir del directorio iso con el que se ha estado trabajando. De nuevo existen múltiples herramientas para este propósito. En este caso, se usa genisoimage.

```
sudo apt install genisoimage
```

Esta herramienta permite generar un fichero de imagen arrancalbe de tipo iso a partir de un directorio.

```
sudo genisoimage -r -J -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -o preseed-debian-12.iso ../iso/
```

Este fichero de imagen ya contiene el conjunto de órdenes definido en el fichero de preseed, que va a hacer que la instalación del sistema operativo se lleve a cabo de principio a fin de forma destendida y sin requerir la interacción del usuario en ningún momento.

# Toques finales

Hay que tener en cuenta que esta configuración volcada al archivo de imagen iso usando el fichero de preseed sólo ejecuta la instalación desatendida del sistema operativo si se accede al instalador desde el modo "install" y no desde el modo de instalación gráfica. Una práctica interesante para evitar elegir un modo de instalación inadecuado accidentalmente puede ser editar el menú de arranque del instalador.

Este menú se define el en fichero /isolinux/menu.cfg (ruta absoluta desde la raíz del archivo de imagen iso). En él cada línea define una entrada del menú del instalador. Algunas de ellas llaman a otros ficheros de configuración en los que se define más detalladamente cada opción de instalación. 

Así, la línea `include gtk.cfg` llama al fichero en el que se describe la opción de instalación gráfica y la línea `include txt.cfg` llama al fichero en el que se describe la opción de instalación usando sólo el menú de texto. Esta opción es la que permite el correcto funcionamiento del procedimiento de instalación desatendida usando el fichero de preseed. 

Por tanto, una posible modificación del menú de la iso es la siguiente, en la que se eliminan todas las líneas que describen otras opciones de instalación como la instalación desatendida (a través de red), la instalación con audiodescripción o la instalación gráfica, que no permite que se ejecuten de forma automática las órdenes configuradas en el fichero de preseed.

```
menu hshift 4
menu width 70

menu title Debian GNU/Linux installer menu (BIOS mode)
include stdmenu.cfg
include txt.cfg
label help
	menu label ^Help
	text help
   Display help screens; type 'menu' at boot prompt to return to this menu
	endtext
	config prompt.cfg
```

Este menú muestra únicamente la línea "install", que ejecuta la instalación desatendida y la línea "help" que permite acceder a información y ayuda relevante sobre el sistema operativo y el instalador.

En una edición un poco más meticulosa de este fichero se pueden mantener todas las opciones compatibles con la instalación desatendida. En este caso se muestra una entrada para la opción de instalación, una entrada para el menú de alto contraste, que facilita la accesibilidad a personas con discapacidad visual y el menú de ayuda.

Para maximizar la compatibilidad con la configuración de instalación desatendida, el menú de alto contraste también cuenta únicamente con las opciones de instalación (compatible con la configuración esteblecida en el fichero de preseed) y ayuda.

Las opciones de instalación con audiodescripción no están presentes en este menú porque no son compatibles con la instalación desatendida configurada a través del fichero de preseed.

```
menu hshift 4
menu width 70

menu title Debian GNU/Linux Menu de instalacion desatendida (BIOS mode)
include stdmenu.cfg
include txt.cfg
menu begin dark
    menu label ^Menu de instalacion en alto contraste
	menu title Opciones de instalacion accesible
	include drkmenu.cfg
	label mainmenu
		menu label ^Atras..
		menu exit
	include drk.cfg
	label help
		menu label ^Ayuda
		text help
   Muestra la pantalla de ayuda; escribe 'menu' en el prompt para volver
		endtext
		config prompt.cfg
menu end
label help
	menu label ^Ayuda
	text help
   Muestra la pantalla de ayuda; escribe 'menu' en el prompt para volver
	endtext
	config prompt.cfg
```

La edición de este fichero permite también mostrar el menú del instalador traducido, como en este ejemplo anterior. Para que todas las opciones se muestren en castellano es también necesario traducir el texto de los ficheros txt.cfg y drk.cfg, que incluyen detalles de los menús de instalación.