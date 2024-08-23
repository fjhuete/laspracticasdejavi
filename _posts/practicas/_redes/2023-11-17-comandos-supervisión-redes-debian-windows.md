---
title:  "Comandos de supervisión de redes"
date:   2023-11-17 00:00:00 +0200
categories: redes
tag: [Windows, GNU/Linux, comandos, redes, Planificación y Administración de Redes]
excerpt: El uso de algunos comandos es fundamental en la administración de redes. Diferentes sistemas operativos usan distintos comandos pero todos ellos cumplen funciones relevantes
---

# Windows

## Configuración de propiedades TCP/IP

En la ventana de configuración de las propiedades de TCP/IP en Windows se pueden configurar dos elementos: la dirección IP y la dirección de servidor DNS.

![](/assets/img/redes/practica4/img1.png)

Para la configuración de la dirección IP, se puede asignar manualmente la dirección IP del ordenador, esto es el número de 32 bits expresado en notación decimal puntuada que identifica de manera única a este dispositivo dentro de la red local.

En el siguiente campo se puede indicar también la máscara de red expresada igualmente en notación decimal puntuada. Este campo indica qué bits de la dirección IP corresponden a la dirección de red y cuáles a la dirección de máquina, es decir, indica qué parte de la dirección IP de cada equipo se refiere al número que se le asigna a la red para identificarla y que deben compartir todas las direcciones IP de todos los dispositivos que formen parte de esa misma red y qué parte de la dirección IP de cada dispositivo identifica, de manera única, al dispositivo dentro de la red.

Por último, también se puede configurar la puerta de enlace predeterminada, es decir, la dirección IP privada de la red, también expresada en notación decimal puntuada, a la que se deben dirigir los mensajes que no están destinados a ninguno de los dispositivos conectados a esa red. Habitualmente, esta dirección IP coincide con la dirección IP privada del router.

![](/assets/img/redes/practica4/img2.png)

Por otra parte, hay varios campos que permiten configurar también las direcciones del servidor DNS. En concreto, se puede indicar la dirección del servidor DNS preferido y la dirección del servidor DNS alternativo. Es decir, estos campos permiten indicar la dirección IP de los servidores tanto preferido como alternativo que se van a encargar de buscar la dirección IP correspondiente a las URL que se busquen desde un navegador web. Así se puede comunicar un dispositivo con otros conectados a otras redes sin tener que conocer las respectivas direcciones IP de cada uno de ellos.

Desde esta ventana de configuración, también se puede acceder a la configuración avanzada de TCP/IP. En esta configuración avanzada se pueden agregar, editar o quitar direcciones IP configuradas en el equipo. También se puede operar agregando, editando o quitando puertas de enlace predeterminadas de forma manual.

Además, una segunda pestaña incluye opciones para la configuración del DNS. En ella se pueden agregar y ordenar varias direcciones de servidores DNS, teniendo en cuenta el orden de uso. También permite marcar las opciones de anexar sufijos DNS principales y específicos para las conexiones, anexar sufijos primarios del sufijo DNS principal y anexar los sufijos indicados manualmente en el orden que se recoja en el cuadro de texto de la ventana de configuración. Finalmente, también se recogen las posibilidades de registrar en DNS las direcciones de la conexión actual y usar el sufijo DNS de la conexión actual para el registro en DNS.

La última pestaña que se recoge en esta ventana de configuración avanzada de TCP/IP se refiere a las direcciones WINS, que también se pueden agregar, editar, quitar u ordenar según su uso. En esta pestaña también se puede habilitar o deshabilitar la búsqueda de LMHOSTS o configurar NetBIOS con tres opciones: predeterminada, que usa la configuración de NetBIOS del servidor DHCP; habilitar NetBIOS a través de TCP/IP; o deshabilitar NetBIOS a través de TCP/IP.

## Utilidad del comando ``ping``

El comando ``ping`` calcula el tiempo mínimo, medio y máximo de respuesta en la conexión entre dos puntos de una red. Este comando permite saber si un dispositivo puede llegar a otro de forma correcta o no. También aporta comunicación sobre el tiempo que se tarda en mantener esa comunicación. Es una herramienta útil para encontrar posibles errores en las tablas de enrutamiento.

![](/assets/img/redes/practica4/img3.png)

El comando ``ping`` permite saber si un problema de acceso a una web puede ser derivado de un fallo de la conexión con el servidor o si, por el contrario, se trata de otro tipo de problema. Otro ejemplo de uso es comprobar si desde un dispositivo de una red local hay acceso a otros dispositivos o recursos compartidos como, por ejemplo, un impresora.

![](/assets/img/redes/practica4/img4.png)

Además, en casos en los que hay cortes de Internet o problemas de conectividad el comando ``ping`` permite identificar si el origen del error está en la red local o en el servicio de Internet, ya que este comando permite descartar que haya problemas en la red local doméstica al hacer un ``ping`` a la puerta de enlace del dispositivo que se está usando. En cambio, para comprobar si el problema está en la conexión a Internet se puede hacer un ``ping`` a una dirección IP de fuera de la red para comprobar si hay un corte en la comunicación o una latencia demasiado alta.

![](/assets/img/redes/practica4/img5.png)

Otro ejemplo de uso del comando ``ping`` es el análisis de la latencia en redes inalámbricas.

![](/assets/img/redes/practica4/img6.png)

Pero el comando ``ping`` también se puede usar con otros objetivos. Por ejemplo, para conocer la IP que usa un dominio web, se puede usar el comando ``ping`` con el modificador `-a` o para conocer la dirección IP asociada a una cuenta de correo electrónico se puede usar el comando ``ping`` seguido de la dirección de correo electrónico que se quiere consultar.

![](/assets/img/redes/practica4/img7.png)

## La salida del comando `ipconfig`

El comando `ipconfig` es un comando de Windows que permite conocer información como las direcciones IP y MAC del ordenador o la dirección IP del router.

![](/assets/img/redes/practica4/img8.png)

Al usar el comando `ipconfig` en Windows, se muestran por pantalla algunos datos relevantes sobre la conexión del ordenador. En primer lugar, se muestra información sobre la configuración de la IP y, posteriormente, se muestran datos relevantes relacionados con los dispositivos que permiten la conexión a la red del ordenador.

![](/assets/img/redes/practica4/img9.png)

Por ejemplo, el campo descripción muestra el nombre de adaptador o tarjeta de red empleado para realizar la conexión, el campo dirección IPv4 muestra la dirección IP única asignada al ordenador dentro de la red local expresada en notación decimal puntuada. 

![](/assets/img/redes/practica4/img10.png)
![](/assets/img/redes/practica4/img11.png)

El campo máscara de subred indica qué bits de la dirección IP corresponden a la dirección de red y cuáles a la dirección de máquina, es decir, indica qué parte de la dirección IP de cada equipo se refiere al número que se le asigna a la red para identificarla y que deben compartir todas las direcciones IP de todos los dispositivos que formen parte de esa misma red y qué parte de la dirección IP de cada dispositivo identifica, de manera única, al dispositivo dentro de la red.

![](/assets/img/redes/practica4/img12.png)

A continuación aparece el campo puerta de enlace predeterminada, que muestra la dirección IP del equipo que tiene acceso a Internet y que, habitualmente, suele ser el router en el caso de las redes locales domésticas.

![](/assets/img/redes/practica4/img13.png)

También se incluyen en esta pantalla algunos datos relativos a los servidores DNS. En concreto, se muestra la dirección IP de los servidores, habitualmente dos, con los que el router asigna la dirección IP correspondiente a cada nombre de dominio de las páginas web solicitadas por los usuarios a través de los navegadores web.

![](/assets/img/redes/practica4/img14.png)

Un último campo que cabe destacar es el de estado de DHCP, que muestra si la configuración dinámica de host está habilitada y, por tanto, si usa una dirección IP estática o fija entre el equipo y el host o no. Si el estado de DHCP es habilitado, cada vez que se inicie una conexión se usará una dirección IP diferente; si el estado es deshabilitado, la dirección IP se mantendrá fija en todas las conexiones.

![](/assets/img/redes/practica4/img15.png)

## Utilidad de `ipconfig /renew`

Una de las funciones del comando `ipconfig` es renovar la configuración de DHCP para todos los adaptadores o para un adaptador específico dentro de la red. Para poder usar el parámetro `/renew` del comando `ipconfig` es necesario que los adaptadores estén configurados para obtener automáticamente una dirección IP.
Si no se especifica el nombre de un adaptador, se renueva la configuración de DHCP de todos los adaptadores del ordenador.

![](/assets/img/redes/practica4/img16.png)

Para renovar la configuración de DHCP de un único adaptador es necesario indicarlo en la sintaxis del comando. Para conocer el nombre del adaptador sobre el que se quiere efectuar esta acción se debe ejecutar el comando `ipconfig` sin parámetros y elegir el adaptador deseado de entre los que devuelve la ejecución del comando.

## Utilidad del comando `arp -a`

El comando `arp` muestra y modifica las entradas en la memoria caché del Protocolo de resolución de direcciones (ARP). La memoria caché de ARP contiene una o varias tablas que se usan para almacenar direcciones IP y sus direcciones físicas de Ethernet o Token Ring resueltas. Hay una tabla independiente para cada adaptador de red Ethernet o Token Ring instalado en el equipo.
Si se usa el parámetro `-a` junto al comando `arp` se muestran las tablas de caché de arp actuales para todas las interfaces. Es decir, para conocer las tablas de caché arp para todas las interfaces se debe ejecutar el comando `arp -a`.

![](/assets/img/redes/practica4/img17.png)

## Utilidad del comando ``netstat``

El comando ``netstat`` muestra las conexiones TCP activas, los puertos en los que escucha el equipo, las estadísticas de Ethernet, la tabla de enrutamiento IP, las estadísticas IPv4 (para los protocolos IP, ICMP, TCP y UDP) y las estadísticas de IPv6 (para los protocolos IPv6, ICMPv6, TCP a través de IPv6 y UDP a través de IPv6).

![](/assets/img/redes/practica4/img18.png)

Este comando es útil si se quiere conocer cuáles son las conexiones TCP activas en el equipo y los puertos TCP y UDP en los que escucha el ordenador.

![](/assets/img/redes/practica4/img19.png)

Además, ``netstat`` también se puede usar para ver estadísticas Ethernet, como el número de bytes y paquetes enviados y recibidos.

![](/assets/img/redes/practica4/img20.png)

Otra utilidad del comando ``netstat`` es conocer las estadísticas por protocolo para los protocolos TCP, UDP, ICMP e IP, así como para los protocolos IPv6 e ICMPv6 si están instalados.

![](/assets/img/redes/practica4/img21.png)

Por último, este comando también se puede emplear para conocer el contenido de la tabla de enrutamiento de IP.

![](/assets/img/redes/practica4/img22.png)

## Utilidad del comando `nslookup`

El comando `nslookup` muestra información que se puede usar para diagnosticar la infraestructura del Sistema de nombres de dominio (DNS).

Nslookup permite resolver direcciones IP asociadas a un determinado dominio. A través de este comando podemos obtener la dirección IP si conocemos el nombre, o al contrario, conocer el nombre del dominio si sabemos la dirección IP. Pero más allá de conocer el nombre o dirección IP también sirve para solucionar problemas de resolución de nombres y comprobar el estado actual de los servidores.

Este comando cuenta con un modo de depuración que puede ser llamado con el comando `nslookup` set debug.

Una de las utilidades que se le puede dar a `nslookup` es encontrar la IP de un nombre de dominio en un servidor DNS determinado.

![](/assets/img/redes/practica4/img23.png)

Otro posible uso de esta herramienta es buscar el nombre de dominio de una determinada dirección IP en el servidor DNS predeterminado en el equipo.

![](/assets/img/redes/practica4/img24.png)

En general, `nslookup` es ampliamente usado para analizar problemas a la hora de acceder a una determinada página web, para saber la dirección IP asociada a una determinada web o para comprobar si un servidor DNS funciona correctamente.

## Utilidad del comando `tracert`

`Tracert` es una herramienta de diagnóstico que determina la ruta de acceso a un destino mediante el envío de mensajes de solicitud de eco del Protocolo de mensajes de control de Internet (ICMP) o ICMPv6 al destino con valores de campo de período de vida (TTL) cada vez mayores. Cada enrutador a lo largo de la ruta de acceso debe disminuir el TTL de un paquete IP en al menos 1 antes de reenviarlo. En la práctica, el TTL es un contador máximo de vínculos. Cuando el TTL de un paquete alcanza 0, se espera que el enrutador devuelva un mensaje ICMP time Exceeded al equipo de origen.

Este comando determina la ruta de acceso enviando el primer mensaje de solicitud de eco con un TTL de 1 e incrementando el TTL en 1 en cada transmisión posterior hasta que el destino responde o se alcanza el número máximo de saltos. El número máximo de saltos es 30 de forma predeterminada y se puede especificar mediante el parámetro `/h`.

La ruta de acceso se determina examinando los mensajes de ICMP time Exceeded devueltos por los enrutadores intermedios y el mensaje de eco de respuesta devuelto por el destino. Sin embargo, algunos enrutadores no devuelven mensajes de tiempo excedido para paquetes con valores TTL vencidos y son invisibles para el comando `tracert`. En este caso, se muestra una fila de asteriscos (*) para ese salto. La ruta de acceso mostrada es la lista de interfaces de enrutador cercano/lateral de los enrutadores en la ruta de acceso entre un host de origen y un destino. La interfaz cercana/lateral es la interfaz del enrutador que está más cerca del host emisor en la ruta de acceso.

El comando `tracert` tiene utilidad a la hora de averiguar si hay algún problema en el camino hacia un equipo de la red local o a un sitio web. Este comando aporta información sobre si la comunicación hacia el host de destino se pierde o interrumpe en algún punto del camino. Gracias a los resultados que devuelve, se puede averiguar en qué punto ocurre el problema.

![](/assets/img/redes/practica4/img25.png)

Uno de los usos principales de este comando es el diagnóstico de errores en redes muy grandes en las que un paquete puede elegir diferentes caminos para llegar a su host de destino. Gracias a él, se puede saber por qué equipos de la red está pasando el paquete de datos enviado y así se puede mejorar el rendimiento de la red local.

## Utilidad del comando `route print`

El comando `route` muestra y modifica las entradas de la tabla de enrutamiento de IP local. Si se le añade el parámetro print, el comando mostrará toda la información de la tabla de enrutamiento del ordenador.
El comando `route print` se usa para ver la tabla de enrutamiento de un ordenador.

![](/assets/img/redes/practica4/img26.png)

## Averiguar la IP pública de un router

Se puede conocer la IP pública de una red de varias formas. La más sencilla de todas las opciones posibles es acceder a una de las múltiples herramientas web que ofrecen este tipo de información con una simple visita a su web como ocurre, por ejemplo, con cual-es-mi-ip.net. Este tipo de páginas muestra la IP pública desde la que se accede a ellas pero también otra información asociada a este dato como la compañía desde la que se realiza la conexión, si se está usando o no un proxy o la localización desde la que se está navegando por Internet.

Sin embargo, esta información también se puede conocer usando el comando `nslookup`.

![](/assets/img/redes/practica4/img27.png)

# GNU/Linux

## Configuración de propiedades TCP/IP

Hay dos formas de configurar los parámetros TCP/IP de una tarjeta de red en GNU/Linux. La primera de ellas es hacerlo a través de la interfaz gráfica, al igual que en el caso anterior con Windows.

Para ello se puede usar la aplicación de conexiones de red y acceder a la conexión que se desee configurar.

![](/assets/img/redes/practica4/img28.png)

En la nueva ventana se pueden modificar parámetros como el método de conexión (DHCP automático, manual, compartida con otros equipos o sólo enlace local) y se pueden añadir varias direcciones IP estáticas indicando su dirección, máscara de red y puerta de enlace. Además, se pueden añadir otros datos relevantes para la configuración TCP/IP como los servidores DNS adicionales, los dominios de búsqueda adicionales o la ID del cliente DHCP.

Además, en otras pestañas de esta misma ventana se pueden configurar otros elementos como la red inalámbrica, la seguridad de la red a través de contraseña, el proxy y otros ajustes generales de la conexión.

![](/assets/img/redes/practica4/img29.png)

Sin embargo, esta configuración también se puede llevar a cabo desde la línea de comandos. Para ello, el primer paso debe ser averiguar el nombre de la interfaz de red que se quiere configurar usando un comando como `ip` o `iconfig`.

![](/assets/img/redes/practica4/img30.png)

Con esta información, se puede acceder y modificar el fichero /etc/network/interfaces, que contiene la configuración TCP/IP de la tarjeta de red, entre otros parámetros importantes para las conexiones del ordenador.

Por defecto este fichero contiene una configuración automática de los elementos de conexión a la red con los que cuenta el ordenador. Sin embargo, modificando la información que contiene se puede establecer una configuración TCP/IP diferente a la predeterminada para las diferentes tarjetas de red del equipo.

## Utilidad del comando `ifconfig`

El comando `ifconfig` se puede utilizar desde la línea de comandos para asignar una dirección a una interfaz de red o para configurar o mostrar la información de configuración actual de la interfaz de red. El comando `ifconfig` debe usarse al inicio del sistema para definir la dirección de red de cada interfaz presente en una máquina.

En general, `ifconfig` permite configurar y ver los parámetros de la interfaz de red para las redes que hacen uso de TCP/IP residentes en el kernel de GNU/Linux. También permite asignar direcciones a una interfaz de red o configurar la información de configuración de esa interfaz según sea necesario.

La utilidad principal de `ifconfig` es conseguir información relacionada con las tarjetas de red de un ordenador. Este comando ofrece información sobre el nombre del adaptador, el número actual de MTU, la dirección IP asignada, la dirección MAC del adaptador, los paquetes enviados y recibidos o los paquetes con error y redireccionados.

![](/assets/img/redes/practica4/img31.png)

Además, `ifconfig` también permite habitiltar o deshabilitar una interfaz desde la línea de comandos.

```bash
ifconfig wlp2s0 down
ifconfig wlp2s0 up
```

El comando `ifconfig` también puede tener otros usos como, por ejemplo, establecer una nueva máscara de red o dirección IP a una interfaz determinada.

```bash
ifconfig wlp2s0 netmask 255.255.255.0
ifconfig wlp2s0 192.168.0.15
```

También permite modificar otros parámetros como la unidad máxima de transmisión (UMT), la puerta de enlace o la dirección MAC, así como crear alias para cada una de las interfaces de red. Finalmente, cabe destacar que `ifconfig` también se puede usar para obtener la información más relevante relacionada con una tarjeta de red de forma muy resumida.

![](/assets/img/redes/practica4/img32.png)

### ¿Hay alguna información de las que se obtiene con `ipconfig /all` que no aparezca?
El comando `ipconfig /all` muestra los siguientes datos que no están disponibles al ejecutar ifconfig: sufijo DNS específico para la conexión, descripción de la tarjeta de red, dirección física, DHCP habilitado, configuración automática habilitada, servidor DHCP, puerta de enlace predeterminada IAID DHCPv6, DUID de cliente DHCPv6, servidores DNS y NetBIOS sobre TCP/IP.

Esta es la información que se ha podido conseguir de otra forma:

- Descripción de la tarjeta de red
- Puerta de enlace predeterminada:
    ```bash
    route -n
    ```
- Servidores DNS: 
    ```bash
    cat /etc/resolv.conf | grep nameserver
    ```

## Utilidad del comando `dhclient`

El cliente DHCP de Internet Software Consortium, `dhclient`, proporciona un medio para configurar una o más interfaces de red utilizando el protocolo Dynamic Host Configuration Protocol, el protocolo BOOTP, o si estos protocolos fallan, asignando una dirección estáticamente.

La utilidad de este comando es configurar el servidor DHCP en un ordenador.

## Diferencias de `netstat` y `ping` entre Windows y GNU/Linux

### `Netstat`

La primera gran diferencia a la hora de usar el comando `netstat` entre Windows y GNU/Linux es que mientras que en el primer caso simplemente hay que introducir el comando en la terminal y ejecutarlo, en el segundo se debe instalar el paquete que contiene este binario antes de poder ejecutarlo puesto que no viene instalado por defecto.

Otra diferencia de `netstat` entre estos dos sistemas operativos es que en el caso de GNU/Linux muestra, por defecto, toda la información de conexiones sin necesidad de añadir ningún parámetro al comando mientras que en Windows es necesario añadir el parámetro `-a`.

![](/assets/img/redes/practica4/img33.png)

Además, cabe destacar que en GNU/Linux existe la opción de acceder a estadísticas del uso de la red a través del comando `netstat` si se añade el parámetro `-s`.

![](/assets/img/redes/practica4/img34.png)

### `ping`
En el caso del comando `ping` la principal diferencia está en su uso. Windows envía, por defecto, cuatro paquetes de 32 bytes y, a partir de esa transmisión de información calcula las estadísticas que devuelve el comando, como porcentaje de paquetes perdidos y velocidad mínima, máxima y media de transmisión. En cambio, el comando `ping` en GNU/Linux envía por defecto paquetes de 64 bytes hasta que se interrumpa manualmente el proceso y, a partir de esas pruebas, devuelve las mismas estadísticas.

![](/assets/img/redes/practica4/img35.png)

## Utilidad del comando `dig`

`Dig` es un comando que permite realizar consultas a servidores DNS para obtener información relacionada con este servicio. Este comando permite hacer cualquier consulta DNS como registros tipo A (direcciones IP), TXT (texto), MX (servidores de correo) y los propios servidores de nombres. Por eso, `dig` se usa mucho para el diagnóstico de servidores DNS.

En general, uno de los principales usos del comando `dig` es obtener registros del servidor DNS de varios tipos.

![](/assets/img/redes/practica4/img36.png)

Otro uso frecuente del comando `dig` es obtener los servidores de nombre.

![](/assets/img/redes/practica4/img37.png)

Además, `dig` también se puede usar para obtener registros de otros tipos como MX o TXT.

![](/assets/img/redes/practica4/img38.png)
![](/assets/img/redes/practica4/img39.png)

## Diferencias entre `traceroute` y `tracert`
Tal y como ocurre en el caso de netstat, la primera gran diferencia entre `traceroute` y `tracert` es que mientras este último está instalado en Windows, `traceroute` no está disponible en GNU/Linux hasta que se instala por parte del usuario.

Más allá de esto, la única diferencia relevante en la salida que se muestra por pantalla al ejecutar el comando entre `traceroute` y `tracert` es el orden de los datos que se representan. Mientras `tracert` muestra primero las diferentes informaciones relacionadas con la velocidad de la transmisión de la información y al final de la línea las diferentes direcciones IP, `traceroute` lo hace al contrario y muestra al principio de la línea la información relativa a las diferentes direcciones IP y, más a la derecha, los datos que indican las diferentes velocidades que esta herramienta devuelve.

![](/assets/img/redes/practica4/img40.png)

## Utilidad del comando `wget`

El comando `wget` se utiliza para descargar archivos de Internet. Este comando también los guarda automáticamente en el directorio de trabajo actual. La descarga no es interactiva, es decir, el proceso puede llevarse a cabo sin haber iniciado sesión. También permite reanudar descargas incompletas o interrumpidas.

`Wget` es un recuperador por red no interactivo. Así, la utilidad principal del comando `wget` es descargar archivos de Internet.

![](/assets/img/redes/practica4/img41.png)

Otra posible utilidad es descargar archivos de Internet con nombres diferentes.

```bash
wget -O- https://www.virtualbox.org/download/oracle_vbos_2016.asc
```

Además, `wget` también se puede emplear con otros fines como, por ejemplo, limitar la velocidad de las descargas o el número de intentos.

```bash
wget --limit-rate=500k https://ftp.gnu.org/gnu/wget/wget2-latest.tar.gz
wget -tries=100 https://ftp.gnu.org/gnu/wget/wget2-latest.tar.gz
```

Otra utilidad relevante de `wget` es la de ejecutar descargas de ficheros de Internet en segundo plano.

```bash
wget -b https://ftp.gnu.org/gnu/wget/wget2-latest.tar.gz
```

Por último, cabe destacar la que, probablemente, sea una de las utilidades más prácticas del comando `wget`: descargar páginas web completas.

```bash
wget --mirror https://ftp.gnu.org/gnu/wget/
```

## Utilidad del comando `tcpdump` y combinación con `ngrep`

El comando `tcpdump` es una herramienta que se usa principalmente para analizar el tráfico que circula por una red. `Tcpdump` permite al usuario capturar y mostrar en tiempo real los paquetes transmitidos y recibidos por la red a la cual el ordenador está conectado. 

Una de las principales funciones de `tcpdump` es depurar aplicaciones en tiempo real que utilizan la red para comunicarse. También se puede usar para depurar la propia red y para capturar y leer datos enviados por otros usuarios u ordenadores. El objetivo de la captura de toda esta información es almacenarla para poder estudiarla posteriormente.

Otro uso que se le puede dar a `tcpdump` es comprobar que el tráfico de una red es el esperado teniendo en cuenta su uso, así como capturar y leer los datos de otros equipos de la red.

Además, `tcpdump` puede ser una herramienta peligrosa si se emplea con malas intenciones puesto que un usuario que tiene el control de un router a través del cual circula tráfico no cifrado puede usar `tcpdump` para conseguir contraseñas u otras informaciones.

El comando `tcpdump` devuelve una gran cantidad de información, por tanto, puede resultar muy práctico combinar su uso con el de un paginador como `ngrep`.

## Utilidad del comando `arp` y opción equivalente del comando `ip`

El comando `arp` se usa para encontrar los detalles de los dispositivos conectados cuando el protocolo convierte la IP a MAC. Este comando manipula la memoria caché ARP del sistema. También permite un volcado completo de la memoria caché ARP. `Arp` puede añadir entradas a la tabla de la memoria caché ARP del sistema, eliminarlas o mostrar el contenido actual.

![](/assets/img/redes/practica4/img42.png)

Existe también una opción del comando `ip` que muestra la misma información que si se ejecuta el comando `arp`. La opción equivalente del comando `ip` para conseguir obtener esta misma información es `neighbour`.

![](/assets/img/redes/practica4/img43.png)

## Posibilidades del comando `ip` de GNU/Linux

La sintaxis del comando `ip` está formada por el nombre del comando seguido de una o varias opciones y un objeto. Como se puede apreciar en la página del manual del comando, existe una gran variedad de opciones y objetos que se pueden aplicar a este comando.

```bash
SYNOPSIS
       ip [ OPTIONS ] OBJECT { COMMAND | help }

       ip [ -force ] -batch filename

       OBJECT := { link | address | addrlabel | route | rule | neigh | ntable
               | tunnel | tuntap | maddress | mroute | mrule | monitor | xfrm
               | netns | l2tp | tcp_metrics | token | macsec | vrf | mptcp |
               ioam }

       OPTIONS := { -V[ersion] | -h[uman-readable] | -s[tatistics] |
               -d[etails] | -r[esolve] | -iec | -f[amily] { inet | inet6 |
               link } | -4 | -6 | -B | -0 | -l[oops] { maximum-addr-flush-at‐
               tempts } | -o[neline] | -rc[vbuf] [size] | -t[imestamp] |
               -ts[hort] | -n[etns] name | -N[umeric] | -a[ll] | -c[olor] |
               -br[ief] | -j[son] | -p[retty] }

```

Entre las opciones, algunas de las más relevantes son `-b` o `-batch`, que lee los comandos del fichero que se indique y los invoca; `-s`, `-stats` o `-statistics`, que muestra más información que en el modo normal; `-d` o `-details`, que también muestra la información de forma más detallada; `-r` o `-resolve`, que sustituye las direcciones IP por los nombres de dominio DNS en la información que muestra por pantalla; o `-br` o `-brief`, que muestra solo información básica en formato tabular para facilitar su lectura.

Sin embargo, donde realmente reside el potencial del comando `ip` es en la variada lista de objetos que especifican el aspecto de la configuración de la red sobre el que este comando puede ejecutar sus tareas. Entre los objetos más relevantes que recoge el manual de este comando están address, que trabaja sobre la dirección IPv4 o IPv6 de un dispositivo; link, para configurar dispositivos de red; monitor, para monitorizar la red; neighbour, para trabajar con las entradas de la caché de ARP; netns, para gestionar los nombres de los dispositivos de la red; ntable, para gestionar las operaciones de la memoria caché; route, para trabajar sobre la tabla de enrutamientos; stats, para gestionar y mostrar las estadísticas de las diferentes interfaces de la red o tcp_metrics, para gestionar y monitorizar las métricas de las comunicaciones TCP.

Con esta gran variedad de aspectos de la configuración de red sobre los que `ip` puede trabajar, este comando se convierte en le más completo y el que más opciones aporta para la gestión de redes a través de una única herramienta.