---
title:  "Manejo y modificación de módulos del kernel "
date:   2024-11-12 01:00:00 +0200
categories: administracion-sistemas
tag: [sistemas, debian, linux, kernel, módulos, sysctl, comandos, administracion-sistemas, Administración de Sistemas Operativos]
excerpt: Aquí se recogen algunos ejercicios en los que se ponen en práctica diferentes herramientas para trabajar con los módulos del kernel de Linux.
---

# Ejercicios de manejo de módulos

## 1. Comprueba los módulos cargados en tu equipo

Los módulos cargados en el equipo se pueden listar con el comando `lsmod`.

```
lsmod
Module                  Size  Used by
ip6t_REJECT            16384  6
nf_reject_ipv6         20480  1 ip6t_REJECT
rpcsec_gss_krb5        36864  0
nfsv4                1052672  0
dns_resolver           16384  1 nfsv4
nfs                   520192  1 nfsv4
fscache               380928  1 nfs
netfs                  57344  1 fscache
veth                   36864  0
nft_masq               16384  1
vhost_net              36864  9
vhost                  57344  1 vhost_net
vhost_iotlb            16384  1 vhost
tap                    28672  1 vhost_net
tun                    61440  19 vhost_net
snd_seq_dummy          16384  0
snd_hrtimer            16384  1
snd_seq                90112  7 snd_seq_dummy
snd_seq_device         16384  1 snd_seq
xt_CHECKSUM            16384  5
xt_MASQUERADE          20480  12
xt_conntrack           16384  4
ipt_REJECT             16384  18
nf_reject_ipv4         16384  1 ipt_REJECT
xt_tcpudp              20480  0
nft_compat             20480  45
nft_chain_nat          16384  3
nf_nat                 57344  3 nft_masq,nft_chain_nat,xt_MASQUERADE
nf_conntrack          188416  4 xt_conntrack,nf_nat,nft_masq,xt_MASQUERADE
nf_defrag_ipv6         24576  1 nf_conntrack
nf_defrag_ipv4         16384  1 nf_conntrack
nf_tables             303104  983 nft_compat,nft_masq,nft_chain_nat
nfnetlink              20480  2 nft_compat,nf_tables
qrtr                   49152  2
bridge                311296  0
stp                    16384  1 bridge
llc                    16384  2 bridge,stp
binfmt_misc            28672  1
intel_rapl_msr         20480  0
intel_rapl_common      32768  1 intel_rapl_msr
nls_ascii              16384  1
nls_cp437              20480  1
x86_pkg_temp_thermal    20480  0
intel_powerclamp       20480  0
vfat                   24576  1
fat                    90112  1 vfat
coretemp               20480  0
kvm_intel             380928  6
kvm                  1146880  1 kvm_intel
irqbypass              16384  27 kvm
ghash_clmulni_intel    16384  0
ath9k                 143360  0
cryptd                 28672  1 ghash_clmulni_intel
ath9k_common           24576  1 ath9k
sha512_ssse3           49152  0
sha512_generic         16384  1 sha512_ssse3
ath9k_hw              512000  2 ath9k_common,ath9k
snd_hda_codec_realtek   172032  1
snd_hda_codec_generic    98304  1 snd_hda_codec_realtek
ledtrig_audio          16384  1 snd_hda_codec_generic
ath                    36864  3 ath9k_common,ath9k,ath9k_hw
mac80211             1175552  1 ath9k
snd_hda_codec_hdmi     81920  1
sha256_ssse3           32768  1
libarc4                16384  1 mac80211
snd_hda_intel          57344  2
snd_intel_dspcfg       36864  1 snd_hda_intel
snd_intel_sdw_acpi     20480  1 snd_intel_dspcfg
snd_hda_codec         184320  4 snd_hda_codec_generic,snd_hda_codec_hdmi,snd_hda_intel,snd_hda_codec_realtek
sha1_ssse3             32768  0
cfg80211             1146880  4 ath9k_common,ath9k,ath,mac80211
pcspkr                 16384  0
rapl                   20480  0
snd_hda_core          122880  5 snd_hda_codec_generic,snd_hda_codec_hdmi,snd_hda_intel,snd_hda_codec,snd_hda_codec_realtek
iTCO_wdt               16384  0
intel_pmc_bxt          16384  1 iTCO_wdt
snd_hwdep              16384  1 snd_hda_codec
snd_pcm               159744  4 snd_hda_codec_hdmi,snd_hda_intel,snd_hda_codec,snd_hda_core
rfkill                 36864  6 ath9k,cfg80211
mei_hdcp               24576  0
snd_timer              49152  3 snd_seq,snd_hrtimer,snd_pcm
iTCO_vendor_support    16384  1 iTCO_wdt
intel_cstate           20480  0
snd                   126976  16 snd_hda_codec_generic,snd_seq,snd_seq_device,snd_hda_codec_hdmi,snd_hwdep,snd_hda_intel,snd_hda_codec,snd_hda_codec_realtek,snd_timer,snd_pcm
soundcore              16384  1 snd
watchdog               45056  1 iTCO_wdt
intel_uncore          217088  0
at24                   28672  0
sg                     40960  0
mei_me                 53248  1
evdev                  28672  11
mei                   159744  3 mei_hdcp,mei_me
nfsd                  704512  5
auth_rpcgss           159744  2 nfsd,rpcsec_gss_krb5
msr                    16384  0
nfs_acl                16384  1 nfsd
lockd                 131072  2 nfsd,nfs
grace                  16384  2 nfsd,lockd
sunrpc                692224  21 nfsd,nfsv4,auth_rpcgss,lockd,rpcsec_gss_krb5,nfs_acl,nfs
parport_pc             40960  1
ppdev                  24576  0
efi_pstore             16384  0
lp                     20480  0
parport                69632  3 parport_pc,lp,ppdev
fuse                  176128  7
configfs               57344  1
loop                   32768  0
ip_tables              36864  0
x_tables               61440  8 xt_conntrack,nft_compat,xt_tcpudp,xt_CHECKSUM,ipt_REJECT,ip_tables,xt_MASQUERADE,ip6t_REJECT
autofs4                53248  2
xfs                  1957888  3
btrfs                1794048  0
zstd_compress         294912  1 btrfs
efivarfs               24576  1
raid10                 65536  0
raid456               180224  0
async_raid6_recov      24576  1 raid456
async_memcpy           20480  2 raid456,async_raid6_recov
async_pq               20480  2 raid456,async_raid6_recov
async_xor              20480  3 async_pq,raid456,async_raid6_recov
async_tx               20480  5 async_pq,async_memcpy,async_xor,raid456,async_raid6_recov
xor                    24576  2 async_xor,btrfs
raid6_pq              122880  4 async_pq,btrfs,raid456,async_raid6_recov
libcrc32c              16384  6 nf_conntrack,nf_nat,btrfs,nf_tables,xfs,raid456
crc32c_generic         16384  0
raid1                  53248  0
raid0                  24576  0
multipath              20480  0
linear                 20480  0
md_mod                192512  6 raid1,raid10,raid0,linear,raid456,multipath
dm_mod                184320  6
hid_generic            16384  0
usbhid                 65536  0
hid                   159744  2 usbhid,hid_generic
i915                 3055616  73
sd_mod                 65536  4
t10_pi                 16384  1 sd_mod
crc64_rocksoft         20480  1 t10_pi
crc64                  20480  1 crc64_rocksoft
crc_t10dif             20480  1 t10_pi
crct10dif_generic      16384  0
drm_buddy              20480  1 i915
i2c_algo_bit           16384  1 i915
drm_display_helper    184320  1 i915
cec                    61440  2 drm_display_helper,i915
ahci                   49152  3
rc_core                69632  1 cec
libahci                49152  1 ahci
ttm                    94208  1 i915
libata                401408  2 libahci,ahci
drm_kms_helper        212992  2 drm_display_helper,i915
xhci_pci               24576  0
xhci_hcd              315392  1 xhci_pci
scsi_mod              286720  3 sd_mod,libata,sg
crct10dif_pclmul       16384  1
crct10dif_common       16384  3 crct10dif_generic,crc_t10dif,crct10dif_pclmul
ehci_pci               20480  0
ehci_hcd              102400  1 ehci_pci
drm                   614400  18 drm_kms_helper,drm_display_helper,drm_buddy,i915,ttm
i2c_i801               36864  0
i2c_smbus              20480  1 i2c_i801
crc32_pclmul           16384  0
scsi_common            16384  3 scsi_mod,libata,sg
r8169                  94208  0
lpc_ich                28672  0
realtek                36864  1
crc32c_intel           24576  1
mdio_devres            16384  1 r8169
usbcore               348160  5 xhci_hcd,ehci_pci,usbhid,ehci_hcd,xhci_pci
libphy                180224  3 r8169,mdio_devres,realtek
usb_common             16384  3 xhci_hcd,usbcore,ehci_hcd
fan                    20480  0
video                  65536  1 i915
wmi                    36864  1 video
button                 24576  0
```

## 2. Cuenta el número de módulos disponibles en el núcleo que estás usando

```
sudo find /lib/modules/$(uname -r) -type f -iname '*.ko' | wc -l
4024
```

En el kernel instalado en el equipo hay disponibles un total de 4024 módulos.

## 3. Conecta un lápiz USB y observa la salida de la instrucción sudo dmesg

Al conectar un dispoisitivo USB al equipo, se cargan en memoria los módulos del kernel que contienen los drivers necesarios para gestionar el dispositivo. En este caso, se observa en la salida del comando `sudo dmesg` cómo se cargan los módulos `usb 2-1`, `usb-storage`, `scsi host4` o `usbcore`. Además, se muestran algunos de los pocesos relacionados con la detección, identificación y montaje del nuevo dispositivo.

```
sudo dmesg
...
[14561.684067] usb 2-1: new high-speed USB device number 5 using xhci_hcd
[14562.325600] usb 2-1: New USB device found, idVendor=125f, idProduct=1036, bcdDevice= 1.00
[14562.325605] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[14562.325607] usb 2-1: Product: USB Flash Drive
[14562.325609] usb 2-1: Manufacturer: USB 2.0
[14562.325610] usb 2-1: SerialNumber: 9701b097f311bd
[14563.248344] usb-storage 2-1:1.0: USB Mass Storage device detected
[14563.249769] scsi host4: usb-storage 2-1:1.0
[14563.251217] usbcore: registered new interface driver usb-storage
[14563.358101] usbcore: registered new interface driver uas
[14564.280602] scsi 4:0:0:0: Direct-Access     USB 2.0  USB Flash Drive  0.00 PQ: 0 ANSI: 2
[14564.281193] sd 4:0:0:0: Attached scsi generic sg2 type 0
[14564.281424] sd 4:0:0:0: [sdb] 7897088 512-byte logical blocks: (4.04 GB/3.77 GiB)
[14564.281802] sd 4:0:0:0: [sdb] Write Protect is off
[14564.281807] sd 4:0:0:0: [sdb] Mode Sense: 00 00 00 00
[14564.282150] sd 4:0:0:0: [sdb] Asking for cache data failed
[14564.282156] sd 4:0:0:0: [sdb] Assuming drive cache: write through
[14564.350308]  sdb:
[14564.419487] sd 4:0:0:0: [sdb] Attached SCSI removable disk
[14569.357025] FAT-fs (sdb): Volume was not properly unmounted. Some data may be corrupt. Please run fsck.
```

## 4. Elimina el módulo correspondiente a algún dispotivo no esencial y comprueba qué ocurre. Vuelve a cargarlo

Cuando se descarga de memoria el módulo `ath9k`, que está relacionado con los drivers de la tarjeta de red inalámbrica.

```
sudo modprobe -r ath9k
```

La ejecución del comando `sudo dmesg` muestra la siguiente línea:

```
[17980.371067] ath9k: ath9k: Driver unloaded
```

Si se vuelve a cargar el módulo en memoria

```
sudo modprobe ath9k
```

Se vuelve a cargar la información relacionada con la tarjeta de red inalámbrica en el sistema.

```
[18041.354113] ath: EEPROM regdomain: 0x809c
[18041.354117] ath: EEPROM indicates we should expect a country code
[18041.354118] ath: doing EEPROM country->regdmn map search
[18041.354118] ath: country maps to regdmn code: 0x52
[18041.354119] ath: Country alpha2 being used: CN
[18041.354120] ath: Regpair used: 0x52
[18041.355354] ieee80211 phy1: Selected rate control algorithm 'minstrel_ht'
[18041.379127] ieee80211 phy1: Atheros AR9287 Rev:2 mem=0x000000009cd2f86f, irq=17
[18041.579207] ath9k 0000:04:00.0 wlp4s0: renamed from wlan0
```

## 5. Selecciona un módulo que esté en uso en tu equipo y configura el arranque para que no se cargue automáticamente

Una forma de configurar el kernel para que no cargue automáticamente un módulo en el arranque es usando el directorio /etc/modprobe.d/\<modulo\>.conf. Por ejemplo, en el caso del módulo `ath9k` habría que crea el fichero /etc/modprobe.d/ath9k.conf y en él añadir la siguiente línea:

```
blacklist ath9k
```

Realmente, el nombe del fichero dentro del directorio /etc/modprobe.d/ no es relevante. De esta manera, si hay una lista más o menos amplia de módulos cuya carga automática en el arranque se quiere evitar se puede crear un fichero /etc/modprobe.d/blacklis.conf y en él añadir una línea con el contenido `blacklist <módulo>` por cada uno de los módulos a los que se quiera aplicar esta configuración.

## 6. Carga el módulo loop, obtén información de qué es y para qué sirve. Lista el contenido de /sys/modules/loop/parameters y configura el equipo para que se puedan cargar como máximo 12 dispositvos loop la próxima vez que se arranque

Tras cargar el módulo loop con el comando `modprobe` se puede comprobar que, efectivamente, este módulo está cargado con el comando `lsmod`.

```
sudo modprobe loop
sudo lsmod | grep loop
loop                   32768  0
```

El comando `modinfo` devuelve cierta infomación sobre el módulo `loop`. Se trata del módulo que gestiona los dispositivos de tipo loop en el sistema. Este módulo no tiene dependencias y tiene tres parámetros: `max_loop`, que indica el número máximo de dispositivos de tipo loop que se pueden habilitar en el equipo, `max_part`, que indica el número máximo de particiones que puede tener cada uno de estos dispositivos y `hw_queue_depth`, que indica la profundida de la cola de cada dispositivo y que, por defecto, tiene un valor de 128.

```
sudo modinfo loop
filename:       /lib/modules/6.1.0-26-amd64/kernel/drivers/block/loop.ko
alias:          devname:loop-control
alias:          char-major-10-237
alias:          block-major-7-*
license:        GPL
depends:
retpoline:      Y
intree:         Y
name:           loop
vermagic:       6.1.0-26-amd64 SMP preempt mod_unload modversions
sig_id:         PKCS#7
signer:         Debian Secure Boot CA
sig_key:        32:A0:28:7F:84:1A:03:6F:A3:93:C1:E0:65:C4:3A:E6:B2:42:26:43
sig_hashalgo:   sha256
signature:      9A:13:9A:8A:39:48:35:B1:0C:B1:32:B9:F6:58:16:95:98:7C:1A:8F:
		37:7A:44:CB:6B:B6:8D:80:B6:60:83:B3:4B:9E:0F:AB:62:50:12:0A:
		5F:4D:FB:56:7F:02:DA:3E:93:7F:5C:EB:9A:7F:E7:DB:37:BD:BC:2F:
		47:78:64:8C:52:F0:EE:DE:8A:2E:56:FB:6F:64:40:EC:95:FB:41:76:
		AE:28:8F:61:39:F9:20:7B:F3:EA:B5:D7:4A:11:DA:BD:CF:A1:33:1D:
		2B:9D:F8:99:1F:12:09:6F:1F:B2:32:79:E4:A6:2C:3A:09:B7:9B:02:
		13:25:0D:0F:71:38:4D:2B:31:10:78:4E:9E:0B:03:8C:6A:1D:93:FB:
		1F:2F:DC:35:CF:10:26:C2:40:E0:CA:50:D2:1F:93:EA:8A:5C:48:42:
		E7:D5:B3:F7:7C:15:95:49:B3:13:E6:89:D6:E9:44:C3:6D:BF:AF:CF:
		6F:A2:72:18:2C:69:6E:60:AA:E6:1A:5E:B7:D2:AC:68:D6:4D:66:8B:
		06:2E:E3:F0:D1:C5:CF:9C:7C:69:FD:5E:3A:F0:64:CE:6C:CF:88:F1:
		62:AB:9B:B8:6D:B9:7A:87:84:7D:0A:4A:AF:CC:F3:E1:4D:15:C2:6A:
		43:98:8D:58:68:5A:36:5E:03:EF:0F:60:46:AC:57:C3
parm:           max_loop:Maximum number of loop devices
parm:           max_part:Maximum number of partitions per loop device (int)
parm:           hw_queue_depth:Queue depth for each hardware queue. Default: 128
```

Estos parámetros también se pueden acceder listando el contenido del dierctorio /sys/module/loop/parameters/.

```
sudo ls -l /sys/module/loop/parameters/
total 0
-r--r--r-- 1 root root 4096 oct 24 14:23 hw_queue_depth
-r--r--r-- 1 root root 4096 oct 24 14:23 max_loop
-r--r--r-- 1 root root 4096 oct 24 14:23 max_part
```

El parámetro `max_loop` del módulo loop define la cantidad de dispositivos de tipo loop que se pueden montar de forma simultánea en el equipo. En este caso, tiene un valor de 8.

```
sudo cat /sys/module/loop/parameters/max_loop
8
```

Para que se puedan montar, como máximo, 12 dispositivos de tipo loop, este parámetro debe tomar el valor `12`. Para cambiar el valor de este parámetro y, además, hacerlo de forma persistente, se debe usar un fichero de configuración almacenado en el directorio /etc/modprobe.d. En él, se crea un fichero con el mismo nombre del módulo y la extensión .conf y que debe tener como contendio el nuevo valor del parámetro.

```
nano /etc/modprobe.d/loop.conf

options loop max_loop=12
```

Para que esta modificación sea efectiva, se debe actualizar el initramfs y reiniciar el equipo.

```
sudo update-initramfs -u
sudo reboot
```

Tras reiniciar el contenido del fichero en el que se almacena la información de este parámtro se ha actualizado.

```
sudo cat /sys/module/loop/parameters/max_loop
12
```

<div class="page"/>

# Ejercicios de modificación de parámetros del kernel

## 1. Deshabilita apparmor en el arranque

AppArmor es un servicio que limita la cantidad de recursos a los que puede tener acceso un determinado programa. Esta limitación se establece a través de perfiles que se cargan en el kernel

Como otros muchos servicios en Debian, AppArmor se puede gestionar a través del comando `systemctl`. Así, para deshabilitar apparmor en el arranque se puede ejecutar el siguiente comando:

```
❯ sudo systemctl disable apparmor
Synchronizing state of apparmor.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install disable apparmor
Removed "/etc/systemd/system/sysinit.target.wants/apparmor.service".
```

## 2. Deshabilita si es posible el Kernel Mode Setting (KSM) de la tarjeta gráfica

KSM (Kernel Samepage Merging) es una característica de ahorro de memoia habilitada con el parámetro `CONFIG_KSM=y` del kernel incluida desde el kernel 2.6.32. Esta característica, usada por KVM, permite a varias máquinas virtuales compartir páginas de memoria idénticas. Estas páginas de memoria compartidas suelen ser librerías comunes a todas las máquinas u otros tipos de información idéntica frecuentemente usada por varias máquinas.

Algunos de los parámetros del kernel, incluidos alguno relacionados con KSM, se pueden modificar en el directorio /proc/sys pero estas modificaciones no son persistentes a reinicios.

Estos parámetros también se pueden gestionar usando el comando `sysctl`. Además, este comando cuenta con la peculiaridad de que permite modificar los valores del kernel durante la ejecución, es decir, en caliente. Los parámetros que puede gestiona el comando `sysctl` están listados en el directorio /proc/sys/.

En este caso, no es posible deshabilitar la característica KSM del kernel puesto que no está incluída en el kernel de este equipo. Al buscar los parámetros relacionados con KSM en el directorio /proc/sys no se muestra ninguno disponible.

```
❯ sudo find /proc/sys -iname '*ksm*'
```

## 3. Cambia provisionalmente la swappiness para que la swap de tu equipo se active cuando se use más de un 90% de la RAM

Swappiness también es una característica del kernel que se puede modificar. El valor de este parámetro se almacena en el directorio /proc/sys. Para encontrar dónde se alamacena este parámetro se puede hacer una búsqueda en este directorio.

```
❯ sudo find /proc/sys -iname '*swappiness*'
/proc/sys/vm/swappiness
```

Se puede conocer el valor que tiene este parámetro del kernel consultado el contenido de este fichero.

```
❯ cat /proc/sys/vm/swappiness
60
```

Con esta configuración, la swap del equipo se activa cuando se usa más del 60% de la RAM. Para modificar este valor se puede usar el comando `sysctl`.

```
❯ sudo sysctl vm.swappiness=90
vm.swappiness = 90
```

Tras ejecutar este comando, el valor de este parámetro del kernel se ha modificado en el fichero /proc/sys.

```
❯ cat /proc/sys/vm/swappiness
90
```

Pero esta configuración no es persistente al reinicio y sólo se aplica hasta el próximo apagado de la máquina.

## 4. Haz que el cambio de la swappiness sea permanente

Existen varias formas de hacer persistente al reinicio esta configuración. Una de las más comunes consiste en utilizar el fichero /etc/sysctl.conf. En este fichero se pueden configurar los valores de los parámetros del kernel que se cargan en cada reinicio del equipo.

En este caso, se puede añadir el nuevo valor de este parámetro a este fichero para hacer esta configuración permanente.

```
❯ echo "vm.swappiness=90" >> /etc/sysctl.conf
```

Tras ejecutar este comando, este parámetro del kernel toma su nuevo valor en el fichero de configuración de sysctl.

```
❯ cat /etc/sysctl.conf | grep 'swappiness'
vm.swappiness=90
```

Para que esta nueva configuración sea efectiva hay que volver a carga el fichero sysctl.conf.

```
❯ sudo sysctl -p
vm.swappiness = 90
```

La salida del comando muestra la lista de parámetros que se han modificado al cargar el nuevo fichero de configuración de parámetros del kernel. En este caso, sólo se muestar el nuevo valo de la swappiness.

## 5. Muestra el valor del bit de forward para IPv6

El bit de forward para las redes IPv6 se puede configurar para cada una de las interfaces de red de forma independiente o de forma general para todas ellas.

```
❯ find /proc/sys -iname 'forwarding' | grep ipv6
/proc/sys/net/ipv6/conf/all/forwarding
/proc/sys/net/ipv6/conf/br-intra/forwarding
/proc/sys/net/ipv6/conf/br-intra2/forwarding
/proc/sys/net/ipv6/conf/br0/forwarding
/proc/sys/net/ipv6/conf/default/forwarding
/proc/sys/net/ipv6/conf/enp2s0/forwarding
/proc/sys/net/ipv6/conf/lo/forwarding
/proc/sys/net/ipv6/conf/lxcbr0/forwarding
/proc/sys/net/ipv6/conf/veth0zlkeI/forwarding
/proc/sys/net/ipv6/conf/vethigCamC/forwarding
/proc/sys/net/ipv6/conf/vethj7iUWu/forwarding
/proc/sys/net/ipv6/conf/virbr0/forwarding
/proc/sys/net/ipv6/conf/virbr1/forwarding
/proc/sys/net/ipv6/conf/virbr2/forwarding
/proc/sys/net/ipv6/conf/virbr3/forwarding
/proc/sys/net/ipv6/conf/virbr4/forwarding
/proc/sys/net/ipv6/conf/virbr5/forwarding
/proc/sys/net/ipv6/conf/virbr6/forwarding
/proc/sys/net/ipv6/conf/vnet0/forwarding
/proc/sys/net/ipv6/conf/vnet1/forwarding
/proc/sys/net/ipv6/conf/vnet16/forwarding
/proc/sys/net/ipv6/conf/vnet17/forwarding
/proc/sys/net/ipv6/conf/vnet18/forwarding
/proc/sys/net/ipv6/conf/vnet19/forwarding
/proc/sys/net/ipv6/conf/vnet2/forwarding
/proc/sys/net/ipv6/conf/vnet20/forwarding
/proc/sys/net/ipv6/conf/vnet21/forwarding
/proc/sys/net/ipv6/conf/wlp4s0/forwarding
```

Para conocer si el bit de forward de cada una de estas interfaces está activado se puede consultar el valor de cada uno de estos ficheros, que será 0 si el bit no está activado o 1 si el bit está activado. En este caso, por ejemplo, el bit de forward para las interfaces IPv6 no está activado.

```
❯ cat $(find /proc/sys -iname 'forwarding' | grep ipv6)
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
0
```

## 6. Deshabilita completamente las Magic Sysrq en el arranque y vuelve a habilitarlas después de reiniciar

Las Magic Sysrq es una combinación de teclas "mágica" a las que el kernel responde independientemente de lo que esté haciendo siempre y cuando no esté bloqueado. Esta combinación de teclas se activa a través del parámetro del kernel `CONFIG_MAGIC_SYSRQ`. El fichero /proc/sys/kernel/sysrq contiene el valor de este parámetro.

En este fichero se puede configura uno de los siguientes valores:

- 0 = disable sysrq completely
- 1 = enable all functions of sysrq
- 2 =   0x2 - enable control of console logging level
- 4 =   0x4 - enable control of keyboard (SAK, unraw)
- 8 =   0x8 - enable debugging dumps of processes etc.
- 16 =  0x10 - enable sync command
- 32 =  0x20 - enable remount read-only
- 64 =  0x40 - enable signalling of processes (term, kill, oom-kill)
- 128 =  0x80 - allow reboot/poweroff
- 256 = 0x100 - allow nicing of all RT tasks

```
❯ cat /proc/sys/kernel/sysrq
438
```

Actualmente, el valor de este parámtro en el kernel es `438`. Es decir, esta combinación de teclas está configurada de manera que permite las características habilitadas por los valores `256`, `128`, `32`, `16`, `4` y `2`. 

Para deshabilitar las Magic Sysrq se puede cambiar el valor de este fichero a 0. Sin embargo, para que esta configuración sea persistente a los reinicios de la máquina, este parámetro se debe espcifica en el fichero /etc/sysctl.conf.

En el fichero /etc/sysctl.conf está especificado el mismo valor que en el fichero /proc/sys/kernel/sysrq aunque esta líea está comentada.

```
❯ sudo cat /etc/sysctl.conf | grep sysrq
# 0=disable, 1=enable all, >1 bitmask of sysrq functions
# See https://www.kernel.org/doc/html/latest/admin-guide/sysrq.html
#kernel.sysrq=438
```

Para deshabilitar esta característica del kernel se puede editar este fichero, descomentar esta línea y sustituir el valor `438` por `0`.

```
❯ sudo cat /etc/sysctl.conf | grep sysrq
# 0=disable, 1=enable all, >1 bitmask of sysrq functions
# See https://www.kernel.org/doc/html/latest/admin-guide/sysrq.html
kernel.sysrq=0
```

Para que esta modificación sea efectiva, hay que cargar la nueva configuración del fichero.

```
❯ sudo sysctl -p
kernel.sysrq = 0
vm.swappiness = 90
```

Tras reiniciar el equipo se puede devolver la configuración al valor por defecto en el fichero de configuración.

```
❯ sudo cat /etc/sysctl.conf | grep sysrq
# 0=disable, 1=enable all, >1 bitmask of sysrq functions
# See https://www.kernel.org/doc/html/latest/admin-guide/sysrq.html
kernel.sysrq=438

❯ sudo sysctl -p
kernel.sysrq = 438
vm.swappiness = 90

❯ cat /proc/sys/kernel/sysrq
438
```