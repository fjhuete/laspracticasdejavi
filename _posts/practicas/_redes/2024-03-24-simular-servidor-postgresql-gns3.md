---
title:  "Cómo simular un servidor PostgreSQL en GNS3"
date:   2024-03-24 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, servidores, postgresql, bases de datos]
excerpt: PostgreSQL es uno de los servidores de bases de datos más usados. Se trata de un software libre y portente que permite almacenar y gestionar grandes volúmenes de información. En este post se explica cómo simularlo en un escenario de GNS3.
toc: false
---
Para simular un servidor PostgreSQL en GNS3 se necesita una máquina, en este caso Debian 11, con conexión a Internet a través de la nube NAT del simulador.

Tras conectar la máquina a la nube NAT y solicitar una configuración IP a través de DHCP, se pueden actualizar los paquetes del equipo y, una vez que se ha actualizado el sistema, se puede instalar el servidor Postgre.

```bash
sudo dhclient
sudo apt update && sudo apt upgrade
sudo apt install postgresql
```

Tras instalar PostgreSQL y comprobar que el servicio se ha ejecutado con éxito usando el comando `systemctl status postgresql`, es necesario habilitar el acceso remoto para que los PC conectados a otras redes locales puedan llegar a él. Para ello, se deben modificar dos ficheros de configuración. El primero de ellos es el fichero postgresql.conf, en el que se debe indicar que el servidor debe escuchar en el puerto 5432 (por defecto) y de todas las direcciones IP (no sólo de localhost, por defecto) modificando en la línea listen_addresses  el valor ‘localhost’ por ‘*’.

![](/assets/img/redes/practica13/img2.png)

Además, para permitir el acceso al servidor desde ubicaciones diferentes a la red local es necesario añadir las dos líneas que se muestran en la siguiente captura del fichero pg_hba.conf. Con estas líneas se permite la conexión desde cualquier dirección IP usando una contraseña encriptada con el método md5.

Una forma de hacer más segura esta configuración sería usar la IP pública del router Casa, la 100.40.0.2/24 en la columna dirección, en vez de la 0.0.0.0/0. Esto haría que sólo los dispositivos que se conectan al servidor desde esa IP puedan acceder a él. Además, también se puede indicar en la segunda columna las bases de datos y en la tercera los usuarios a los que se permite el acceso.

De esta manera, la configuración más segura para este documento sería indicar, en primer lugar, el método de conexión (host), a continuación el nombre de la base de datos a la que se va a permitir la conexión, el usuario al que se le va a permitir la conexión a esa base de datos y la IP pública que va a usar ese usuario para realizar la conexión. En la última columna se indica el método de encriptación que usa la contraseña del usuario u otras opciones de autenticación menos seguras como ‘trust’ que permiten la conexión sin contraseña.

![](/assets/img/redes/practica13/img3.png)

Finalmente, se le debe asignar su IP privada a la máquina que aloja este servidor a través del fichero de configuración de red /etc/network/interfaces.

Para la conexión remota se usa el siguiente comando desde el equipo cliente:

```bash
psql -h IP_servidor -U usuario -d BaseDeDatos
```