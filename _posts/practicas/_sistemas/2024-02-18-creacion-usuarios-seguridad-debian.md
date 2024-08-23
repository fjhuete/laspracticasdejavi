---
title:  "Creación de usuarios y políticas de seguridad en Debian"
date:   2024-02-18 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos básicos, comandos, usuarios, seguridad, permisos, ugoa, Implantación de Sistemas Operativos]
excerpt: En este post se explica, a través de varios casos prácticos y ejemplos, el uso adecuado de los comandos básicos para la creación de usuarios y el establecimiento de políticas de seguridad en los sistemas operativos basados en Debian.
---
En este post se explica, a través de varios casos prácticos y ejemplos, el uso adecuado de los comandos básicos para la creación de usuarios y el establecimiento de políticas de seguridad en los sistemas operativos basados en Debian.

# Creación de usuarios y grupos

Para ver el funcionamiento de los comandos para la creación de usuarios y grupos en Debian se crean dos usuarios de nombre externo1 y externo2, que tengan el UID 1200 y UID 1201 respectivamente, tengan como comentario Becarios. Además tendrán como shell /bin/bash y sus propios directorios personales. Ambos deben pertenecer al grupo externos de GID 1300.

Para ello, primero se crea el grupo:

```bash
groupadd -g 1300 externos
```

Y, a continuación, los usuarios:

```bash
useradd -u 1200 -c Becarios -s /bin/bash -m -g 1300 externo1
useradd -u 1201 -c Becarios -s /bin/bash -m -g 1300 externo1
```

Durante la creación de los usuarios externo1 y externo2 no se ha establecido una contraseña para estos usuarios, por tanto, no cuentan con un método de autenticación para acceder al sistema. Para solventar este problema, es necesario generar una contraseña para cada uno de estos usuarios:

```
~$ sudo passwd externo1
New password:
Retype new password:
passwd: password updated succesfully

~$ sudo passwd externo2
New password:
Retype new password:
passwd: password updated succesfully
```

Tras aplicar esta configuración, ambos usuarios pueden acceder al sistema.

Se crea un nuevo grupo.

```bash
groupadd -g 1400 itinerantes
```

Se pueden añadir usuarios a este nuevo grupo modificando el fichero /etc/group añadiendo la siguiente línea:

```
itinerantes:x:1400:externo1
```

# Políticas de seguridad

## Establecer condiciones para el cambio de contraseñas

Con el comando `chage` se pueden establecer condiciones para el cambio de contraseñas de determinados usuarios. En este caso, se modifica la información de cambio de contraseña de externo1 para que no se pueda cambiar la contraseña antes de 10 días y sea obligatorio cambiar la contraseña cada 30 días.

```bash
sudo chage -m 10 -M 30 externo1
```

El mismo resultado se puede conseguir usando el comando `passwd`:

```bash
sudo passwd -n 10 -x 30 externo 1
```

## Verificar la integridad de los ficheros de contaseñas

El comando `pwck` verifica la integridad de los ficheros que recogen la información de autenticación de los usuarios. Este comando comprueba que las entradas de los ficheros /etc/passwd y de /etc/shadow tiene el formato adecuado y contienen información válida. Además, puede eliminar las entradas que no cumplen estos requisitos o que tienen errores incorregibles.

Para el fichero /etc/passwd verifica que cada entrada cuente con el número correcto de campos, un nombre de usuario único y válido y un identificador de grupo y usuario, un grupo primario, un directorio home y una shell válidas. En el fichero /etc/shadow, `pwck` verifica que cada entrada de passwd tenga un entrada correspondiente en el fichero shadow y viceversa, que las contraseñas estén especificadas en el fichero de referencia, que las entradas tengan un número de campos adecuado, que sean entradas únicas y que las fechas de modificación de contraseñas sean correctas.

Las principales opciones del comando `pwck` son `-r`, para ejecutarlo en modo solo lectura; y `-s` para ordenar las entradas por UID.

De la misma forma, el comando `grpck` comprueba la integridad de los grupos. Verifica que todas las entradas de los ficheros /etc/group y /etc/gshadow tienen un formato y contenido correcto. También puede eliminar las entradas incorrectas.

En el fichero /etc/group, el comando `grpck` verifica que los campos contenga el número adecuado de campos, un nombre de grupo único y válido, un identificador y una lista de miembros y administradores válidas y su entrada correspondiente en el fichero /etc/gshadow.

Cuenta con las mismas opciones que el comando `pwck`, como `-r` para mostrar la salida en modo sólo lectura o `-s` para ordenar las entradas en los ficheros por GID.

Estos comandos se pueden emplear, por ejemplo, para comprobar la consistencia de las ficheros de configuración relacionados con usuarios y grupos después de haber estado trabajando en la creación de usuarios y grupos o modificando esta información en el sistema.

## Permisos y control de acceso a directorios

En este ejemplo se crean las carpetas externos e itinerantes. Dichas carpetas pertenecerán a root y al grupo de su nombre. En ellas todos los miembros pertenecientes a cada grupo podrán acceder y escribir en su carpeta. Además, todo objeto creado por un usuario debe pertenecer a su grupo. También se crea la carpeta public y a ella podrán acceder y escribir todos los usuario del sistema pero no podrán borrar objetos que no les pertenezcan.

Para configurar este escenario se debe crear, en primer lugar, un directorio que sea propiedad del root y, dentro de él, los directorios indicados: externos e itinerantes.

```bash
mkdir -p directorios/externos
mkdir -p directorios/itinerantes
```

A continuación, se debe modificar el grupo al que pertenecen cada uno de estos directorios para que sea accesibles a los usuarios que formen parte de estos grupos:

```bash
chgrp -R 1300 externos/
chgrp -R 1400 itinerante/
```

Para permitir acceder y modificar el contenido de su respectivo directorio a los miembros de cada grupo se puede usar un permiso 775 (rwxrwx---) en el directorio. Con este permiso, tanto el usuario como todos los miembros del grupo podrán acceder al directorio así como escribir y borrar los ficheros que contiene.

Para la carpeta pública, a la que todos deben poder acceder y escribir pero no borrar se deben asignar permisos de lectura y ejecución a otros pero no de escritura. Así, los permisos de este directorio deberían ser rwxr-xr-x o 755. Puesto que el directorio es propiedad del usuario y del grupo root, un permiso rwxrwxr-x o 775 tendría el mismo efecto ya que los usuarios que accedan a él no deberían pertenecer tampoco al grupo root y, por tanto, tampoco podrían borrar el contenido con esos permisos. En cualquier caso, el permiso rwxr-xr-x, por defecto para los directorios, otorga un mayor grado de seguridad.

```bash
mkdir -p directorio/publica
```

## Grupos con contraseñas

Con el comando groupmod se puede añadir o modificar la contraseña de un grupo, en este caso se pone la contraseña "itinerantes" al grupo del mismo nombre:

```bash
groupmod -p itinerantes itinerantes
```

El uso de este comando evita que haya que editar los diferentes ficheros de configuración, lo que podría generar inconsistencias en la configuración de usuarios y grupos.

# Ficheros de conexiones

El fichero /etc/issue contiene un mensaje de identificación del sistema que se imprime antes del indicador de login.

Por su parte, /etc/issue.net es un fichero de configuración en el que se puede incluir un mensaje o identificación del sistema que se mostrará justo antes de que aparzca el prompt en una sesión telnet. Puede mostrar diferentes informaciones que se indican en el fichero con el carácter % y una letra que hace referencia a la información que se quiere mostrar. Por ejemplo, %t muestra el tty que se está usando, %D muestra el nombre del dominio, %d muestra la hora y fecha actual, %s muestra el nombre del sistema operativo, %r muestra el número de release del sistema operativo y %v muestra la versión del sistema operativo.

El fichero /etc/motd (message of the day) contiene el texto que se muestra tras un inicio de sesión exitoso antes de que se ejecute el intérprete de órdenes del sistema.

Estos ficheros se pueden utilizar para configurar la información que se muestra a los usuarios al iniciar sesión en su máquina como, por ejemplo, un mensaje de bienvenida, información sobre la situación de la máquina o una información relevante dirigida a los usuarios de un sistema.

Por ejemplo, para avisar a todos los usuarios de un paro por mantenimiento se puede editar el fichero /etc/issue, que muestra el texto que se incluya en él antes de la línea de login al realizar la conexión a una máquina.

Esto se puede hacer usando un editor desde la terminal como nano al añadir el siguiente texto al fichero /etc/issue:

```
Debian GNU/Linux 12 \n \l
El sistema se parará el 19/02/2024 a las 15:00 por mantenimiento.
```

> Con \n se escribe por pantalla el nombre de la máquina.
>
> Con \l se escribe por la máquina el nombre del tty en uso.
{: .notice--primary}

A partir de ese momento todos los usuarios verán este mensaje antes de introducir sus credenciales para el inicio de la sesión en la máquina.

Para particularizar este tipo de mensajes a cada usuario en concreto, la configuración se debe aplicar al fichero oculto .bashrc de cada uno de los usuarios a los que se quiera dirigir. Mientras que los ficheros del directorio etc/ issue, issue.net y motd son accesibles para todos los usuarios que se conectan al sistema, el fichero .bashrc contiene una configuración específica para cada usuario.

Por tanto, para configurar un mensaje personalizado de inicio de sesión para un usuario concreto se debe editar ese mensaje en su fichero .bashrc.

Por último, para incluir estos mensajes de bienvenida en las conexiones por SSH se debe editar el fichero de configuración de este servicio. En primer lugar, para mostrar los mensajes que se recogen en el fichero /etc/issue, que se muestran previos al inicio de sesión, se debe editar la línea banner del fichero de configuración del servicio SSH /etc/ssh/sshd_config para indicar que se quiere mostrar un banner y la ruta al fichero que contiene el texto que debe incluir ese banner:

```
Banner /etc/issue
```

El mensaje del día (motd), también cuenta con su propia línea en el fichero de configuración del SSH. Esta línea aparece comentada por defecto. Si se activa y se cambia la opción de print motd a yes se mostrará el contenido del fichero /etc/motd cada vez que se realice una conexión de forma exitosa a través de SSH:

```
PrintMotd yes
```

Junto al mensaje del día, el fichero de configuración SSH también incluye la posibilidad de mostrar o no la información sobre la última conexión.

En cualquiera de estos casos, tras realizar una modificación en el fichero de configuración del servicio de SSH /etc/ssh/sshd_config se debe reiniciar este servicio:

```bash
systemctl restart ssh.service
```

><i class="fas fa-lightbulb" aria-hidden="true"></i> 
>Para conocer más sobre gestión de permisos y listas de acceso en Debian puedes consultar [este otro post](/sistemas/permisos-ugoa-acl-debian).
{: .notice--primary}