---
title:  "Cómo simular un servidor web en GNS3"
date:   2024-03-03 01:00:00 +0200
categories: redes
tag: [Wireshark, GNS3, redes, Planificación y Administración de Redes, servidor web, nginx, apache]
---
Existen varias formas de crear un servidor web en un escenario de GNS3. En este post se optará por añadir una máquina Linux con Debian 11 en la que se instalará el servidor Nginx para que pueda actuar a forma de servidor web en el escenario.

# Añadir una máquina Debian al escenario

Para crear un servidor web en GNS3 es necesario incluir una máquina que pueda cumplir esta función. En este caso se usará una máquina con Debian 11. Para que esta máquina se pueda usar en el proyecto es necesario importar la plantilla. Tal y como ocurre en el caso del router que se ha instalado también para esta práctica, el procedimiento comienza pulsando el botón new template.

![](/assets/img/redes/practica12/img2.png)

Esto abre una ventana en la que se puede elegir cómo importar la plantilla, en este caso, desde el servidor de GNS3.

![](/assets/img/redes/practica12/img5.png)

En la lista con todas las opciones disponibles se busca y elige la máquina Debian.

![](/assets/img/redes/practica12/img6.png)

A continuación se carga la imagen desde la carpeta Images/QEMU que se genera durante la instalación de GNS3 y ya se puede proceder a la importación de esta plantilla.

![](/assets/img/redes/practica12/img7.png)

# Instalación y configuración del servidor web

Para instalar el servidor web, ya sea Nginx o Apache, en la máquina Debian 11 del escenario de GNS3 es necesario que se pueda conectar a Internet. Con este objetivo se puede añadir de forma provisional una nube NAT al escenario que permita la conexión entra la máquina Debian e Internet, de manera que se puedan actualizar los paquetes instalados y descargar nuevos.

Una vez que la máquina está incorporada al escenario y conectada a la nube nat, se puede arrancar y acceder a ella. Lo primero que se debe hacer es establecer una configuración TCP/IP que le permita conectarse a Internet. En este caso, como la nube NAT cuenta con un servidor DHCP se puede hacer usando el comando dhclient:

```bash
sudo dhclient
```

Con la conexión a Internet configurada y establecida es necesario actualizar la paquetería de la máquina antes de proceder a instalar cualquier paquete nuevo.

```bash
sudo apt update && sudo apt upgrade
sudo apt install nginx
```

Con el sistema actualizado, se puede instalar el servidor web elegido, por ejemplo, Nginx

```bash
sudo apt install nginx
```
o Apache

```bash
sudo apt install apache2
```

Para comprobar que tras el proceso de instalación el servicio de Nginx o Apache se arranca de forma automática se puede ejecutar el comando correspondiente

```bash
sudo systemctl status nginx.service
sudo systemctl status apache2.service
```

Si no se ha arrancado, se puede hacer con el comando

```bash
sudo systemctl start nginx
sudo systemctl start apache2
```

Una vez que se ha Nginx está funcionando en el servidor se debe configurar la dirección IP estática de esta máquina a la que el resto de ordenadores deberá acceder para consultar el contenido alojado en él. En este caso se usa la IP 10.0.13.2. Esta información se debe indicar en el fichero de configuración /etc/network/interfaces:

![](/assets/img/redes/practica12/img8.png)

Tras editar el fichero /etc/network/interfaces se reinicia el servicio networking.

```bash
sudo systemctl restart networking.service
```

Adicionalmente, como paso final de la configuración se puede añadir contenido al servidor editando el fichero /var/www/html/index.html y, si fuera necesario, añadiendo directorios y ficheros a esta estructura para crear la web.