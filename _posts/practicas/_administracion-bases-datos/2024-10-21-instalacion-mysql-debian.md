---
title:  "Instalación de MySQL en Debian"
date:   2024-10-21 01:00:00 +0200
categories: administracion-bbdd
tag: [bases de datos, mysql, Debian 12, bookworm, instalacion, Oracle, administracion-bbdd, Administración de Sistemas Gestores de Bases de Datos]
excerpt: El proceso de instalación de MySQL difiera un poco del que se sigue para instalar MariaDB en Debian. En este post se desarrolla una guía con los pasos a seguir
---

# Instalación MySQL en Debian 12

En teoría MySQL se puede instalar de forma directa desde los repositorios de Debian. Sin embargo, el gestor de paquetes apt no tiene acceso al paquete mysql-server a través del repositorio de la rama estable (bookworm) de Debian. Oracle ofrece un paquete .deb instalable en su web de descargas, que hay que configurar para que el gestor de paquetes apt pueda instalar la versión deseada de MySQL. El paquete que ofrece Oracle instala tanto el servidor como el cliente en la misma máquina.

```
usuario@mysql:~$ wget https://dev.mysql.com/get/mysql-apt-config_0.8.32-1_all.deb
usuario@mysql:~$ sudo dpkg -i mysql-apt-config_0.8.32-1_all.deb
```
 
Al ejecutar la instalación comienza un proceso interactivo en el que se deben elegir las diferentes opciones de configuración del paquete. En primer lugar, hay que indicar el producto que se va a configurar durante la instalación, en este caso, el servidor MySQL. Posteriormente, hay que indicar la versión para la instalación. En este caso se elige la versión 8.4, que es la versión estable con soporte a largo plazo actualmente. 

Tras configurar estas dos opciones en el paquete, hay que salir del menú interactivo y ejecutar una actualización de la paquetería del sistema. Posteriormente se instala el paquete mysql-server con el gestor de paquetes apt.

```
usuario@mysql:~$ sudo apt update
usuario@mysql:~$ sudo apt install mysql-server
```

Este proceso de instalación también es interactivo. En él hay que indicar la contraseña del usuario root del SGBD. 
La configuración del acceso remoto a este servidor se realiza en el fichero /etc/mysql/mysql.conf.d/mysqld.cnf añadiendo la siguiente línea.

```
bind-address    = 0.0.0.0
```

En versiones posteriores a la 8.0, esta línea no aparece por defecto en el fichero de configuración y se debe añadir dentro de la sección [mysqld]. Para hacer efectiva la configuración se reinicia el servicio.

Mientras que Postgres permite especificar la conexión remota desde una IP concreta o desde una red, el fichero de configuración de MySQL sólo permite indicar si la conexión será desde la máquina local o desde cualquier otra IP. 

Para evitar el acceso de máquinas que no estén conectadas a la red local es necesario añadir una regla de cortafuegos en el servidor.

```
usuario@mysql:~$ sudo iptables -A INPUT -s 192.168.122.0/24 -p tcp --destination-port 3306 -j ACCEPT
usuario@mysql:~$ sudo iptables -A INPUT -p tcp --destination-port 3306 -j DROP
```

Con la primera regla se permite el tráfico dirigido al puerto 3306, por el que escucha MySQL siempre que la IP de destino sea una IP de la red local. Con la siguiente regla se impide el paso de cualquier otra comunicación dirigida a ese puerto.
Para hacer persistente esta configuración se usa el paquete `iptables-persistent`.

```
root@mysql:~$ iptables-save >> /etc/iptables/rules.v4
```

Con esta configuración cualquier cliente de la red local puede acceder de forma remota al servidor MySQL instalado en esta máquina. Para ello, el cliente necesita una herramienta que le permita conectarse al servidor como, por ejemplo, mysql-client. Para instalarlo hay que seguir un procedimiento similar al de la instalación del servidor. En primer lugar, se descarga e instala el paquete para la configuración del respositorio de MySQL, disponible en su web de descargas. Posteriormente, se actualiza la paquetería del sistema y, por último, se instala el paquete mysql-cliente, que cuenta con la herramienta cliente para acceder de manera remota al servidor MySQL.

```
usuario@clientesBBDD:~$ wget https://dev.mysql.com/get/mysql-apt-config_0.8.32-1_all.deb
usuario@clientesBBDD:~$ sudo dpkg -i mysql-apt-config_0.8.32-1_all.deb
usuario@clientesBBDD:~$ sudo apt update
usuario@clientesBBDD:~$ sudo apt install mysql-client
```

Es posible que durante la instalación del cliente, como ocurre en el caso del servidor, haya que instalar algunas dependencias de forma sencilla con el gestor de paquetes APT. Finalmente, tras concluir el proceso, el cliente puede acceder de forma remota al servidor.

```
usuario@clientesBBDD:~$ mysql -h 192.168.122.47 -u usuario -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 15
Server version: 8.4.2 MySQL Community Server - GPL

Copyright (c) 2000, 2024, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```