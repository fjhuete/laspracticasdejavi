---
title:  "Comandos para la programación de tareas"
date:   2024-01-07 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos básicos, programación, sleep, watch, crontab, comandos, Implantación de Sistemas Operativos]
excerpt: La programación de tareas es una característica muy útil en el ámbito de la administración de sistemas informáticos, especialmente al programar scripts. En este post se repasa brevemente la información más relevante sobre los principales comandos que se pueden usar con esta finalidad.
---

La programación de tareas es una característica muy útil en el ámbito de la administración de sistemas informáticos, especialmente al programar scripts. En este post se repasa brevemente la información más relevante sobre los principales comandos que se pueden usar con esta finalidad.

# Sleep

## Definición

La ejecución del comando `sleep` provoca una pausa de un tiempo determinado en el flujo de trabajo de la terminal. La duración de la pausa se indica a través de los argumentos que deben ser números. Si hay varios argumentos, la pausa será igual a la suma de todos ellos. Las opciones determinan la unidad de tiempo a la que se refieren los argumentos.

###  Sintaxis

```
sleep NÚMERO [SUFIJO]
```

> Número: cantidad de tiempo que debe durar la pausa 
>
> Sufijo: unidad de tiempo
{: .notice}

## Opciones
El comando `sleep` tiene un funcionamiento sencillo y solo admite números (no es necesario que sean enteros) como argumentos y unidades de medida de tiempo como opciones. Las opciones son:

- s: segundos (por defecto).
- m: minutos.
- h: horas.
- d: días.

## Ejemplos de uso

El uso del comando `sleep` está especialmente indicado para el trabajo con scripts o para la creación de tareas complejas en una única línea de comandos (one line).

Por ejemplo, la linea de comandos 

```
sudo apt update; sleep 5; sudo apt upgrade 
```

ejecutará el comando `update`, hará una pausa de 5 segundos y, a continuación, ejecutará el comando `upgrade`.

# Watch

## Definición

El comando `watch` ejecuta otro comando de forma reiterada y muestra por pantalla la salida y los errores del comando indicado. El objetivo de `watch` es mostrar la evolución del resultado de la ejecución de un comando a lo largo del tiempo. La frecuencia de repetición de la ejecución del comando de `watch` por defecto es de 2 segundos y ejecutará el comando reiteradamente hasta que se interrumpa su ejecución.

### Sintaxis

```
watch [opciones] comandos
```

## Opciones

Algunas de las principales opciones del comando `watch` son:

- `-d`, `--differences[=permanent]`: Resalta las diferencias entre las diferentes actualizaciones de la ejecución del comando. Si se usa el argumento “permanent”, `watch` mostrará todos los cambios desde la primera iteración.
- `-`n, `--interval` segundos: Indica el intervalo de actualización. 
- `-p`, `--precise`: Hace que el intervalo de ejecución del comando sea más preciso a nivel de fracción de segundo.
- `-t`, `--no-title`: Deshabilita el encabezado que muestra el intervalo de actualización, el comando y la hora de la última actualización en la parte superior de la pantalla.
- `-b`, `--beep`: Activa un aviso sonoro si se produce algún error en la ejecución del comando.
- `-e`, `--errexit`: Pausa la ejecución del comando si se produce un error.
- `-g`, `--chgexit`: Sale de la ejecución si hay un cambio en la salida del comando.
- `-q`, `--equexit <cycles>`: Sale de la ejecución cuando la salida del comando no cambia tras un número determinado de iteraciones.
- `-c`, `--color`: Interpreta colores y estilos ANSI.
- `-w`, `--no-wrap`: Trunca las líneas largas en la salida del comando en vez de mostrar toda la información en varias líneas.

## Ejemplos de uso

El comando `watch` puede resultar muy útil en tareas de monitorización en las que se necesita mantener una vigilancia sobre la ejecución de otros comandos. También es un comando práctico para alertar de posibles errores, ya que cuenta con varias opciones para detectar y notificar cambios o errores en la ejecución del comando indicado.

Por ejemplo, con la ejecución del comando `watch -d  ls -l` se pueden observar los cambios a lo largo del tiempo en el contenido del directorio en el que se ejecute el comando. Con la opción `-d` se pueden observar resaltados los cambios en la salida de ls entre cada iteración.

# At

## Definición

El comando `at` acepta órdenes a través de la entrada por teclado o desde un fichero y las ejecuta en un momento posterior. `At` conlleva varios comandos asociados. Por ejemplo, con `at` se pueden ejecutar órdenes a una hora determinada, con atq se listan los trabajos pendientes, con atrm se borran trabajos de la lista de tareas pendientes y con batch se ejecutan órdenes cuando lo permite el nivel de carga del sistema.

El comando `at` permite indicar especificaciones de tiempo complejas usando el estándar POSIX.2. Acepta el formato de hora HH:MM pero también expresiones como “midnight”, “noon” o “teatime”. También acepta sufijos como AM o PM, así como los nombres de los meses. También se puede indicar un período de tiempo con “now +” un número de minutos, horas, días o semanas. Además, el comando `at` también acepta expresiones como “today” o “tomorrow”.

### Sintaxis

```
at tiempo
at -f fichero -t tiempo
```

## Opciones

Algunas de las opciones más empleadas junto al comando at son:
- `-V`: Muestra el número de versión en la salida de error estándar y termina.
- `-q cola`: Permite especificar la cola a la que se quiere añadir una tarea. Las colas se nombran con una letra de la a a la z y de la A a la Z. La cola a es la que se usa por defecto para at y la cola b para batch. Además, existe una cola especial, "=", que está reservada para trabajos en ejecución.
Si se usa la opción `-q` con el comando atq, sólo mostrará aquellas tareas que se encuentren en la cola indicada.
- `-m`: Envía un correo al usuario cuando el trabajo termina incluso si no hubiese salida que mostrar.
- `-M`: No envía ningún correo al usuario.
- `-u` username: Indica el usuario al que se debe enviar el correo en vez de al usuario que ejecuta el comando.
- `-f fichero`:  Indica el fichero desde el que se lee el trabajo en lugar de la entrada estándar.
- `-t time`: Indica la hora a la que se ejecuta la tarea con el formato [[CC]YY]MMDDhhmm[.ss]
- `-l`: Es un alias para atq.
- `-r`: Es un alias para atrm.
- `-d`: Es un alias para atrm.
- `-b`: Es un alias para batch.
- `-v`: Muestra la hora a la que se ejecutará la tarea antes de leerla en el formato "Thu Feb 20 14:50:00 1997".
- `-c`: Manda las tareas listadas en la línea de órdenes a la salida estándar.
- `-o fmt`: Usa el formato strftime-like para la lista de tareas.

## Ejemplos de uso

Con todas sus opciones, herramientas y potencial, el comando `at` es uno de los más completos a la hora de gestionar la programación de tareas en GNU/Linux. `At` otorga una gran flexibilidad a los administradores de sistemas para organizar el flujo de trabajo de la forma más eficiente posible postergando la ejecución de tareas, ya sean comandos concretos o scripts más complejos, para momentos posteriores.

Por ejemplo, con la ejecución del comando 

```
at midnight
at> sudo apt update && sudo apt upgrade
```

se actualizarán los paquetes del sistema a las 12 de la noche.

# Crontab

## Definición

`Crontab` es el programa para la instalación, desinstalación, listado y matenimiento de los ficheros para los usuarios del daemon cron en Vixie Cron.
Cron es un programa tipo daemon para ejecutar comandos programados.

Cron usa los ficheros almacenados en el directorio /var/spool/cron/crontabs y /etc/crontab. Estos ficheros, junto al resto de ficheros usados por cron en su funcionamiento no se deben acceder ni editar de forma manual, sino que es necesario usar el comando `crontab` para trabajar con ellos.

Los ficheros crontab recogen las tareas que debe ejecutar el daemon cron. Cada tarea se debe definir como una única línea en el fichero indicando con diferentes campos cuándo se debe ejecutar y qué comando se debe utilizar en la tarea. Para definir la hora se deben usar valores concretos (en este orden) de minutos, horas, día del mes, mes, y día de la semana o usar un asterisco (*) para indicar cualquier valor posible de ese campo.

Además, `crontab` permite configurar ficheros como /etc/cron.allow o /etc/cron.deny, que recogen la lista de usuarios con o sin permiso para usar la programación de comandos a través de Cron.
Cuando los ficheros crontab se han instalado, cron ya cuenta con la información necesaria para funcionar. Entonces, comprueba en los ficheros crontab en el sistema cada minuto si hay algún comando que se deba ejecutar en ese minuto. Si lo hay, lo ejecuta y envía la salida del comando por correo electrónico al propietario del fichero crontab.

### Sintaxis

```
Crontab OPCIÓN
```

## Opciones

Algunas de las principales opciones que se pueden usar con el comando crontab son:

- `-u`: Especifica el nombre de usuario cuyo crontab se va a usar o modificar. Por defecto, crontab trabaja sobre el fichero del usuario activo.
- `-n`: Examina el fichero crontab del usuario para revisar la sintaxis y muestra un mensaje si la sintaxis es correcta pero no efectúa ningún cambio en los ficheros crontab.
- `-l`: Muestra el fichero crontab del usuario por pantalla. También se puede redireccionar la salida a un fichero.
- `-r`: Elimina el fichero crontab.
- `-e`: Permite editar el fichero crontab a través de un editor. Al salir del editor, el crontab se instalará automáticamente.
- `-i`: Muestra una pregunta que espera confirmación antes de eliminar un fichero crontab.

## Ejemplos de uso

El comando `crontab` se puede usar para la gestión de tareas programadas. Una de las ventajas de este comando es que permite la programación de tareas periódicas y complejas mediante la edición e instalación de los ficheros crontab que pueden contener una gran cantidad de información. `Crontab` es muy útil, por ejemplo, para realizar tareas de mantenimiento de los sistema que se deban ejecutar de manera sistemática a lo largo del tiempo.

Por ejemplo, añadiendio la tarea 

```
0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
```

al fichero crontab se puede ejecutar un backup de todas las cuentas de usuario a las 5 de la mañana una vez por semana.