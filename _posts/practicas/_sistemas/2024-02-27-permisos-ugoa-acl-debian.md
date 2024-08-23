---
title:  "Gestión de permisos UGOA y ACL en Debian"
date:   2024-02-27 01:00:00 +0200
categories: sistemas
tag: [sistemas, debian, linux, comandos básicos, comandos, usuarios, seguridad, permisos, ugoa, ACL, Implantación de Sistemas Operativos]
excerpt: El propósito de este post es demostrar el funcionamiento de los permisos UGOA y las listas de control de acceso (ACL) en Debian a través de ejemplos y casos prácticos.
---
El propósito de este post es demostrar el funcionamiento de los permisos UGOA y las listas de control de acceso (ACL) en Debian a través de ejemplos y casos prácticos.

><i class="fas fa-exclamation-circle" aria-hidden="true"></i> Antes de leer este post es recomandable tener conocimientos sobre la [creación de usuarios y la gestión de políticas de seguridad en Debian](/sistemas/creacion-usuarios-seguridad-debian).
{: .notice--primary}

# Permisos UGOA

Para mostrar el funcionamiento de los permisos UGOA primero se crean varios grupos de usuarios:

```bash
sudo groupadd -g 1100 sistemas
sudo groupadd -g 1200 desarrollo
sudo groupadd -g 1300 explotación
```

También se crea una estructura de directorios:

```bash
sudo mkdir -p /proyecto/{sistemas,desarrollo,expliotacion}
```

Estos directorios corresponden al usuario root y cada uno de ellos pertenecen al grupo homónimo:

```bash
sudo chown root:sistemas /proyecto/sistemas/
sudo chown root:desarrollo /proyecto/desarrollo/
sudo chown root:explotacion /proyecto/explotacion/
```

Estos directorios deben tener permiso de lectura, escritura y ejecución para usuario y grupo y ninguno para otros:

```bash
sudo chmod ug=rwx,o= /proyecto/{sistemas,desarrollo,expliotacion}
```

Para comprobar el funcionamiento de la configuración de permisos que se muestra en este post se crean dos usuarios para cada grupo:

```bash
sudo sudo useradd -g 1100 -u 1101 -p usuario -d /proyecto/sistema/ sistemas1
sudo sudo useradd -g 1100 -u 1102 -p usuario -d /proyecto/sistema/ sistemas2
sudo sudo useradd -g 1200 -u 1201 -p usuario -d /proyecto/desarrollo/ desarrollo1
sudo sudo useradd -g 1200 -u 1202 -p usuario -d /proyecto/desarrollo/ desarrollo2
sudo sudo useradd -g 1300 -u 1301 -p usuario -d /proyecto/explotacion/ explotacion1
sudo sudo useradd -g 1300 -u 1302 -p usuario -d /proyecto/explotacion/ explotacion2
```

Con esta configuración de permisos cada usuario sólo puede acceder a su directorio de trabajo y no al resto. Para crear un escenario más complejo que permita mostrar más opciones en la gestión de permisos vamos a suponer que los usuarios de sistemas deben tener acceso de lectura, escritura y ejecución al directorio sistemas, desarrollo y explotación; los usuarios de desarrollo deben tener acceso de lectura, escritura y ejecución al directorio desarrollo y explotación; y los usuarios de explotación deben tener acceso de lectura, escritura y ejecución al directorio explotación únicamente.

Para que los usuarios del grupo sistema puedan acceder a los directorios desarrollo y explotación se deben incorporar a los grupos que tienen permisos sobre esos directorios como sus grupos secundarios con el comando `groupmod`:

```bash
sudo groupmod -U sistemas1 -a desarrollo
sudo groupmod -U sistemas1 -a explotacion
sudo groupmod -U sistemas2 -a desarrollo
sudo groupmod -U sistemas2 -a explotacion
```

Igualmente, para que los usuarios del grupo desarrollo puedan acceder también al directorio explotación se les debe asignar el grupo explotación como secundario para que puedan disfrutar también de sus permisos:

```bash
sudo groupmod -U desarrollo1 -a explotacion
sudo groupmod -U desarrollo2 -a explotacion
```

Y, tras aplicar estos cambios, los usuarios del grupo sistemas pueden acceder a los tres directorios, los del grupo desarrollo pueden acceder tanto al directorio desarrollo como explotación pero no a sistemas y, finalmente, los usuarios del grupo explotación sólo pueden acceder al directorio explotación.

Otra forma de conseguir que usuarios de los grupos sistemas y desarrollo accedan a directorios que pertenecen a otros grupos es asignar a esos grupos una contraseña. De esta manera, usando el comando `sg`, los usuarios que lo necesiten podrían cambiar su identidad a miembros de estos grupos para realizar acciones sobre los directorios especificados.

La asignación de grupos secundarios es una fórmula que permite menos interacción por parte del usuario que, simplemente, sabrá que puede acceder a esos directorios en los que necesita trabajar. El uso de contraseñas para los grupos, en cambio, obliga al usuario a tener un cierto conocimiento del esquema del sistema en el que está trabajando y puede dificultar el acceso a los contenidos alojados en algunos de estos directorios en momentos determinados, por eso, en este caso, se ha optado por la primera opción.

# Listas de control de acceso (ACL)

Para demostrar el funcionamiento de las ACL se crea un nuevo grupo con dos nuevos usuarios que deben tener los mismos permisos que los usuarios del grupo sistema pero, esta vez, la configuración se realizará usando ACL en lugar de configurando los permisos UGOA.

Primero se crea el grupo y el directorio de los operadores y los dos usuarios: 

```bash
#Creación del grupo
sudo groupadd -g 1400 operadores

#Creación del directorio
sudo mkdir -p /proyecto/operadores/

#Creación de los usuarios
sudo useradd -g 1400 -u 1401 -d /proyecto/operadores/ -s /bin/bash operador1
sudo useradd -g 1400 -u 1402 -d /proyecto/operadores/ -s /bin/bash operador2
```

Para otorgar permisos al grupo operadores se usan las ACL. Con el comando `setfacl` y la opción `-m` se asigna al grupo operadores permiso de lectura, escritura y ejecución sobre cada uno de los directorios sobre los que tienen estos permisos los usuarios del grupo sistemas.

Además, con la opción `-R` la ACL se hace recursiva para que afecte a todos los subdirectorios dentro de cada uno de estos directorios y con `-d` se convierte en una ACL por defecto, de manera que se aplicará a todos los directorios y ficheros que se creen dentro de cada uno de los directorios sistemas, desarrollo y explotación.
 
Así, los operadores cuentan con los mismos permisos que los usuarios del grupo sistemas:

```bash
sudo setfacl -Rdm g:opeardores:rwx /proyecto/sistemas/ /proyecto/desarrollo/ /proyecto/explotacion/
```

Con esta configuración, los usuarios del grupo operadores cuentan con los mismos permisos que los del grupo sistemas. La configuración de ACL se puede consultar con el comando `getacl` seguido de la ruta al direcctorio cuyas ACL se quieran conocer.