---
title:  "Comandos para el control de procesos"
date:   2024-03-17 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos básicos, procesos, comandos, ps, top, pidof, killall, disown, renice, Implantación de Sistemas Operativos]
excerpt: Un proceso es la unidad básica de trabajo del sistema operativo. En este post podrás encontrar una lista de comandos con los que controlar los procesos que se llevan a cabo en un equipo, así como sus diferentes opciones y ejemplos de uso.
---
Un proceso es la unidad básica de trabajo del sistema operativo. En este post podrás encontrar una lista de comandos con los que controlar los procesos que se llevan a cabo en un equipo, así como sus diferentes opciones y ejemplos de uso.

# El comando `ps`

## Definición

El comando `ps` muestra información sobre procesos activos. Esta información se muestra de forma estática. Para obtener información actualizada se puede usar el comando top.

Este comando acepta varias opciones. Por una parte, permite usar opciones UNIX, precedidas por un guión y que se pueden agrupar. Por otra, se pueden usar opciones BSD que se pueden agrupar pero no van precedidas de guión. Además, se pueden usar opciones largas GNU, que van precedidas de un doble guión.

Esto hace que opciones similares tengan funcionamiento diferente. Por ejemplo, `ps aux` y `ps -aux` son comandos diferentes. Mientras que `ps -aux` muestra todos los procesos que pertenecen a un usuario llamado x (`-a`: todos los procesos, `-u`: pertenecientes a un usuario, x: nombre del usuario), `ps aux` muestra una lista de todos los procesos activos del sistema.

### Sintaxis

```
ps [opciones]
```

## Opciones
- `a`: Muestra todos los procesos y no sólo los del usuario.
- `-A` / `-e`: Muestra todos los procesos.
- `-a`: Muestra todos los procesos excepto aquellos que no están asociados a una terminal y los líderes de sesión.
- `-d`: Muestra todos los procesos excepto los líderes de sesión.
- `-N` / `--deselect`: Muestra todos los procesos excepto los que cumplen la condición indicada.
- `T`: Muestra todos los procesos relacionados con la terminal vigente.
- `r`: Muestra sólo los procesos en ejecución
- `x`: Muestra los procesos que no tienen una terminal asociada.
- `--pid` / `-p` / `p` / `-<número>` / `<número>`: Indica el número de proceso que se quiere mostrar.
- `-c`: Muestra los procesos asociados a un comando indicado.
- `-G` / `--Group`: Muestra los procesos asociados a un grupo indicado.
- `t` / `-t` / `--tty`: Muestra los procesos asociados a una terminal indicada.
- `-u` / `U` / `--user`: Muestra los procesos asociados a un usuario.
- `-l` / `l`: Muestra la salida en formato largo.

## Ejemplos de uso

El comando ps se puede usar para ver todos los procesos activos en el sistema:

`ps -e` (notación UNIX)

`ps aux` (notación BSD)

También permite mostrar un árbol de procesos:

`ps -ejH` (notación UNIX)

`ps axjf` (notación BSD)

Además, se puede consultar información sobre los hilos de procesos:

`ps -eLf` (notación UNIX)

`ps -axms` (notación BSD)

# El comando `top`

## Definición

El comando `top` Muestra una vista dinámica en tiempo real de los procesos que se ejecutan en el sistema. Además, muestra un resumen de la información del sistema. La información del sistema que muestra `top`, así como el orden y tamaño de la información de los procesos puede configurarse.

### Sintaxis

```
top [opciones]
```

## Opciones
- `-b`: Arranca top en modo batch para enviar la salida a un fichero u otro programa. Se ejecuta durante un número de iteraciones indicado o hasta que se detiene el proceso.
- `-d`: Indica el tiempo de refresco de la información que muestra top.
- `-H`: Muestra cada hilo de forma individual en vez de una suma de todos los hilos de cada proceso.
- `-n`: Muestra el número de iteraciones máximas antes de que se cierre el programa.
- `-p`: Muestra únicamente la información relativa al proceso indicado.
- `-s`: Arranca en modo seguro incluso si lo ejecuta el root. Este modo impide el uso de algunos comandos interactivos.

Además, top permite usar comandos interactivos durante la ejecución del programa como:

- `H`: Muestra todos los hilos de cada uno de los procesos en vez de una suma de ellos para cada proceso.
- `k`: Envía una señal a un proceso.
- `q`: Sale del programa.
- `r`: Modifica la prioridad de un proceso.
- `W`: guarda las opciones de configuración de top a un fichero.

## Ejemplos de uso

El comando `top` se puede usar para mostrar los procesos del sistema:

```
top
```

Se puede indicar el número de lecturas que debe mostrar antes de terminar su ejecución:

```
top -5
```

Y también se puede parar un proceso de manera interactiva desde este programa:

```
top 
k
PID
```

# El comando `htop`

## Definición

El comando `htop` es un visor de procesos similar a top pero que permite desplazarse tanto vertical como horizontalmente a través de la vista, así como interactuar con el ratón. Muestra todos los procesos del sistema junto con sus comandos y también los muestra en modo de árbol. Permite enviar órdenes a los procesos sin indicar su PID.

Además, el paquete htop también incluye `pcp htop`, una versión de `htop` que usa la API Performance Co-Pilot Metrics que permite que `htop` muestre valores de métricas arbitrarias.

### Sintaxis

```
htop [opciones]
```

## Opciones
- `-d`: Indica el tiempo de refresco de la información.
- `-C`: Usa el modo monocromo.
- `-F`: Filtra los procesos que se muestran.
- `-p`: Muestra sólo los procesos indicados.
- `-s`: Ordena la información por la columna indicada.
- `-u`: Muestra sólo los procesos del usuario indicado.
- `-t`: Muestra los procesos en forma de árbol.

Adicionalmente, `htop` ofrece comandos interactivos durante su ejecución para funciones como la navegación a través de la información, el etiquetado de procesos o el filtrado de la información. Los comandos interactivos más relevantes se muestran en la parte inferior de la terminal durante la ejecución del programa.

## Ejemplos de uso

El uso más habitual de `htop` es revisar el uso que los diferentes procesos que se están ejecutando en el equipo hacen de los recursos del sistema.

# El comando `kill`

## Definción

El comando `kill` envía señales a procesos. La señal por defecto es TERM, que termina el proceso. También permite enviar otras señales como `HUP`, `INT`, `KILL`, `STOP` o `CONT`.

### Sintaxis

```
kill [opciones] pid
```

## Opciones

- `-s`: Indica la señal que se debe enviar.
- `-l`: Lista el nombre de las señales.
- `-L`: Lista las señales en forma de tabla.

## Ejemplos de uso

El comando kill se usa para enviar señales a procesos, por ejemplo:
- `kill -9 -1`: envía la señal KILL a todos los procesos que se puedan matar.
- `kill -l 11`: Muestra el nombre de la señal número 11.
- `kill -L`: Muestra la lista de señales que se pueden enviar en forma de tabla.
- `kill 123 456 2615 431`: Envía la señal por defecto (SIGTERM) a los procesos indicados.

# El comando `jobs`

## Definición

El comando `jobs` muestra el estado de los trabajos iniciados en el entorno de shell actual.

### Sintaxis

```
jobs [opciones] [id del trabajo]
```

## Opciones

Este comando sólo tiene cinco opciones:

- `-l`: Muestra más información sobre cada trabajo listado como el número de trabajo, el ID del grupo del proceso, el estado o el comando que ha generado el trabajo.
- `-n`: Sólo muestra los procesos que han cambiado de estado desde la última notificación
- `-p`: Muestra sólo el ID del proceso.
- `-r`: Muestra sólo los trabajos en ejecución.
- `-s`: Muestra sólo los trabajos detenidos.

## Ejemplos de uso

El uso habitual de `jobs` es listar los trabajos que se están desarrollando en un entorno de shell determinado. Para ello se puede usar `jobs` o `jobs -l` para mostrar una información más amplia.

# El comando `nohup`

## Definición

El comando `nohup` hace que otro comando pueda ignorar la señal de cierre durante su ejecución.

### Sintaxis

La sintaxis de `nohup` es: 

```
nohup comando [argumento].
```

Adicionalmente, se puede usar la sintaxis `nohup opción` para usar alguna de las dos opciones de las que dispone el comando.

## Opciones

Las únicas dos opciones que se pueden usar con nohup son:
- `--help`: Muestra la ayuda del comando.
- `--version`: Muestra la versión del comando.

## Ejemplos de uso

El comando `nohup` se suele usar habitualmente para invocar procesos que se deben seguir ejecutando tras el cierre de la terminal desde la que se han invocado. Un caso de uso concreto puede ser descargar un fichero a través de wget:

```
nohup wget URL
```

Además, también permite redirigir la salida de un comando a un fichero. Por ejemplo:

```
nohup find / > búsqueda_sistema.out
```

# El comando `disown`

## Definición

El comando `disown` quita trabajos del shell actual. Para ello, elimina el trabajo correspondiente al ID indicado en el comando de la tabla de trabajos activos de la terminal.

### Sintaxis

```
disown [opción] [idtrabajo | pid]
```

## Opciones

Este comando tiene tres opciones:
- `-a`: Quita todos los trabajos si no se proporciona un ID de trabajo o proceso.
- `-:` Marca cada trabajo para que no se le envíe una señal SIGHUP si el shell se cierra.
- `-r`: Quita sólo los trabajos en ejecución.

## Ejemplos de uso

El comando `disown` se usa para eliminar trabajos del entorno de una terminal. Se suele usar en combinación con el comando `jobs` de manera que para listar los trabajos activos en una terminal se usa `jobs` o `jobs -l` y para eliminar trabajos de esa lista se usa `disown %n`, donde n es el número de trabajo que se desea eliminar o `disown -a` si se quieren eliminar todos los trabajos.

# El comando `nice`

## Definición

El comando `nice` muestra el orden en el que un proceso está en cola para su ejecución en el sistema o ejecuta un comando modificando su prioridad de ejecución. Este comando se puede usar de dos formas: si se ejecuta sin argumentos, muestra el orden de ejecución de los procesos en cola; si se indica un comando como argumento, lo ejecuta con el nivel de prioridad indicado. Por defecto, `nice` incrementa la prioridad de los procesos que ejecuta en 10.

El rango mínimo de prioridad para los procesos va desde el -20 para los procesos más prioritarios hasta el 19 para los procesos con un nivel de prioridad más bajo. 

Sin embargo, el nivel de prioridad que indica el comando `nice` a los procesos no es de obligado cumplimiento para el orden de ejecución de los procesos. Además, `nice` no puede ejecutar comandos que formen parte de las funciones internas de bash.

### Sintaxis

```
nice [opción] [comando]
```

## Opciones

Este comando sólo puede usar una opción:

- `-n`: Indica el nivel de prioridad que se añade o sustrae a la hora de ejecutar el comando indicado. Esta opción va seguida de un operando, que debe ser el número de niveles de prioridad que se modifica en la ejecución del comando indicado a continuación, por defecto, 10. Si el número es positivo, se aumenta la prioridad en la ejecución del comando; si es negativo, se disminuye. Para indicar un ajuste negativo, se debe contar con los privilegios necesarios.

## Ejemplos de uso

El comando `nice` se suele usar para indicar una prioridad en la ejecución de un proceso. Por ejemplo, se puede usar para indicar que la ejecución del proceso de descarga de un fichero no es prioritaria:

```
sudo nice -n -10 wget URL
```

# El comando `renice`

## Definición

Por su parte, el comando `renice` modifica la prioridad de ejecución de un proceso. Este comando acepta dos argumentos: el primero de ellos indica el nivel de prioridad que se asigna a un proceso determinado; el segundo argumento indica el identificador del proceso o de los procesos cuyos niveles de prioridad debe modificar el comando `renice`. 

Para la ejecución de este comando se puede indicar el identificador del proceso, por defecto, pero también el del grupo o el del usuario. También acepta nombres de usuario. Si se usa el identificador de un grupo de procesos se modifica la prioridad de todos los procesos del grupo; si se usa el identificador de usuario o nombre de usuario, la prioridad de todos los procesos que pertenecen a ese usuario se modifica.

### Sintaxis

```
renice [-n] prioridad [-g|-p|-u] identificador
```

## Opciones

Hay dos tipos de opciones que se pueden usar con el comando `renice`:

- Para indicar el nivel de prioridad se usa la opción `-n`. Con `-n` se especifica la prioridad que debe usar el proceso, grupo o usuario. Debe ser el primer argumento.
- En segundo lugar se pueden usar las opciones `-g`, `-p` o `-u` para indicar si el ID corresponde a un grupo de procesos, a un proceso o a un usuario. 
- Además, este comando acepta las opciones `-h` y `-V` para mostrar la ayuda y la versión del comando.

## Ejemplos de uso

El uso de `renice` es muy similar al de `nice`. Siguiendo con el ejemplo anterior y suponiendo que el PID del proceso iniciado es el 1735, se puede alterar la prioridad de ejecución de un proceso como la descarga de un fichero si se necesita aumentar su prioridad:

```
renice -n 10 -p 1735
```

# El comando `killall`

## Definición

El comando `killall` envía una señal a todos los procesos que ejecutan un comando determinado. Las señales se pueden especificar tanto pro nombre como por su código numérico. Este proceso nunca se termina a sí mismo.

### Sintaxis

```
killall [opciones] nombre_proceso
```

## Opciones

Este comando acepta una gran variedad de opciones. Algunas de las más relevantes son:

- `-e`: Indica que la coincidencia con el nombre debe ser exacta.
- `-I`: Ignora la diferencia entre mayúsculas y minúsculas en la cadena de búsqueda.
- `-g`: Envía la señal al grupo de procesos al que pertenece el proceso indicado. La señal sólo se envía una vez por grupo aunque haya varios procesos que pertenezcan al mismo grupo.
- `-i`: Modo interactivo. Pide confirmación antes de enviar la señal a cada proceso.
- `-l`: Muestra una lista con todas las señales que se pueden enviar a los procesos.
- `-n`: Tiene en cuenta los espacios en la cadena de búsqueda.
- `-o`: Considera coincidencia sólo los procesos que comenzaron antes de un período de tiempo indicado. El período de tiempo se puede indicar como un decimal seguido de las unidades s, m, h, d, w, M, y para segundos, minutos, horas, días, semanas, meses y años respectivamente.
- `-r`: Interpreta la cadena de búsqueda como una expresión regular.
- `-s`: Indica la señal que se debe enviar al proceso o a los procesos indicados.
- `-u`: Envía la señal sólo a los procesos que sean propiedad del usuario especificado. En este caso no es necesario indicar un nombre de proceso. Si no se indica un nombre de proceso, envía la señal a todos los procesos que pertenecen al usuario.
- `-w`: Espera hasta que los procesos a los que se ha enviado una señal terminen. Verifica si se han terminado los procesos indicados una vez por segundo. 
- `-y`: Considera coincidencia sólo los procesos que comenzaron después de un período de tiempo indicado. El período de tiempo se puede indicar como un decimal seguido de las unidades s, m, h, d, w, M, y para segundos, minutos, horas, días, semanas, meses y años respectivamente.
- `-Z`: Envía la señal sólo a los procesos que tienen un contexto de seguridad que se corresponde con una expresión regular determinada.

## Ejemplos de uso

Con las opciones indicadas, existen varios ejemplos de uso del comando `killall`:

`killall -l` muestra la lista de señales que se pueden enviar.

`killall -s STOP wget `envía una señal para parar al proceso que depende del comando wget.

`killall -y 20m` envía una señal para terminar a todos los procesos iniciados en los últimos 20 minutos.

`killall -u debian` envía una señal para terminar a todos los procesos del usuario debian.

# El comando `pidof`

## Definición

El comando `pidof` busca el ID del proceso que corresponde a un programa en ejecución. Este comando devuelve el PID de un proceso a partir de su nombre. 

### Sintaxis

```
pidof [opciones] programa
```

## Opciones

Algunas de las opciones más relevantes del comando pidof son:

- `-s`: Devuelve sólo un ID de proceso.
- `-c`: Sólo devuelve el ID de los procesos que se estén ejecutando en el mismo directorio de root. Esta opción no se puede usar desde usuarios que no sean root.
- `-q`: No devuelve los ID de los procesos, sino que simplemente indica con un valor booleano si la búsqueda ha devuelto algún resultado o no.
- `-x`: Devuelve también el ID del proceso de la shell que ejecuta un script determinado.
- `-z`: Intenta detectar procesos que estén en estado zombi. Habitualmente estos procesos se ignoran porque pueden provocar que herramientas como pidof se cuelguen.
- `-d`: Indica un separador en la salida si devuelve más de un ID de proceso. El separador por defecto es el espacio.
- `-o`: Omite el proceso con el ID indicado. Se puede usar para ignorar el ID del proceso padre desde el que se ejecuta el comando, ya sea una shell o un script.

## Ejemplos de uso

El uso habitual de este comando es encontrar el ID que corresponde a un proceso determinado. Por ejemplo `pidof bash`.

# El comando `bg`

## Definición

El comando `bg` mueve trabajos al segundo plano. Este comando coloca los trabajos cuyo ID se indique como si se hubieran iniciado con `&`.

### Sintaxis

```
bg [id_trabajo]
```

## Opciones

Este comando no tiene opciones.

## Ejemplos de uso

El uso habitual de `bg` es mover trabajos al segundo plano. Por ejemplo, en un entorno de shell en el que se está llevando a cabo una actualización de la paquetería del sistema con `apt upgrade -y` y este trabajo tiene el identificador 1, se puede mover al segundo plano con `bg 1` para poder seguir haciendo uso de la terminal mientras el trabajo se desarrolla.

# El comando `fg`

## Definición

El comando `fg` mueve un trabajo al primer plano. Este comando ubica el trabajo  cuyo ID se indique en el primer plano y lo hace el trabajo actual.

### Sintaxis

```
bg [id_trabajo]
```

## Opciones

Este comando no tiene opciones.

## Ejemplos de uso

El uso habitual de `fg` es mover trabajos al primer plano. Por ejemplo, si en un entorno de shell se está llevando a cabo una actualización de la paquetería del sistema con `apt upgrade` en segundo plano y este trabajo tiene el identificador 1, se puede mover al segundo plano con `fg 1` para interactuar con el comando cuando pida confirmación para la instalación o actualización de un determinado paquete.