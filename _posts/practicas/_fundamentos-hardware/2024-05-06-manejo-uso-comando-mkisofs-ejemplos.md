---
title: "Manejo y uso del comando mkisofs"
date: 2024-05-06 01:00:00 +0200
categories: fundamentos-hardware
tag: [Fundamentos de Hardware, debian, linux, instalación, mkisofs, comandos, file system, iso]
---
El comando mkisofs o `genisoimage` es un programa que genera sistemas de ficheros de tipo ISO. Este comando genera registros usando el protocolo SUSP (System Use Sharing Protocol) especificado por el Rock Ridge Interchange Protocol para trasladar los ficheros de un sistema de ficheros con formato ISO9660 a una máquina Unix, incluyendo información sobre nombres de ficheros largos, UID/GID, permisos POSIX, enlaces simbólicos, dispositivos de bloques.

`Genisoimage` usa la imagen de un árbol de directorios y genera una imagen binara y la escribe en un dispositivo de bloques en formato ISO9660. Además, trunca y modifica los nombres de fichero para evitar inconsistencias en la creación de la imagen ISO.
Este comando no está diseñado para comunicarse directamente con el grabador en el caso de que la imagen se quiera grabar en un disco y es necesario usar herramientas específicas para llevar a cabo este proceso como wodim.

Si se especifican varias rutas al usar mkisofs, los ficheros que conforman cada una de ellas se unirán en el sistema de ficheros de tipo imagen.

# Sintaxis

La sintaxis del comando genisoimage es 

```
genisoimage [options] [-o filename] pathspec [pathspec …]
```

# Opciones

- `-nobak`, `-no-bak`. Excluye los ficheros de backup.
- `-e FILE`, `-efi-boot FILE`. Establece el nombre de la imagen de arranque EFI.
- `-G FILE`, `-generic-boot FILE`. Estable el nombre de la imagen de arranque genérica. 
- `-dir-mode mode`. Establece los permisos de todos los directorios. 
- `-file-mode mode`. Establece los permisos de todos los ficheros.
- `-f`, `-follow-links`. Mantiene los enlaces simbólicos. 
- `-gid gid`. Establece el grupo como propietario de todos los ficheros.
- `-root DIR`. Establece el directorio raíz.
- `-iso-level LEVEL`. Establece el nivel ISO9660 del 1 al 3 o 4 para ISO9660 versión 2.
- `-l`, `-full-iso9660-filenames`. Permite nombres de fichero de hasta 31 caracteres.
- `-max-iso9660-filenames`. Permite nombres de fichero de hasta 37 caracteres (no cumple con la norma ISO9660).
- `-allow-limited-size`. Permite un tamaño de ficheros diferente para ficheros grandes.
- `-allow-leading-dots`, `-ldots`, `-L`, `-allow-leading-dots`. Permite los ficheros que empiezan por un punto (‘.’) (no cumple con la norma ISO9660).
- `-log-file LOG_FILE`. Redirige los mensajes a un fichero de log indicado.
- `-m GLOBFILE`, `-exclude GLOBFILE`. Excluye el fichero indicado.
- `-exclude-list FILE`. Excluye los ficheros incluidos en la lista.
- `-new-dir-mode mode`. Establece los permisos para los nuevos directorios.
- `-o FILE`, `-output FILE`. Indica el nombre del fichero en el que se guarda la imagen.
- `-path-list FILE`. Fichero con la lista de rutas que se deben procesar.
- `-print-size`. Muestra el tamaño estimado del sistema de ficheros y sale.
- `-s TYPE`, `-sectype TYPE`. Establece el tipo de sector de la imagen ISO (data/xa1/raw…).
- `-sort FILE`. Ordena los ficheros según las reglas proporcionadas.
- `-split-output`. Divide la imagen en ficheros de aproximadamente 1GB.
- `-stream-media-size #`. Establece el tamaño del dispositivo en el que se quiere grabar la imagen en sectores.
- `-sysid ID`. Establece el ID del sistema.
- `-T`, `-translation-table`. Genera una tabla de traducción para los sistemas que no comprenden nombres de ficheros largos.
- `-table-name TABLE_NAME`. Indica una tabla de traducción para los sistemas que no comprenden nombres de ficheros largos.
- `-uid uid`. Establece el usuario como propietario de todos los ficheros.
- `-U`, `-untranslated-filenames`. Allow Untranslated filenames (for HPUX & AIX - violates ISO9660). Forces `-l`, `-d`, `-N`, `-allow-leading-dots`, `-relaxed-filenames`, `-allow-lowercase`, `-allow-multidot`.
- `-allow-lowercase`. Permite caracteres en minúscula (no cumple con la norma ISO9660).
- `-allow-multidot`. Permite más de un punto en el nombre de los ficheros como .tar.gz (no cumple con la norma ISO9660).
- `-use-fileversion LEVEL`. Usa la versión indicada del sistema de ficheros.
- `-v`, `-verbose`. Modo verboso.
- `-version`. Muestra la versión.
- `-V ID`, `-volid ID`, `-volset ID`. Establece el ID del volumen.
- `-volset-size #`. Establece el tamaño del volumen.
- `-hard-disk-boot`. Indica que es la imagen es de un disco duro.
- `-no-boot`. Indica que la imagen no es arrancable.

# Ejemplos de uso

ara crear una imagen iso llamada imagen.iso del directorio raíz.

```bash
genisoimage -o imagen.iso /
```

Para usar la extensión Rock Ridge al crear una imagen.

```bash
genisoimage -o imagen.iso -R /
```

Para dar a todos los ficheros permisos, al menos, de lectura y cambiar su propietario al root.

```bash
genisoimage -o imagen.iso -r /
```
