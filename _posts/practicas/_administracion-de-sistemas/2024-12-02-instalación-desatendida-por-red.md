---
title:  "Instalación desatendida por red de Debian12"
date:   2024-11-24 01:00:00 +0200
categories: administracion-sistemas
tag: [sistemas, debian, linux, instalación, preseed, instalación desatendida, debian installer, iventoy, PXE, TFTP, dnsmasq, administracion-sistemas, Administración de Sistemas Operativos]
excerpt: En este post se recogen varias opciones alternativas para la configuración de un servicio de instalación desatendida de Debian 12 por red.
---

# Instalación automática usando un fichero de preseed alojado en un servidor web

## Configuración del servidor web

El fichero de preseed para la instalación desatendida de Debian se puede aportar no sólo volcando su contenido al archivo de imagen iso del instalador sino también haciéndolo accesible para el cliente en el que se instala el sistema operativo desde un servidor web alojado en la red. Para usar este método de instalación desatendida, primero es necesario contar con un fichero de preseed correctamente configurado y con un servidor web accesible desde el cliente.

```
sudo apt install apache2
```

Este método se puede adaptar a las necesidades de la configuración del servidor web, ya sea creando un virtual host específico para alojar el fichero de preseed o estableciendo una resolución de nombres estática en la red local para que el servidor sea accesible por su nombre de host y no se necesario indicar su IP. Para el propósito de esta documentación, la configuración del servidor web se mantiene sencilla y se opta por la solución más simple: colocar el fichero de preseed en el document root por defecto.

```
cp preseed.cfg /var/www/html
```

Esto hace que el fichero sea accesible desde cualquier cliente conectado a la red que acceda a la ruta `http://IP_SERVIDOR/preseed.cfg`. 

## Acceso al fichero de preseed desde el instalador

Para que el instalador tenga acceso a este fichero el cliente tiene que arrancar con una iso que cargue el menú de instalación de Debian y, desde él, acceder a las `opciones avanzadas` y después a `instalación automática`.

Esto lanza el proceso de instalación y, en un momento determinado, tras la configuración por DHCP del equipo y una vez que la máquina obtiene la configuración de red, se muestra un diálogo en el que se solicita la ubicación del fichero de preseed para ejecutar la instalación desatendida. En este ejemplo el cliente es una máquina virtual creada con libvirt-KVM y el servidor web está instalado en el host, así que la conexión se realiza a través de la red por defecto de libvirt-KVM.

```
http://192.168.122.1/preseed.cfg
```

A partir de este punto, el proceso de instalación continúa su ejecución de forma autónoma hasta finalizar con el reinicio del equipo, que en este punto ya cuenta con el nuevo sistema operativo instalado.

# Instalación desatendida desde un servidor PXE/TFTP

Para esta instalación es necesario contar en el escenario con un servidor DHCP y un servidor TFTP. Ambos se pueden ejecutar en la misma máquina.

## Configuración del servidor TFTP

Como servidor TFTP [la guía oficial de Debian](https://www.debian.org/releases/stable/i386/ch04s05.es.html) recomienda usar `tftpd-hpa` para un servidor Debian GNU/Linux. Está escrito por el mismo autor del gestor de arranque syslinux, y por ello menos proclive a generar problemas.

```
sudo apt install tftpd-hpa
```

Existen dos formas de iniciar TFTP: desde el demonio del sistema o como un demonio independiente. Esta configuración se determina en el fichero /etc/default/tftpd-hpa, en el que se debe incluir la siguiente línea:

```
  RUN_DAEMON="yes"
```

Con esta orden se fuerza la ejecución del demonio. A continuación, se debe reiniciar el servicio para cargar esta nueva configuración

```
sudo systemctl restart tftpd-hpa.service 
```

## Creación del archivo de instalación

A diferencia de las instalaciones que usan medios físicos como CD, DVD o memorias extraíbles USB, donde se usa un fichero de imagen de tipo iso arrancable para cargar e instalar el sistema operativo en el equipo, para las instalaciones por red Debian distribuye imágenes de instalación comprimidas, generalmente, con el nombre `netboot.tar.gz`. Esta imagen se puede descargar desde los repositorios de Debian.

```
wget http://ftp.debian.org/debian/dists/bookworm/main/installer-amd64/current/images/netboot/netboot.tar.gz
```

Para que el archivo de instalación esté disponible desde el resto de equipos conectados a la red hay que descomprimir el contenido de este archivo en el directorio que el servidor TFTP comparte con los clientes.

En este punto se abren dos posibilidades. La primera de ellas es modificar el contenido de este archivo de imagen de forma análoga a cuando se modifica el contenido de una imagen iso, de manera que las órdenes del fichero de preseed se carguen en el fichero initrd de la imagen comprimida y se ejecuten de forma automática durante el proceso de instalación.

La otra opción es instalar junto al servidor TFTP y DHCP de este equipo un servidor web y alojar en él el fichero de preseed. De esta forma, se puede llevar a cabo una instalación automatizada obteniendo el fichero por red tal y como se ha descrito [en el apartado anterior](#instalación-automática-usando-un-fichero-de-preseed-alojado-en-un-servidor-web). Como este procedimiento ya está documentado se sigue a continuación con la otra opción.

Para volcar la configuración del fichero de preseed al fichero initrd del instalador de Debian hay que seguir unos pasos similares a los que se realizan para crear una imagen iso para la instalación desatendida con la diferencia de que, en este caso, no es necesario utilizar ninguna herramienta específica para descomprimir el archivo de instalación. Así, en primer lugar, se descomprime el archivo de instalación y, a continuación se descomprime también el fichero `initrd.gz` del directorio debian-installer.

```
tar -xvf netboot.tar.gz 
gunzip debian-installer/amd64/initrd.gz
```

A continuación, se vuelca el contenido del fichero de preseed con la configuración para la instalación desatendida en el fichero initrd y se vuelve a comprimir.

```
echo preseed.cfg | cpio -H newc -o -A -F debian-installer/amd64/initrd
gzip debian-installer/amd64/initrd
```

> Si este procedimiento se realiza en un equipo diferente al servidor TFTP será necesario comprimir de nuevo el archivo de instalación para poder llevarlo al servidor y descomprimirlo en el directorio que el servidor comparte con los clientes.

Con el archivo de instalación modificado para que ejecuta la instalación desatendida gracias a la configuración que aporta el fichero de preseed y todos los ficheros necesarios para la instalación por red ubicados en el directorio correcto del servidor TFTP (por defecto, el directorio /srv/tftp), el siguiente paso es instalar y configurar el servidor DHCP.

## Configuración del servidor DHCP

A continuación, hay que configurar el servidor DHCP para que apunte al archivo de imagen con el instalador de Debian que ofrece el servidor TFTP. Como servidor DHCP [la guía oficial de Debian](https://www.debian.org/releases/stable/i386/ch04s05.es.html) recomienda el uso del paquete `isc-dhcp-server` que instala el servidor dhcpd de ISC.

```
sudo apt install isc-dhcp-server
```

En su wiki, Debian recoge un [ejemplo de configuración](https://www.debian.org/releases/stable/i386/ch04s05.es.html#dhcpd) básico del servidor DHCP para habilitar el arranque por PXE en las máquinas cliente que se conecten a la red. En este ejemplo, el servidor TFTP y el servidor DHCP están instalados en máquinas diferentes por lo que en el escenario que se plantea aquí la configuración es un poco más sencilla. Simplemente hay que añadir a la configuración habitual del servidor DHCP la línea `filename "pxelinux.0";` dentro del párrafo que identifica la red.

De esta manera, la configuración del fichero /etc/dhcp/dhcpd.conf sería similar a la siguiente:

```
option domain-name "example.org";
option domain-name-servers ns1.example.org, ns2.example.org;

default-lease-time 600;
max-lease-time 7200;

ddns-update-style none;

subnet 10.0.0.0 netmask 255.255.255.224 {
  range 10.0.0.2 10.0.0.20;
  option routers 10.0.0.1;
  allow bootp;
  next-server 10.0.0.1;
  filename "pxelinux.0";
}
```

> No se debe olvidar configurar la interfaz de escucha del servidor DHCP en el fichero /etc/default/isc-dhcp-server con la línea: `INTERFACESv4="enp7s0"`.

Para aplicar la configuración se tiene que reiniciar el servidor.

```
sudo systemctl restart isc-dhcp-server.service
```

Si la instalación de Debian requiere instalar paquetes desde los repositorios, el cliente debe tener acceso a internet. Esto se puede conseguir si el servidor DHCP/TFTP actúa también como router. Para ello, hay que activar el bit de forwarding.

```
echo net.ipv4.ip_forward = 1 > /etc/sysctl.conf 
sudo sysctl -p
```

Además, también deberá contar con una regla de iptables que permite hacer NAT a los paquetes que reciba desde el cliente.

```
sudo iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o enp1s0 -j MASQUERADE
sudo iptables-save > /etc/iptables/rules.v4
```

Tras completar esta configuración, cualquier cliente de la red que arranque usando la opción PXE obtendrá por DHCP la configuración de red que apunta al archivo de instalación almacenado en el servidor TFTP. Cuando el cliente acceda al fichero de arranque al que apunta el servidor DHCP cargará el menú de instalación por red de Debian. Como la imagen de instalación tiene cargadas las órdenes configuradas en el fichero de preseed, al arrancar la instalación desde este menú se produce de forma desatendida.

# Instalación desatendida con dnsmasq

Dnsmasq es una herramienta que funciona como un DNS, un servidor DHCP y un servidor TFTP para redes pequeñas al mismo tiempo. Esto permite reducir el flujo de trabajo para la instalación de sistemas operativos por red al tener que usar sólo una herramienta en vez de trabajar con los diferentes servidores por separado.

Para instalar dnsmasq:

```
sudo apt install dnsmasq
```

La configuración tanto de DHCP como de TFTP se realizan en el fichero /etc/dnsmasq.conf

```
interface=enp7s0
dhcp-range=10.0.0.2,10.0.0.200,6h
dhcp-boot=pxelinux.0
enable-tftp
tftp-root=/srv/tftp
```
La línea `interface` indica la interfaz por la que escucha y reparte direcciones el servidor DHCP, con `dhcp-range` se indica el rango de direcciones y el tiempo de concesión del servidor DHCP, la orden `dhcp-boot` apunta al fichero de arranque, la línea `enable-tftp` habilita el servidor TFTP y con `tftp-root` se apunta a la ruta donde están los ficheros alojados en el servidor TFTP.

Para aplicar los cambios en la configuración se reinicia el servicio.

```
sudo systemctl restart dnsmasq.service 
```

El contenido del directorio /srv/tftp y la configuración para que el servidor DHCP/PXE/TFTP haga de router para los clientes es igual al documentado en [el punto anterior](#instalación-desatendida-desde-un-servidor-pxetftp).

Al encender por red un cliente de la red, el servidor dnsmasq le da una configuración TCP/IP y lo dirige al fichero de arranque del instalador.

# iVentoy: una alternativa sencilla

Una alternativa a la creación de un servidor PXE/TFTP es el uso de herramientas más sencillas como iVentoy. Esta herramienta es una versión mejorada de un servidor PXE que permite arrancar e instalar imágenes iso en diferentes máquinas de la red de manera simultánea. Se trata de una herramienta muy sencilla y fácil de usar y que se puede implantar sin hacer casi ninguna configuración. Además, iVentoy cuenta con una interfaz gráfica a la que se accede a través de un navegador web. Soporta una gran variedad de sistemas operativos y arquitecturas.

La herramienta iVentoy es software libre y se distribuye a través de su [repositorio de GitHub](https://github.com/ventoy/PXE/releases) en forma de archivo comprimido. Para instalarla en el servidor PXE de la red simplemente hay que descargar y descomprimir el archivo y ejecutar el script `iventoy.sh` que está en el directorio raíz al descomprimir este archivo.

```
wget https://github.com/ventoy/PXE/releases/download/v1.0.20/iventoy-1.0.20-linux-free.tar.gz
tar -xvf iventoy-1.0.20-linux-free.tar.gz
cd iventoy-1.0.20/
sudo bash iventoy.sh start
```

Tras ejecutar la herramienta se puede acceder a la interfaz gráfica en el puerto 26000 del servidor. 

```
usuario@preseedsrv:~/iventoy-1.0.20$ sudo bash iventoy.sh start
iventoy start SUCCESS PID=1164

Please open your browser and visit http://127.0.0.1:26000 or 
http://x.x.x.x:26000 (x.x.x.x is any valid IP address)
```

Desde ella se puede configurar el servidor iVentoy, establecer la configuración de DHCP, arrancar y parar el servicio y establecer una lista de equipos que tienen permiso o no para acceder al servidor.

Para hace accesible una imagen iso a los equipos de la red a través de iVentoy simplemente hay que llevar el archivo de imagen iso al directorio iso/ dentro del directorio de iVentoy.

```
cp deb12-inst-desatendida.iso iventoy-1.0.20/iso/
```

Desde la interfaz gráfica se puede también listar las máquinas que se han conectado al servidor para instalar una imagen desde él.

El uso de iVentoy aporta algunas ventajas frente a otras soluciones como la instalación de un servidor PXE/TFTP desde cero. La más evidente es que el proceso de instalación y configuración del servidor es mucho más simple en este caso puesto que no es necesario instalar cada uno de los servidores por separado y establecer la configuración específica para ellos.

Por otra parte, iVentoy facilita el proceso de instalación desatendida de un sistema operativo en múltiples equipos porque permite distribuir imágenes iso, a diferencia del servidor PXE/TFTP que distribuye imágenes en ficheros netboot comprimidos. Esto permite que se pueda distribuir para instalar en un entorno real con múltiples equipos el mismo archivo de imagen iso que se puede instalar en una máquina virtual en un entorno local de pruebas.