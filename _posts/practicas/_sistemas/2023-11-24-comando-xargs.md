---
title:  "El comando xargs"
date:   2023-11-24 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos básicos, xargs, comandos, Implantación de Sistemas Operativos]
excerpt: El comando xargs permite la construcción y ejecución de líneas de comando desde la entrada estándar. Este comando lee los elementos de la línea de la entrada estándar delimitados por espacios o líneas y ejecuta el comando una o más veces con el argumento inicial seguido de los elementos elementos indicados por teclado.
---

# Función del comando `xargs`

El comando `xargs` permite la construcción y ejecución de líneas de comando desde la entrada estándar. Este comando lee los elementos de la línea de la entrada estándar delimitados por espacios o líneas y ejecuta el comando una o más veces con el argumento inicial seguido de los elementos elementos indicados por teclado.

Si no se indica ningún otro comando, el comando que `xargs` ejecuta por defecto es echo.

```bash
~$ xargs
Hola
esto
es una
prueba
Hola esto es una prueba
```

Tras su ejecución, `xargs` espera una entrada desde el teclado. En este momento se puede introducir una cadena de texto como en el ejemplo anterior pero también un comando que se ejecutará cuando se especifique el fin de la entrada por teclado.

# Aplicación del comando `xargs`

La principal aplicación de `xargs` es facilitar y dinamizar las tareas de la administración del sistema. De esta forma, por ejemplo, se pueden listar los ficheros almacenados en varios directorios con la ejecución de un único comando. Por ejemplo, la ejecución de `xargs ls -l` espera que se introduzcan por teclado los directorios cuyo contenido se quiere listar. Tras indicar los diferentes directorios se indica el fin de la entrada por teclado.

```bash
~$ xargs ls -l
/tmp
./Descargas
./pruebas
```

Como respuesta, `xargs` devuelve la lista del contenido de todos los directorios indicados.

# Opciones más utilizadas del comando `xargs`

Algunas de las opciones más comúnmente utilizadas en la ejecución de xargs son:

- `-0` `--null`. Los nombres de fichero de entrada se terminan con un carácter nulo en lugar de  con espacio  en blanco, y las comillas y barra inversa no son especiales (cada carácter se toma literalmente). Deshabilita el final de la cadena de fin de fichero, que  se trata  como cualquier otro argumento. Es útil cuando los argumentos pueden contener espacio en blanco, comillas o barras invertidas.
- `-e[eof-str]`, `--eof[=eof-str]`. Establece  la  cadena  de fin de fichero a eof-str.  Si la cadena de fin de fichero ocurre como una línea de la entrada, el resto de la entrada  se  descarta. Si esta opción no se da, la cadena de fin de fichero predeterminada es "_".
- `-i[replace-str]`, `--replace[=replace-str]`. Reemplaza ocurrencias de replace-str en los argumentos iniciales con nombres leídos de  la  entrada  estándar.  Además, los blancos no entrecomillados no delimitan los argumentos.
- `-L max-lines`, `-l[max-lines]`, `--max-lines[=max-lines]`. Utiliza  como  mucho  max-lines  líneas  de  entrada no en blanco por cada línea de órdenes; el valor predeterminado de max-lines es 1.
- `-n max-args`, `--max-args=max-args`. Utiliza  como  mucho  max-args  argumentos  por  cada línea de órdenes.
- `-p`, `--interactive`. Pregunta  al  usuario si se debe ejecutar cada línea de órdenes, y lee una línea de la terminal. Sólo ejecuta la línea de órdenes si la respuesta empieza con "y" o "Y" (o quizás el equivalente local, en español "s" o "S").
- `-r`, `--no-run-if-empty`. Si  la  entrada  estándar  no  contiene  algo distinto de blancos, no se ejecuta la orden. Normalmente, la orden se ejecuta una vez incluso si no hay entrada.
- `-s max-chars`, `--max-chars=max-chars`. Utiliza como mucho max-chars caracteres por cada línea de  órdenes,  incluyendo  la orden  y  los  argumentos iniciales, y los nulos terminadores en los finales de las cadenas de argumentos.
- `-t`, `--verbose`. Muestra la línea de órdenes en la salida estándar de errores antes de ejecutarla.
- `-P max-procs`, `--max-procs=max-procs`. Ejecuta hasta  max-procs procesos de una vez; el valor predeterminado es 1. Si max- procs es 0, xargs ejecutará tantos procesos como sea posible de  una  vez.

# Ejemplos de utilización del comando `xargs`

Un ejemplo de uso interesante para el comando `xargs` puede ser hacer búsquedas de diferentes archivos en un mismo directorio sin tener que reescribir todo el comando. Por ejemplo, para buscar los ficheros cuyo nombre empieza por “fichA” o “fichB” en el directorio ./prueba/origen/ podemos ejecutar `xargs -L 1 find -name` y, a continuación, ir introduciendo los criterios de búsqueda, en este caso, `fichA*.txt` en primer lugar y `fichB*.txt` posteriormente. De esta forma, la ejecución del comando devolverá todas las coincidencias con ambas búsquedas en el directorio especificado.

```bash
~$ xargs -L 1 find -name
fichA*.txt
./fichA5.txt
./fichA.txt
./fichA6.txt
./fichA4.txt
./fichA3.txt
./fichA2.txt
./fichA1.txt
fichB*.txt
./fich3.txt
./fichB.txt
./fichB2.txt
./fichB1.txt
```

Otra utilidad interesante de `xargs` puede ser crear ficheros o directorios de forma más rápida y cómoda con una sola línea. Por ejemplo, se puede usar el siguiente comando para crear tres ficheros de nombre A.txt, B.txt y C.txt.

```bash
echo A B C | xargs touch
```

Con `xargs` también se pueden hacer búsquedas amplias con una única orden. Por ejemplo, usando el siguiente comando se pueden buscar todos los ficheros del directorio /etc con extensión .conf que incluyan la palabra root.

```bash
find /etc -iname "*.conf" | xargs grep "root"
```