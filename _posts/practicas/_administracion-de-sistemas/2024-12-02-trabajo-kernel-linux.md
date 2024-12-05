---
title:  "Trabajo con el kernel de Linux"
date:   2024-11-02 01:00:00 +0200
categories: administracion-sistemas
tag: [sistemas, debian, linux, kernel, apt, paquetes, comandos, administracion-sistemas, Administración de Sistemas Operativos]
excerpt: En esta entrada se aborda la actualización del kernel de Linux y su instalación desde el código fuente.
---

# Trabajo con kernel Linux

## Actualizar el kernel a través del gestor de paquetes

Ver la versión actual del kernel

```
❯ uname -r 
6.1.0-25-amd64
```

Comprobar las versiones disponibles del kernel

```
❯ apt policy linux-image-amd64
linux-image-amd64:
  Installed: (none)
  Candidate: 6.10.11-1
  Version table:
     6.10.11-1 500
        500 http://deb.debian.org/debian testing/main amd64 Packages
     6.1.106-3 500
        500 http://deb.debian.org/debian bookworm/main amd64 Packages
```

Ver los kerneles instalados en el sistema

```
❯ dpkg --list | grep linux-image
ii  linux-image-6.1.0-25-amd64       6.1.106-3                      amd64        Linux 6.1 for 64-bit PCs (signed)
```

Instalar el kernel

```
❯ sudo apt policy linux-image-amd64
```

Tras reiniciar el equipo, el sistema carga el nuevo kernel recién instalado.

Para desinstalar el kernel se debe arrancar el equipo con un kernel diferente al que se desinstala y, posteriormente, desinstalarlo desde el gestor de paquetes.

```
❯ sudo apt remove --purge linux-image-6.10.11-amd64
```

La instalación de un kernel genera los ficheros config, initrd.img y vmlinuz en el directorio /boot/.

```
❯ ls -l /boot/
total 47244
-rw-r--r-- 1 root root   259508 Aug 26 21:47 config-6.1.0-25-amd64
drwxr-xr-x 5 root root     4096 Oct  8 13:27 grub
-rw-r--r-- 1 root root 39925056 Oct  4 09:30 initrd.img-6.1.0-25-amd64
-rw-r--r-- 1 root root       83 Aug 26 21:47 System.map-6.1.0-25-amd64
-rw-r--r-- 1 root root  8177600 Aug 26 21:47 vmlinuz-6.1.0-25-amd64
```

## Compilar el kernel desde el código fuente

Descargar el código fuente

```
❯ wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.11.2.tar.xz
```

Es un archivo pesado que tarda más de 30 segundos en descomprimirse.

```
❯ time tar -xf linux-6.11.2.tar.xz 

real	0m32.098s
user	0m13.602s
sys	0m7.698s
```
El directorio generado tras descomprimir el archivo contiene múltiples ficheros .c y .h con el código fuente del kernel. En total son casi 60.000 ficheros de código fuente. Además, se puede contar el número de líneas de código fuente que componen el kernel.

```
❯ find . -iname "*.[ch]" | wc -l
59558
❯ find . -iname "*.[ch]" -exec cat {} \; | grep "[;|#]" | wc -l
15863403
``