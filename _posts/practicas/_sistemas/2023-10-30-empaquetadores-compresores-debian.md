---
title:  "Empaquetadores y compresores/descompresores en Debian"
date:   2023-11-3 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, apt, empaquetadores, compresores, tar, gzip, zip, rar, compress, xz, 7z comandos, Implantación de Sistemas Operativos]
excerpt: Los empaquetadores y compresores/descompresores son paquetes fundamentales en cualquier distribución de un sistema operativo. En este post se analizan varios de estos paquetes disponibles para Debian y otras distribuciones GNU/Linux
---

# Empaquetador `tar`

##  Definición

El empaquetador `tar`  crea y manupila archivos que son, en realidad, colecciones de varios ficheros. `Tar` ofrece un método sistematizado y organizado de controlar grandes volúmenes de datos.

El programa `tar` se usa para crear y manipular archivos `tar`, que son archivos individuales que contienen el contenido de varios ficheros cuyos nombres, propie`tar`ios y otra información pueden conservar.

Los ficheros contenidos en estos archivos se llaman miembros (members). El término extracción se refiere al proceso de copiar estos archivos miembros en un fichero en el sistema de ficheros. A la acción de extraer todos los miembros de un archivo también se la llama “extraer el archivo”. El término desempaque`tar` también se puede usar para referirse a la extracción de varios o todos los miembros de un archivo.

Extraer un archivo no destruye la estructura del archivo, de la misma forma que crear un archivo no destruye las copias de los ficheros que existen fuera del archivo. 

`Tar` ofrece la posibilidad de crear archivos `tar`, así como extraer ficheros de archivos previamente creados, almacenar fichero adicionales o actualizar la lista de ficheros que han sido almacenados en un archivo.

Originalmente, `tar` se usaba para almacenar archivos en cintas magnéticas y el nombre del programa viene de “tape archiver” (archivador de cinta). Sin embargo, `tar` puede enviar su salida a cualquier dispositivo disponible, ficheros o programas, a través de tuberías (pipes). Incluso puede acceder a dispositivos o ficheros remotos.

El programa `tar` se usa con mucha frecuencia para almacenar ficheros relacionados para su transferencia a través de una red, de manera que se puedan transferir como una única unidad. Los archivos `tar` también se suelen usar para almacenamiento de larga duración.

Otro uso habitual de `tar` es el de las copias de seguridad. Los archivos `tar` conservan la información de los ficheros y la estructura de directorios, lo que hace que `tar` sea usado habitualmente para crear copias de seguridad de discos. Una copia de seguridad guarda una colección de ficheros y directorios en un disco, protegiendo la información contra una posible destrucción accidental. El programa `tar` tiene funcionalidades especiales que permiten que se pueda usar para hacer volcados de todos los ficheros de un sistema de ficheros.

Por último, otro uso habitual de `tar` es la transferencia de información. Con `tar` se puede crear un archivo en un sistema, transferirlo a otro y extraerlo en él. De esta forma, se puede transferir un grupo de ficheros de un sistema a otro de forma cómoda y ordenada.

## Opciones más comunes y ejemplos de utilización

Algunas de las opciones más comunes y su ejemplo de utilización para `tar` son:

- ```
tar -A [OPTIONS] ARCHIVE ARCHIVE
tar {--catenate|--concatenate} [OPTIONS] ARCHIVE ARCHIVE
```
Empaqueta un archivo al final del otro. Los argumentos son los nombres de los archivos que se van a empaque`tar`. Todos los archivos deben tener el mismo formato que el resto de archivos con los que se van a empaque`tar`.
- ```
tar -c [-f ARCHIVE] [OPTIONS] [FILE…]
tar --create [--file ARCHIVE] [OPTIONS] [FILE…]
```
Crea un nuevo archivo.
- ```
tar -d [-f ARCHIVE] [OPTIONS] [FILE…]
tar {--diff|--compare} [--file ARCHIVE] [OPTIONS] [FILE…]
```
Encuentra las diferencias entre el archivo y el sistema de ficheros.
- ```
tar --delete [--file ARCHIVE] [OPTIONS] [MEMBER…]
```
Elimina ficheros o miembros del archivo. Los argumentos se refieren al nombre de los archivos miembros que se van a eliminar.
- ```
tar -r [-f ARCHIVE] [OPTIONS] [FILE…]
tar --append [-f ARCHIVE] [OPTIONS] [FILE…]
```
Empaqueta ficheros al final de un archivo. Los argumentos se usan igual que con `-c` (--create).
- ```
tar -t [-f ARCHIVE] [OPTIONS] [MEMBER…]
tar --list [-f ARCHIVE] [OPTIONS] [MEMBER…]
```
Lista el contenido de un archivo. Los argumentos son opcionales y especifican los nombres de los miembros que se deben lis`tar`.
- ```
tar --test-label [--file ARCHIVE] [OPTIONS] [LABEL…]
```
Prueba la etiqueta de volumen y sale. Sin argumentos devuelve el volumen y sale, si se usan argumentos compara la etiqueta de volumen de cada argumento y devuelve un 0 si encuentra alguna coincidencia y un 1 si no.
- ```
tar -u [-f ARCHIVE] [OPTIONS] [FILE…]
tar --update [--file ARCHIVE] [OPTIONS] [FILE…]
tar --update [-f ARCHIVE] [OPTIONS] [FILE…]
```
Empaqueta ficheros que son más nuevos que la copia correspondiente en el archivo. Los argumentos tienen el mismo significado que con `-c` y `-r`.

- ```
tar -x [-f ARCHIVE] [OPTIONS] [MEMBER…]
tar {--extract|--get} [-f ARCHIVE] [OPTIONS] [MEMBER…]
```
Extrae ficheros de un archivo. Los argumentos son opcionales. Especifican el nombre de los miembros del archivo que se van a extraer.

## Extensión de los ficheros

Los ficheros empaquetados con `tar` tienen la extensión .`tar`.

## Paquete donde se encuentra

`tar` se encuentra en el paquete `tar`, ubicado en la ruta `/usr/bin/tar`.

# Compresor/descompresor `gzip`

## Definición

La orden `gzip` reduce el tamaño del archivo nombrado usando el código Lempel-Ziv (LZ77). Cuando sea posible, cada fichero se reemplaza por una con extensión .gz manteniendo la propiedad, acceso y fecha de modificación.

Si el fichero tiene un nombre demasiado largo para el sistema de ficheros, `gzip` también puede truncar el nombre. Por defecto, mantiene el mismo nombre en los ficheros comprimidos.
Además, `gzip` también puede descomprimir ficheros creados por `gzip`, `zip`, `comrpess` o `pack`.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de `gzip` es el siguiente:

```
gzip [OPTION]… [FILE]…
```
Entre las opciones más comunes se encuentran:
- `-c`, `--stdout`. Escribe el fichero en salida estándar y mantiene sin cambio los ficheros originales.
- `-d`, `--decompress`. Descomprime los ficheros indicados.
- `-f`, `--force`. Fuerza la sobreescritura del fichero de salida y comprime los enlaces.
- `-h`, `--help`. Muestra la ayuda del comando.
- `-k`, `--keep`. Conserva el fichero de entrada.
- `-l`, `--list`. Lista el contenido de los ficheros comprimidos
- `-n`, `--no-name`. No conserva ni recupera el nombre y la hora de modificación del fichero original.
- `-N`, `--name`. Guarda y recupera el nombre y la hora de modificación del fichero original.
- `-r`, `--recursive`. Opera de forma recursiva en directorios.
- `-t`, `--test`. Comprueba la integridad de los ficheros comprimidos.
- `-v`, `--verbose`. Modo verboso
- `-V`, `--version`. Muestra el número de versión.
- `-1`, `--fast`. Comprime más rápido.
- `-9`, `--best`. Comprime mejor.

## Extensión de los ficheros

Los ficheros empaquetados con `gzip` tienen la extensión .gz.

## Paquete donde se encuentra

`Gzip` se encuentra en el paquete gzip, ubicado en la ruta `/usr/bin/gzip` y en el paquete klibc-utils, ubicado en la ruta `/usr/lib/klibc/bin/gzip`.

# Compresor/descompresor `xz`

## Definición

El programa `xz` es una herramienta de compresión de datos de propósito general con una sintaxis de línea de comando similar a `gzip` y `bzip2`. Soporta el formato nativo .xz pero también .lzma.

Con `xz` se pueden comprimir o descomprimir cada fichero según un modo de operación seleccionado.
El nombre del fichero comprimido deriva del fichero original. Al comprimir, se añade al fichero original el sufijo .xz o .lzma. Al descomprimir, se elimina el sufijo. Además, xz también reconoce las extensiones .txz y .tlz y las sustituye por .tar.

Tras comprimir o descomprimir un fichero, `xz` copia también el propietario, grupo, permisos, hora de acceso y de modificación del fichero origen.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de xz es el siguiente:
```
xz [OPTION]… [FILE]…
```

Entre las opciones más comunes se encuentran:
- `-z`, `--compress`. Comprime el fichero. Es el modo de operación por defecto.
- `-d`, `--decompress`, `--uncompress`. Descomprime el fichero.
- `-t`, `--test`. Comprueba la integridad de los ficheros comprimidos.
- `-l`, `--list`. Lista información sobre los ficheros comprimidos. No se produce ninguna descompresión.
- `-k`, `--keep`. No elimina los ficheros de entrada.
- `-f`, `--force`. Realiza varias acciones:
    - Si el fichero objetivo existe, lo elimina antes de comprimirlo o descomprimirlo.
    - Comprime o descomprime aunque el fichero sea un enlace simbólico de un fichero normal.
-  `-c`, `--stdout`, `--to-stdout`. Escribe la información comprimida o descomprimida a la salida estándar en vez de a un fichero.
- `-S .suf`, `--suffix=.suf`. Permite seleccionar la extensión del archivo. Al comprimir, se puede elegir una extensión diferente a .xz y .lzma. Al descomprimir, reconocer los ficheros con el sufijo indicado además de .xz, .lzma, .txz, .tlz o .lz.

## Extensión de los ficheros

Los ficheros empaquetados con `xz` tienen la extensión .xz o .lzma. También reconoce y descomprime ficheros con extensiones como .txz, .tlz o .tz.

## Paquete donde se encuentra
`Xz` se encuentra en el paquete xz, ubicado en la ruta `/usr/bin/xz`.

# Compresor/descompresor `bzip2`

## Definición

El programa `bzip2` comprime ficheros usando la compresión de Burrows-Wheeler y el código de Huffman. Esta compresión es, generalmente, mejor que la obtenida con compresores más convencionales basados en LZ77/LZ78. Está consturido sobre la librería libbzip2, una librería flexible para el manejo de datos comprimidos en formato bzip2.

Las opciones de `bzip2` son muy similares a las de gzip pero no idénticas. Cada fichero se sustituye por una versión comprimida de sí mismo con el nombre original y la extensión .bz2. Los ficheros comprimidos conservan la fecha de modificación, permisos y, cuando es posible, propiedad que el original. `Bzip2` no sobreescribe los ficheros originales por defecto.
Si el fichero no usa una extensión reconocible por `bzip2` (.bz2, .bz, .tbz2 o .tbz), `bzip2` advierte de que no se ha podido averiguar la extensión y usa el nombre del fichero original con la extensión .out.

## Opciones más comunes y ejemplos de utilización

Algunos ejemplos de utilización de `bzip2` son los siguientes:

```
bzip2 [OPTION]… [FILE]…
bunzip2 [OPTION]… [FILE]…
bzcat [OPTION]… [FILE]…
bzip2recover FILE
```

Entre las opciones más comunes se encuentran:

- `-c` `--stdout`. Comprime o descomprime a la salida estándar.
- `-d` `--decompress`. Descomprime el fichero indicado.
- `-z` `--compress`. Comprime el fichero indicado.
- `-t` `--test`. Comprueba la integridad de los ficheros comprimidos.
- `-f` `--force`. Fuerza la sobreescritura del fichero de salida. Por defecto, bzip2 no sobreescribe los ficheros de salida.
- `-k` `--keep`. Conserva los ficheros de entrada durante la compresión o descompresión.
- `-s` `--small`. Reduce el uso de memoria para la compresión, descompresión y comprobación de los ficheros.
- `-v` `--verbose`. Modo verboso. Muestra el ratio de compresión para cada fichero.
- `-L` `--license` `-V` `--version`. Muestra la versión, licencia, términos y condiciones del software.
- `-1` (or `--fast`) to `-9` (or `--best`). Determina el tamaño de los bloques de compresión. 

## Extensión de los ficheros

Los ficheros empaquetados con `bzip2` tienen la extensión .bz2. También reconoce y descomprime ficheros con extensiones como .bz, .tbz2 o .tbz.

## Paquete donde se encuentra

`bzip2` se encuentra en el paquete bzip2, ubicado en la ruta `/usr/bin/bzip2`.

# Compresor/descompresor 7z

## Definición

7-Zip es software de fuente abierta. La mayor parte del código fuente se distribuye bajo la licencia GNU LGPL. Algunas partes del código están bajo la licencia BSD de 3 cláusulas.

7-Zip es un archivador de ficheros que soporta 7z, que implementa la compresión algorítmica LZMA con una ratio de compresión muy alta. El ratio de compresión en 7z es entre un 30% y un 50% mejor que en el formato zip.

Cada fichero indicado se comprime a uno con extesión .7z y después se elimina.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de `p7zip` es el siguiente:

```
p7zip [OPTION]… [FILE]…
```

Entre las opciones más comunes se encuentran:

- `-c`, `--stdout`, `--to-stdout`. Escribe la salida a la salida estándar.
- `-d`, `--decompress`, `--uncompress`. Descomprime los archivos.
- `-f`, `--force`. Fuerza la compresión o descompresión.
- `-k`, `--keep`. No elimina el fichero de entrada.
- `--`. Trata todos los argumentos que siguen como nombres de ficheros aunque comiencen con un guión (-).

## Extensión de los ficheros

La extensión de los ficheros comprimidos con 7z es .7z.

## Paquete donde se encuentra

7z se encuentra en el paquete `p7zip`, ubicado en la ruta `/usr/bin/p7zip`. P7zip también es una librería ubicada en la ruta /usr/lib/p7zip.

# Compresor/descompresor `zip`

## Definición

`Zip` empaqueta y comprime ficheros. `Zip` es una utilidad de compresión y empaquetado de ficheros análoga a una combinación de los comandos tar y compress de Unix y es compatible con PKZIP.

Este programa es útil para empaquetar un conjunto de ficheros para su distribución, para archivar ficheros y para ahorrar espacio en el disco duro comprimiendo temporalmente ficheros y directorios que no se usan.

`Zip` almacena uno o varios ficheros comprimidos en un único archivo zip, junto con información sobre los ficheros (nombre, ruta, fecha y hora de la última modificación, protección e información de comprobación de la integridad del fichero). Una estructura de directorios completa se puede empaquetar en un fichero zip con un único comando. `Zip` puede comprimir los ficheros o guardarlos sin compresión.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de `zip` es el siguiente:

```
zip [OPTION]... [FILE]... [INPATH]...
```
Entre las opciones más comunes se encuentran:

- `-f`  `--freshen`. Reemplaza una entrada existente en el archivo zip sólo si se ha modificado más recientemente que la versión que ya se encuentra en el archivo.
- `-u`  `--update`. Afecta sólo a ficheros nuevos o modificados.
- `-d` `--delete`. Elimina entradas de un archivo zip.
- `-m` `--move`. Mueve ficheros a un archivo zip.
- `-r` `--recurse-path`. Afecta recursivamente al contenido de un directorio.
- `-j` `--junk-path`. No guarda los nombres de los directorios.
- `-0` `--output-file`. Solo almacena los ficheros sin comprimirlos.
- `-#` (`-0` … `-9`). Indica la velocidad de compresión. `-1` comprime más rápido; `-9` comprime mejor.
- `-v` `--verbose`. Modo verboso. Muestra información por pantalla.
- `-c` `--entry-comments`. Añade comentarios de una línea para cada fichero.
- `-z` `--archive-comment`. Añade comentarios de varias líneas al archivo completo.
- `-@` `--names-stdin`. Toma la lista de ficheros de entrada de la entrada estándar.
- `-o` `--latest-time`. Asigna la fecha de modificación del fichero más antiguo al archivo zip.
- `-x` `--exclude`. Excluye los ficheros indicados
- `-i` `--include`. Incluye los ficheros indicados.
- `-F` `--fix`. Se usa si alguna falta alguna parte del acrhivo. `-FF` `–fixfix` se usa si el archivo está demasiado dañado.
- `-D` `--no-dir-entries`. No crea entradas para directorios en el archivo zip.
- `-A` `--adjust-sfx`. Hace un archivo ejecutable que se extrae automáticamente.
- `-J` `--junk-sfx`. Almacena el nombre del fichero guardado pero no los nombres de los directorios.
- `-T` `--test`. Comprueba la integridad de los ficheros
- `-X` `--no-extra`. No almacena atributos extra de los ficheros.
- `-e` `--encrypt`. Encripta el contenido del archivo. 
- `-n` `--suffixes`. No comprime los fiches con la extensión indicada.

## Extensión de los ficheros

La extensión de los ficheros comprimidos con `zip` es .zip.

## Paquete donde se encuentra

`zip` se encuentra en el paquete zip, ubicado en la ruta `/usr/bin/zip`.

# Compresor/descompresor `rar`

## Definición

`Rar` es un compresor y archivador que empaqueta ficheros con compresión.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de zip es el siguiente:

```
rar <command> [-<switch 1> -<switch N>] archive [files…]
```
Entre las opciones más comunes se encuentran:
- `-a`. Añade ficheros al archivo.
- `-c`. Añade comentarios al archivo.
- `-cf`. Añade comentarios al fichero.       
- `-cw`. Añade un comentario a un fichero especificado.
- `-d`. Elimina ficheros del archivo.
- `-e`. Extrae ficheros del directorio actual. No crea subdirectorios.
- `-f`. Actualiza los ficheros del archivo.
- `-k`. Bloquea los archivos.
- `-l[t]`. Lista el contenido del archivo
- `-m[f]`. Mueve los archivos. Con f se mueven solo los ficheros y no se eliminan los directorios.
- `-p`. Muestra el fichero por la salida estándar.
- `-r`. Repara el archivo
- `-t`. Comprieba la integridad de los ficheros.
- `-u`. Actualiza los ficheros de un archivo.
- `-v[t]`. Modo verboso. Muestra información del archivo.
- `-x`. Extra ficheros sin la ruta completa.

## Extensión de los ficheros

La extensión de los ficheros comprimidos con `rar` es .rar.

## Paquete donde se encuentra
`rar` se encuentra en el paquete rar, ubicado en la ruta /usr/bin/rar.

# Compresor/descompresor `unzip`

## Definición

Lista, comprueba y extrae los ficheros almacenados en un archivo zip. Por defecto, `unzip` extrae los ficheros en el directorio de trabajo.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de `unzip` es el siguiente:

```
unzip [OPTION]… [FILE]…
```

Entre las opciones más comunes se encuentran:

- `-p`. Extrae los ficheros a una tubería.
- `-f`. Actualiza los ficheros existentes y no crea ninguno nuevo.
- `-u`. Actualiza los ficheros y crea los que sean necesarios.
- `-v`. Lista en modo verboso y muestra información de la versión.
- `-x`. Excluye los ficheros indicados.
- `-n`. No sobreescribe ficheros existentes.
- `-o`. Sobreescribe ficheros
- `-j`. No crea directorios.
- `-K`. Conserva los permisos de setuid/setgid/tracky
- `-l`. Lista los ficheros en formato corto.
- `-t`. Comprueba la integridad de los ficheros comprimidos.
- `-z`. Muestra sólo los comentarios.
- `-T`. Usa la marca temporal más antigua para el archivo.
- `-d`. Extrae los ficheros a exdir.
- `-a`. Convierte automáticamente cualquier fichero de texto.
- `-aa`. Trata todos los ficheros como ficheros de texto.
- `-M`. Muestra el resultado en el paginador “more”.

## Extensión de los ficheros

`Unzip` descomprime archivos con formato .zip.

## Paquete donde se encuentra

`Unzip` se encuentra en el paquete unzip, ubicado en la ruta `/usr/bin/unzip`.

# Compresor/descompresor `unrar`

## Definición

El programa `unrar` extrae ficheros de archivos rar.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de unzip es el siguiente:

```
unrar  command  [-switch ...] archive [file ...] [@listfiles ...] [path...] [path_to_extract/]
```
Entre las opciones más comunes se encuentran:

- `e`. Extrae los ficheros al directorio de trabajo.
- `l`. Lista el contenido del archivo. 
- `t`. Comprueba la integridad de los ficheros
- `v`. Lista el contenido del archivo en modo verboso.
- `x`. Extrae los ficheros con una ruta completa.
- `-ag`. Genera nombres de archivo usando la fecha actual.
- `-ai`. Ignora los atributos de los ficheros.
- `-c-`. Deshabilita los comentarios.
- `-cl`. Convierte los nombres de los ficheros a minúsculas.
- `-cu`. Convierte los nombres de los ficheros a mayúsculas.
- `-ep`. Excluye las rutas de los nombres.
- `-kb`. Mantiene los ficheros extraídos rotos.
- `-o+`. Sobreescribe ficheros existentes.
- `-o-`. No sobreescribe los ficheros existentes.
- `-or`. Renombra los ficheros automáticamente.
- `-r`. Afecta recursivamente a los subdirectorios.
- `-sl<tamaño>`. Procesa los ficheros con un tamaño menor del especificado.
- `-sm<tamaño>`. Procesa los ficheros con un tamaño mayor del especificado.
- `-u`. Actualiza los ficheros
- `-v`. Lista todos los volúmenes.
- `-x<fichero>`. Excluye el fichero especificado
- `-x@<lista>`. Excluye los ficheros de la lista especificada.

## Extensión de los ficheros

`unrar` descomprime archivos con formato .rar.

## Paquete donde se encuentra

`unrar` se encuentra en el paquete unrar, ubicado en la ruta `/usr/bin/unrar`.

# Compresor/descompresor `compress`

## Definición

`Compress` reduce el tamaño de los ficheros indicados usando el código adaptativo Lempel-Ziv. Cuando sea posible, cada fichero se reemplaza por uno con la extensión .Z y se mantienen la propiedad de los ficheros y la hora de acceso y modificación. Si no se especifican los ficheros, se comprimen los ficheros de la entrada estándar a la salida estándar. `Compress` sólo intenta comprimir ficheros normales e ignora los enlaces simbólicos. Los ficheros comprimidos usando `compress` se pueden restaurar a su forma original usando uncompress.real.

## Opciones más comunes y ejemplos de utilización

Un ejemplo de utilización de `compress` es el siguiente:

```
compress [OPTION]… [NAME]…
```

Entre las opciones más comunes se encuentran:

- `-c`. Escribe a la salida estándar. No modifica los ficheros.
- `-r`. Opera de forma recursiva. Si algún fichero especificado es un directorio, compress accede al directorio y comprime todos los ficheros que encuentre en él.
- `-V`. Muestra la versión antes de realizar ninguna compresión o descompresión.
- `-b`. Indica el nivel de compresión en n.º de bits.
- `-v`. Muestra el porcentaje de reducción de cada fichero comprimido.
- `--`. Indica que todo el resto de argumentos se deben tratar como rutas.

## Extensión de los ficheros

`Compress` comprime archivos en formato .Z.

## Paquete donde se encuentra

`Compress` se encuentra en el paquete ncompress, ubicado en la ruta `/usr/bin/ncompress`.