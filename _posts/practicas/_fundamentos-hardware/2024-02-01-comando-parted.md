---
title:  "Ejemplos de uso del comando parted en Debian"
date:   2024-02-01 01:00:00 +0200
categories: fundamentos-hardware
tag: [Fundamentos de Hardware, debian, linux, comandos básicos, parted, comandos]
---
`Parted` es un programa que manipula particiones de disco. Soporta múltiples formatos de tablas de particiones como MS-DOS y GPT. Es útil para crear espacio para nuevos sistemas operativos, reorganizar el uso del disco y copiar su contenido a un nuevo disco duro.

Así, `parted` permite crear, destruir, redimensionar, mover y copiar particiones de disco. Este programa puede usar múltiples formatos de particiones, tales como DOS, Mac, Sun, BSD, GPT, MIPS y PC98, así como el tipo loop que permite su uso en RAID/LVM.

Además, `parted` puede detectar y eliminar sistemas de archivos ASFS/AFFS/APFS, Btrfs, ext2/3/4, FAT16/32, HFS, JFS, linux swap, UFS, XFS y ZFS. Por otra parte, este programa también puede crear y modificar sistemas de archivos de algunos de estos tipo.

El uso de `parted` debe ser cuidadoso porque un error en su utilización puede provocar una pérdida de datos en el disco duro, por lo que es aconsejable hacer una copia de seguridad del contenido del disco antes de trabajar con este programa de particionado como con el resto de herramientas de este tipo.

# Sintaxis

La sintaxis para invocar a este programa es:

```
parted [opciones] [dispositivo [orden [opciones…]…]]
```

De manera que se puede lanzar el programa desde la línea de comandos sin ninguna opción ni argumento y una vez iniciado, indicar las diferentes acciones y dispositivos sobre los que se desea trabajar.

También existe la opción de trabajar en una sola línea de comandos indicando junto al nombre del programa, las opciones que se desean usar y los dispositivos sobre los que se van a realizar las tareas indicadas.

# Opciones

Algunas opciones generales para el uso del comando `parted` son:

- `-h`, `--help`. Muestra un mensaje de ayuda.
- `-l`, `--list`. Lista las particiones de todos los dispositivos de bloques.
- `-m`, `--machine`. Devuelve la información en modo analizable.
- `-s`, `--script`. Nunca pide la intervención del usuario.
- `-a alineación`, `--align=alineación`. Determina el tipo de alineación para las nuevas particiones. Los tipos que se pueden usar son none, que usa la mínima alineación permitida por el tipo de disco; cylinder, que alinea las particiones a los cilindros; minimal, que usa la mínima alineación necesaria para alinear las particiones lógicas a los bloques físicos; y optimal, que alinea las particiones a un múltiplo del tamaño físico del bloque, de manera que garantice un funcionamiento óptimo. Estas opciones no tienen mucho sentido en la actualidad puesto que ya se suele trabajar con discos de estado sólido y este modo de trabajar con las particiones está orientado al uso de discos mecánicos, cada vez más en desuso.

# Órdenes

Tras la opción u opciones que se deban especificar en el uso de `parted`, se puede indicar también uno o varios dispositivos sobre los que el programa debe trabajar. Si no se indica ningún dispositivo de bloques, `parted` usará el primero que encuentre.
Posteriormente, también se pueden indicar las órdenes que el programa debe llevar a cabo sobre cada uno de los dispositivos indicados. Alguna de estas órdenes pueden ser:
- `help`. Muestra la ayuda general o la ayuda de una orden específica indicada.
- `mklabel`. Crea una nueva tabla de particiones del tipo indicado entre las opciones "aix", "amiga", "bsd", "dvh", "gpt", "loop", "mac", "msdos", "pc98" o "sun".
- `mkpart`. Crea una nueva partición. Si se está usando una tabla de particiones de tipo msdos o dvh se debe especificar si la partición es primaria, lógica o extendida. Si la tabla de particiones es de tipo GPT, se debe indicar un nombre de la partición y, adicionalmente, se puede indicar el sistema de ficheros que se quiere usar en la partición entre las opciones que permite `parted`: "btrfs", "ext2", "ext3", "ext4", "fat16", "fat32", "hfs", "hfs+", "linux-swap", "ntfs", "reiserfs", "udf" o "xfs". A diferencia del resto de herramientas de particionado que se han usado en clase, `parted` permite dar un sistema de ficheros a cada partición directamente en el momento de su creación sin tener que usar también el comando mkfs en un paso posterior a la creación de las particiones del disco.
- `name`. Da nombre a una partición.
- `print`. Muestra la tabla de particiones, los dispositivos disponibles, el espacio libre, las particiones encontradas o una partición en concreto.
- `quit`. Sale del programa.
- `rescue`. Permite recuperar una partición perdida indicando el punto de inicio y de fin de la partición.
- `resizepart`. Redimensiona una partición indicando un nuevo punto de fin.
- `rm`. Elimina una partición.
- `select`. Elige el dispositivo a editar.

# Ejemplos de uso

Un ejemplo de uso del comando `parted` puede consistir en particionar un disco duro. En este ejemplo se va a utilizar un disco duro llamado /dev/vdb. Para poder usarlo, el primer paso es ejecutar el programa `parted` y, a continuación, seleccionar el disco.

```
select /dev/vdb
```

Posteriormente, se puede establecer una nueva tabla de partciones de tipo GPT con la orden `mklabel`.

Con la tabla de particiones creadas, ya se puede usar la orden `mkpart` para crear las diferentes particiones en el disco. Cabe destacar que, con esta herramienta, se puede dar un sistema de ficheros a la partición desde su creación como se muestra en la siguiente imagen.

```
mkpart uefi fat32 1M 100M
mkpart / ext4 101M 1G
```

Para ver la tabla de particiones creada se puede usar la orden `print`. Además, `parted` permite redimensionar particiones con la orden `resizepart`.

```
resizepart 2 512M
```

Con la orden `rm` se puede eliminar una partición.

```
rm 1
```

# Conclusión

`Parted` puede resultar un comando útil para el particionado de discos duros gracias a las mejoras que implementa respecto a otras herramientas similares, tales como la posibilidad de redimensionar o recuperar particiones, así como la opción de dotar a cada partición de un sistema de ficheros desde el momento mismo de su creación.

En las tareas del administrador de sistemas, donde puede ser habitual el trabajo con particionado de discos de forma intensiva, herramientas de este tipo que facilitan el proceso y disminuyen los pasos necesarios para alcanzar un objetivo pueden ser una baza interesante a tener en cuenta.