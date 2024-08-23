---
title:  "Ejemplos de uso de comandos básicos en Debian"
date:   2023-11-10 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos básicos, ls, cd, mkdir, cp, mv, rm, comandos, Implantación de Sistemas Operativos]
excerpt: En esta entrada se recogen algunos ejercicios básicos que demuestran el uso de los comandos más usados en Debian y otras distribuciones GNU/Linux.
---
En esta entrada se recogen algunos ejercicios básicos que demuestran el uso de los comandos más usados en Debian y otras distribuciones GNU/Linux.

# Ejemplos de uso del comando `ls`

## Listar todos los archivos del directorio bin
Para realizar esta acción hay que acceder en primer  lugar al directorio bin usando `cd /./bin` y, posteriormente, se pueden listar los archivos que contiene con `ls -lh`, paraque muestre el contenido en forma de lista y el tamaño de los archivos en términos más fácilmente comprensibles.

```bash
cd /./bin
ls -lh
```

## Listar todos los archivos del directorio tmp
En este caso, se debe repetir el mismo procedimiento que en el ejercicio anterior sustituyendo el directorio bin por tmp. Primero se accede con `cd /./tmp` y posteriormente se listan los archivos con `ls -lRh`. En este caso, se ha usado también la opción `-R `para listar recursivamente en los directorios dentro del directorio.

```bash
cd /./tmp
ls -lRh
```

## Listar todos los archivos del directorio etc que empiecen por "t" en orden inverso

Al igual que en los casos anteriores, se accede al directorio con `cd /./etc` y después se listan los archivos con ls `-lr t*`. En este caso, la opción `-r` muestra el resultado en orden inverso y el argumento `t*` pide que se listen todos los archivos que empiezan por "t".

```bash
cd /./etc
ls -lr t*
```
## Listar todos los archivos del directorio dev que empiecen por "tty" y tengan 5 caracteres

En este caso, se accede con `cd /./dev` y, posteriormente, se listan los archivos con `ls -lh tty??`. Los signos de interrogación, a diferencia del asterisco, sustituyen a un único carácter cada uno.

```bash
cd /./dev
ls -lh tty??
```

## Listar todos los archivos del directorio dev que empiecen por "tty" y acaben en "1","2","3" ó "4"

En ls -lh tty*[1,2,3,4] el asterisco en este caso indica que se puede sustituir por uno o varios caracteres y la lista recoge todas las opciones de caracteres que se quieren listar como último carácter.

```bash
cd /./dev
ls -lh tty*[1,2,3,4]
```

## Listar todos los archivos del directorio dev que empiecen por "t" y acaben en "C1".

```bash
cd /./dev
ls -lh t*C1
```

## Listar todos los archivos, incluidos los ocultos, del directorio raíz

Con el comando `cd /` se navega hasta el directorio raíz. Una vez allí, con el comando `ls -la` se listan todos sus archivos. La opción -a hace que se muestren también los archivos que empiezan por un ., es decir, los ocultos.

```bash
cd /
ls -la
```

## Listar todos los archivos del directorio etc que no empiecen por "t"

En primer lugar se debe navegar al directorio etc con `cd /./etc` y, después listar los archivos con `ls -l [!t]*`. Para ello, se usa el carácter ! para indicar indicar una cadena de texto en la que el primer carácter sea distinto de "t" y el * para indicar que el resto de caracteres es indiferente.

```bash
cd /etc
ls -l [!t]*
```

## Listar todos los archivos del directorio usr y sus subdirectorios

Para listar los archivos y subdirectorios de usr hay que cambiar al directorio /usr en primer lugar con `cd /usr` y, después, listar los archivos con `ls -lR`. En este caso, la opción `-R` fuerza la búsqueda recursiva dentro de los subdirectorios del directorio actual.

```bash
cd /usr
ls -lR
```

# Mostrar el día y la hora actual

Para mostrar el día y la hora se usa el comando `date`.

# Comandos para navegar entre directorios

## Cambiarse al directorio tmp

Con el comando `cd /tmp` se cambia el directorio actual al directorio tmp.

## Verificar que el directorio actual ha cambiado

Para verificar que el directorio actual ha cambiado se usa el comando `pwd`, que devuelve la ruta del directorio actual, en este caso /tmp en vez de /home/usuario, donde usuario corresponde al nombre de usuario que tiene la sesión iniciada.

```bash
~$ cd /tmp
/tmp$ pwd
/tmp
```

## Con un solo comando posicionarse en el directorio $HOME

Para cambiar al directorio home, se puede usar el comando `cd ~`, que navega del directorio actual al directorio /home/usuario.

## Listar todos los ficheros del directorio HOME mostrando su número de inodo

Para este ejercicio se usa el comando `ls -lia` para listar los archivos. Con la opción `-i` se muestra el número de inodo y con la opción `-a` se muestran los archivos ocultos.

```bash
cd ~
ls -lia
```

##  Crear en vuestro directorio de inicio, un directorio prueba

Para crear un directorio se usa el comando `mkdir` seguido del nombre del directorio.

```bash
mkdir prueba
```

## Crear los directorios dir1, dir2 y dir3 en el directorio prueba. Dentro de dir1 crear el directorio dir11. Dentro del directorio dir3 crear el directorio dir31. Dentro del directorio dir31, crear los directorios dir311 y dir312

En primer lugar, con `mkdir` se crean los directorios dentro de prueba: dir1, dir2 y dir 3 con una lista entre llaves. A continuación, dentro de dir1 se crea dir 11 y dentro de dir3, dir31.

Finalmente, haciendo uso de una nueva lista, se crean los directorios dir311 y dir312 dentro
de dir31.

```bash
mkdir -p prueba/{dir1,dir2,dir3} prueba/dir1/dir11 prueba/dir3/dir31 prueba/dir3/dir31/{dir311,dir312}
```

# Copiar, mover y borrar ficheros

##  Copiar el archivo /etc/motd a un archivo llamado mensaje de nuestro directorio prueba

Para copiar un archivo se usa el comando `cp` con la ruta del archivo que se quiere copiar como primer argumento y la ruta de destino de la copia como segundo.

```bash
cp /etc/mod ~/prueba/mensaje
```

## Copiar el archivo mensaje en dir1, dir2 y dir3

Nuevamente, para copiar el archivo se usa el comando `cp` con la ruta del archivo que se quiere copiar como primer argumento y la ruta de destino de la copia como segundo.

```bash
cp ~/prueba/mensaje ~/prueba/dir1
cp ~/prueba/mensaje ~/prueba/dir2
cp ~/prueba/mensaje ~/prueba/dir3
```

## Copiar los archivos del directorio rc2.d que se encuentra en /etc al directorio dir31

En este caso se usa la opción `-r` del comando `cp` para copiar de forma recursiva todo el contenido del directorio /etc/rc2.d.

```bash
cp -r /etc/rc2.d ~/prueba/dir3/dir31
```

## Copiar en el directorio dir311 los archivos de /bin que tengan una a como segunda letra y su nombre tenga cuatro letras

En este ejemplo se usa la expresión regular [A-z] para indicar que una letra ocupa esa posición en el nombre del archivo.

```bash
cp /bin/[A-z]a[A-z][A-z] ~/prueba/dir3/dir31/dir311
```

## Copiar el directorio de otro usuario y sus subdirectorios debajo de dir11 (incluido el propio directorio)

Para copiar el directorio y subdirectorios de otro usuario se usa la opción `-a` del comando `cp`, que copia recursivamente los subdirectorios dentro del directorio indicado conservando los atributos de los archivos incluidos en ellos.

```bash
cp -a /home/usuario ~/prueba/dir1/dir11
```

## Mover el directorio dir31 y sus subdirectorios debajo de dir2

Para mover un directorio se usa el comando `mv` con la ruta del directorio que se quiere mover como primer argumento y la ruta del directorio destino como segundo argumento.

```bash
mv ~/prueba/dir3/dir31 ~/prueba/dir2
```

## Mostrar por pantalla los archivos ordinarios del directorio HOME y sus subdirectorios

Para mostrar todos los archivos del directorio home y sus subdirectorios se puede usar el comando `find` con la opción `-type` para filtrar la lista por tipo de ficheros y `f` para obtener sólo los ficheros de tipo ordinario.

```bash
find -type f
```

## Ocultar el archivo mensaje del directorio dir3

En GNU/Linux los archivos ocultos son aquellos que empiezan por un ., por tanto, para ocultar el archivo solo hay que renombrarlo con un . delante usando, por ejemplo, el comando `cp`.

```bash
cp ~/prueba/dir3/mensaje ~/prueba/dir3/.mensaje
```

## Borrar los archivos y directorios de dir1, incluido el propio directorio

El comando `rm`, seguido de una ruta de un directorio lo elimina. La opción `-r` hace que se eliminen recursivamente todos los directorios que contiene.

```bash
rm -r ~/prueba/dir1
```

## Copiar al directorio dir312 los ficheros del directorio /dev que empiecen por "t", acaben en una letra que vaya de la "a" a la "b" y tengan cinco letras en su nombre

```bash
cp /dev/t[A-z][A-z][A-z][a-b] ~/prueba/dir3/dir31/dir312
```

## Borrar los archivos de dir312 que no acaben en "b" y tengan una "q" como cuarta letra

```bash
rm ~/prueba/dir3/dir31/dir312/???q*[!b]
```

# Mover el directorio dir312 debajo de dir3

De nuevo, para mover un directorio se usa el comando `mv` con la ruta del directorio que se quiere mover como primer argumento y la ruta del directorio destino como segundo argumento.

```bash
mv ~/prueba/dir3/dir31/dir312 ~/prueba/dir3
```