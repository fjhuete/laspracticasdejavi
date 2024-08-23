---
title:  "Así funciona IPv6: configuración en router Linux y Cisco"
date:   2024-05-14 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, router Linux, Cisco, IPv6, SLAAC, DHCPv6, Wireshark]
---
IPv6 es una actualización del protocolo IPv4 que nace ante la escasez de direcciones que IPv4 permite. Las direcciones IPv6 están formada por 32 caracteres hexadecimales en grupos de cuatro caracteres separados por el carácter dos puntos (:). En este post se recogen varios ejemplos prácticos del funcionamiento de este protocolo.

# Dirección IPv6 de enlace local

El funcionamiento de las direcciones IPv6 de enlace local se puede comprobar en un escenario simulado en GNS3 con dos máquinas Debian conectadas al mismo switch.

Para conseguir una dirección IPv6 es necesario encender las interfaces que, en estas máquinas, están apagadas por defecto. Para ello, se usa el comando `sudo ip l set ens4 up` e inmediatamente después ya se puede consultar la dirección IPv6 de enlace local de cada máquina.

![](/assets/img/redes/practica16/img1.png)
*PC1*

![](/assets/img/redes/practica16/img2.png)
*PC2*

Para comprobar la conectividad entre ambos equipos se puede hacer un ping, por ejemplo, de PC1 a PC2 con el comando `ping6 -c 3 fe80::ea4:e8ff:fe35:0`.

![](/assets/img/redes/practica16/img3.png)

# Direcciones globales por SLAAC con un router Linux

En este caso, se añade una tercera máquina Debian al escenario que funciona como router.

Para que esta nueva máquina funcione como router se debe activar el bit de forwarding para IPv6 descomentando la línea `net.ipv6.conf.all.forwarding=1` en el fichero /etc/sysctl.conf.

Además, para que el router pueda ofrecer la información sobre el prefijo de red necesaria para que las máquinas generen su IPv6 global mediante SLAAC, debe tener instalado y configurado un daemon que cumpla este propósito. En este caso, se usa radvd. Este daemon se instala con el gestor de paquetes apt: 

```bash
sudo apt install radvd
```
Al comprobar el estado del daemon tras la instalación con `systemctl status radvd`, la salida del comando muestra una advertencia sobre la ausencia del fichero de configuración. En este caso, la instalación no genera los ficheros de configuración necesarios para la gestión del daemon y se deben crear a mano.

![](/assets/img/redes/practica16/img4.png)

Este fichero de configuración se debe alojar en la ubicación /etc/radvd.conf y contiene información como el nombre de la interfaz, los intervalos mínimos y máximos en los que el router debe enviar mensajes Router Advertisement o el prefijo que debe indicar a las máquinas de la red que lo soliciten con una estructura como esta:

```
interface ens4
{
MinRtrAdvInterval 3;
MaxRtrAdvInterval 4;
AdvSendAdvert on;
AdvManagedFlag on;
prefix 2001:db8::/64
{ AdvValidLifetime 14300; AdvPreferredLifetime 14200; }
;
};
```

Tras aplicar esta configuración y reiniciar el daemon, se ejecuta con éxito.

Al volver a consultar la dirección IPv6 de las máquinas conectadas a la red, éstas ya cuentan con una dirección global:

![](/assets/img/redes/practica16/img5.png)
*PC1*

![](/assets/img/redes/practica16/img6.png)
*PC2*

De nuevo, para comprobar la conectividad, se puede hacer un ping, esta vez a la dirección global de PC2 desde PC1, por ejemplo con `ping6 -c 3 2001:db8::ea4:e8ff:fe35:0`:

![](/assets/img/redes/practica16/img7.png)

Finalmente, para hacer persistente esta configuración en las interfaces de las máquinas se añade la siguientes líneas al fichero /etc/network/interfaces:

```
auto ens4
iface ens4 inet6 auto
```

# Servidor DHCPv6 en router Linux

Las direcciones IPv6 de la red también se pueden otorgar a las máquinas a través de DHCPv6. Para ello, el router debe contar con un servidor DHCPv6, en este caso, dhcp6s.

Antes de comenzar la instalación del servidor y la configuración por DHCPv6 de las máquinas de la red, los comandos `sudo systemctl stop radvd.service` y `sudo systemctl disable radvd.service` paran y deshabilitan el daemon configurado en el punto anterior.

El servidor DHCPv6 dhcp6s requiere ser configurado tras su instalación: 

```bash
sudo apt install wide-dhcp6-server
```
El instalador lanza un asistente de configuración que guía el proceso. En primer lugar, se configura la interfaz por la que el servidor escucha las peticiones DHCP. A continuación se debe completar la configuración editando el fichero /etc/wide-dhcpv6/dhcp6s.conf a partir de los ejemplos recogidos en el directorio /usr/share/doc/wide-dhcpv6-server/examples.

Este fichero de ejemplo se puede copiar a la ubicación en el directorio /etc con el comando sudo cp /usr/share/doc/wide-dhcpv6-server/examples/dhcp6s.conf.sample /etc/wide-dhcpv6/dhcp6s.conf. Entre las opciones de configuración que permite el servidor, están la de indicar una dirección de DNS a las máquinas, asignar un pool de direcciones IPv6 que puede ofrecer o reservar direcciones estáticas a equipos concretos. En este caso, se configura un pool que concede direcciones desde la 2001:db8:1:2::1000 a la 2001:db8:1:2::2000 durante 3600 segundos. Además, indica a todas las máquinas que la dirección del DNS es la  2001:db8::35.

```
option domain-name-servers 2001:db8:2::45;

interface ens4 {
        address-pool pool1 3600;
};

pool pool1 {
        range 2001:db8:1:2::1000 to 2001:db8:1:2::2000 ;
};
```

Para que los clientes puedan recibir la configuración IPv6 que ofrece este servidor deben tener instalado el paquete dhcp6c.

```bash
sudo apt install wide-dhcpv6-client.
```

En este caso se debe indicar durante el proceso de instalación la interfaz por la que el cliente solicitará las direcciones IPv6.

El paquete del cliente también genera ejemplos de configuración durante la instalación, en este caso, en el directorio /usr/share/doc/wide-dhcpv6-client/examples/. Desde este fichero de ejemplo, se puede copiar y adaptar el siguiente contenido al fichero de configuración:

```json
interface ens4 {
       send ia-na 0;
       send rapid-commit;
       send domain-name-servers;
};

id-assoc na {
};
```

Con esta configuración el cliente devuelve los siguientes errores al intentar obtener una dirección IPv6 desde el servidor DHCP.

Otra opción para otorgar direcciones por DHCPv6 es el servidor DHCPv6 Kea, del ISC. Para instalar este servidor se debe añadir primero el repositorio al sistema con el comando 

```bash
curl -1sLf \
  'https://dl.cloudsmith.io/public/isc/kea-2-0/setup.deb.sh' \
  | sudo -E bash.
```

A continuación se puede instalar el servidor. 

```bash
sudo apt install isc-kea-dhcp6-server
```
La configuración del servidor se establece en el fichero /etc/kea/kea-dhcp6.conf. Comienza con las siguientes líneas, en las que se indica al servidor por qué interfaces debe escuchar.

```json
"Dhcp6": {
    "interfaces-config": {
        "interfaces": [ens4]
    },
```

A continuación, se indica en qué tipo de base de datos debe almacenar la información sobre las direcciones concedidas.

```json
    "lease-database": {
        "type": "memfile",
        “persist”: true,
        "lfc-interval": 3600
    },
```

Posteriormente se puede indicar cómo debe gestionar el servidor las concesiones que han expirado.

```json
    "expired-leases-processing": {
        "reclaim-timer-wait-time": 10,
        "flush-reclaimed-timer-wait-time": 25,
        "hold-reclaimed-time": 3600,
        "max-reclaim-leases": 100,
        "max-reclaim-time": 250,
        "unwarned-reclaim-cycles": 5
    },
```

Además, en este fichero se establece una configuración genérica para el tiempo de las concesiones, en la que se puede indicar el tiempo que debe esperar el servidor antes de renovar una concesión, el tiempo de vida de la concesión por defecto y el tiempo válido, entre otros parámetros.

```json
"renew-timer": 1000,
    "rebind-timer": 2000,
    "preferred-lifetime": 3000,
    "valid-lifetime": 4000,
```

En el campo option-data se puede añadir otra información relacionada con la configuración IP que se concede a las máquinas cliente como, por ejemplo, las direcciones de DNS o la opción unicast (“code”: 12) que indica la dirección IPv6 por la que escucha el router.

```json
"option-data": [
        {
            "name": "dns-servers",
            "data": "2001:db8:2::45, 2001:db8:2::100"
        },
        {
            "code": 12,
            "data": "2001:db8::1"
        },
```

Después se define también la subred con la que trabaja el servidor con la opción “subnet6”. En ella se define el prefijo de la red que se usa en las direcciones que se otorgan a los clientes, así como los pools de direcciones que el servidor puede asignar. Este campo también cuenta con la opción “option-data” que se aplica sólo a las máquinas de la subred y que sobrescribe la configuración general si se usa. Además, permite reservar direcciones IPv6 para determinados clientes.

```json
"subnet6": [
        {
           "subnet": "2001:db8:1::/64",
            "pools": [ { "pool": "2001:db8:1::/80" } ]
    }
  ]
}
}.
```

El servidor isc-dhcp también cuenta con soporte para IPv6. Este servidor se instala con `sudo apt install isc-dhcp-server` y se configura en el fichero /etc/dhcp/dhcpd6.conf. En este fichero se indican valores como el tiempo por defecto de las concesiones, el tiempo de vida de la concesión o el tiempo de renovación por defecto. Además, también se indica en este fichero el DNS por defecto. Finalmente, se declara la subred con su prefijo y el rango de direcciones que el servidor podrá conceder dentro de esa red.

```
default-lease-time 2592000;
preferred-lifetime 604800;
option dhcp-renewal-time 3600;
option dhcp-rebinding-time 7200;
option dhcp6.name-servers 2001:db8:2::45, 2001:db8:2::100;
subnet6 2001:db8:1::/64 {
        interface ens4;
        range6 2001:db8:1::1000 2001:db8:1::2000;
}
```

Adicionalmente, en el fichero /etc/default/isc-dhcp-server hay que descomentar la linea que indica la ruta al fichero de configuración para el servidor y añadir las interfaces por las que este servidor escucha.

```
DHCPDv6_CONF=/etc/dhcp/dhcpd6.conf
OPTIONS="-6"
INTERFACESv6="ens4"
```

También hay que crear el fichero que el servidor usa como base de datos para almacenar la información sobre las concesiones que ofrece a los equipos de la red con sudo touch /var/lib/dhcp/dhcpd6.leases.
Además, se asigna a la interfaz por la que escucha el servidor una IPv6 estática con el mismo prefijo de las direcciones que concede el servidor DHCPv6 pero fuera del rango de direcciones configurado:

```
auto ens4 
iface ens4 inet6 static
        address 2001:db8:1::1
        netmask 64 
```

Finalmente, al arrancar el servidor con `sudo dhcpd -6 -cf /etc/dhcp/dhcpd6.conf`, comienza a funcionar.

Sin embargo, el servidor continúa devolviendo un error. Este error parece estar relacionado con un proceso que ya existe como se muestra en la siguiente línea de la salida del comando `sudo service isc-dhcp-server status`:

![](/assets/img/redes/practica16/img8.png)

Solucionarlo es bastante sencillo: sólo hay que eliminar el fichero asociado al proceso existente y volver a iniciar el servidor.

```bash
sudo rm -r /var/run/dhcpd6.pid
sudo service isc-dhcp-server start
```

# El proceso de autoconfiguración de direcciones por SLAAC

En la siguiente captura de Wireshark se muestra el proceso de autoconfiguración de la dirección IPv6 del PC1 usando SLAAC:

![](/assets/img/redes/practica16/img9.png)

Antes del encendido de la tarjeta de red, la captura de tráfico detecta varios mensajes Router Advertisement (paquetes 9 y 10) desde la IPv6 del router. Estos son los mensajes que el daemon están enviando constantemente a la red para indicar al resto de equipos el prefijo de red que deben usar al asignarse direcciones IPv6.

Cuando se enciende la tarjeta de red, el PC1 envía varios mensajes, entre ellos, dos mensajes multicast. En concreto, el que forma parte del proceso de autoconfiguración por SLAAC es el paquete número 15 de la captura, un mensaje enviado a la IPv6 ff02::2, la dirección multicast que se usa para comunicarse con los routers de la red, y que contiene un mensaje Router Solicitation, en el que se solicita la información necesaria para generar la IPv6 global.

Tras la solicitud del PC1, se captura un paquete, el número 16, que también es de tipo Router Advertisement pero que, a diferencia del resto de paquetes de este tipo, no se dirige a la dirección ff02::1, dirección multicast que envía los mensajes a todos los nodos de la red, sino a la fe80::e42:f1ff:fef4:0, es decir, la IPv6 de enlace local del PC1 desde la que ha solicitado el prefijo de red para configurar su dirección global en el mensaje Router Solicitation anterior. Por tanto, el mensaje número 16 en la captura es la respuesta del router al mensaje número 15.

Después de eso, se siguen sucediendo mensajes Router Advertisement, nuevamente dirigidos a la dirección multicast ff02::1 porque el router los envía de forma automática pero el PC1 ya ha generado su IPv6 a partir del prefijo que el router le ha facilitado en su respuesta al mensaje Router Solicitation.

También es relevante en el proceso de autoconfiguración el paquete número 18 en esta captura de tráfico. Se trata de un mensaje Neighbor Solicitation que el PC1 envía a la dirección de nodo solicitado para comprobar que la dirección que se ha configurado a partir del prefijo aportado por el router no se repite en la red. Como se puede comprobar, ningún otro equipo de la red contesta este mensaje, por tanto, esto significa que esta dirección IPv6 no está duplicada en la red y PC1 la puede usar.

# El proceso de autoconfiguración de direcciones por DHCPv6

En la siguiente captura de tráfico se muestra el proceso de autoconfiguración de la dirección IPv6 del PC2 usando DHCPv6:

![](/assets/img/redes/practica16/img10.png)

Antes de solicitar una configuración IPv6 con sudo dhclient -6, por el cable que conecta al PC2 con el router R1 circulan varios mensajes de tipo multicast como los neigbor solicitation o los router solicitation.

Cuando el PC2 solicita una configuración IPv6 al servidor DHCP, envía un mensaje de tipo solicit (paquete 9 de la captura). Este mensaje es de tpo multicast y se envía a la dirección IPv6 reservada ff02::1:2. Esta dirección reservada se usa para hacer llegar el mensaje a todos los dispositivos de la red. Con este mensaje, el ordenador solicita la respuesta de algún equipo de la red que tenga instalado un servidor DHCPv6 en funcionamiento.

El siguiente mensaje registrado en la captura (paquete 10) es un mensaje advertise, en el que el router contesta la solicitud del PC2. En este caso se trata de un mensaje de tipo unicast que tiene como origen la IP de enlace local del router y como destino la IP de enlace local del PC. Con este mensaje, el router informa al PC2 de que tiene un servidor DHCPv6 que puede atender su petición.

En este mensaje, el servidor DHCP ya incluye información sobre la dirección IPv6 que ofrece al ordenador (2001:db8:1::2000, la última del rango), así como el tiempo de concesión de la configuración.

![](/assets/img/redes/practica16/img11.png)

Estos paquetes también incluyen un identificador del cliente y un identificador del servidor que cuenta con información como el DUID de cada uno de los equipos (un identificador único en numeración hexadecimal), el tipo de conexión, la fecha y hora del mensaje o la dirección MAC de cada uno de los equipos.

![](/assets/img/redes/practica16/img12.png)

Posteriormente, el PC2 envía un mensaje request al router, también de tipo multicast y dirigido a la misma dirección que el anterior. Con este segundo mensaje, el ordenador solicita al servidor DHCP del router la configuración de IPv6 ofrecida en la respuesta anterior.

Finalmente, el servidor DHCPv6 del router contesta con un mensaje de tipo reply en el que, definitivamente, concede la configuración IPv6 indicada al PC2, como se puede ver en el paquete número 12 de la captura.

El resto de paquetes capturados corresponden a mensajes multicast de tipo neigbor solicitation o advertisement o router solicitation que no forman parte de la comunicación por la que el servidor DHCPv6 concede una dirección al PC.

# Acceder a un servidor web con IPv6

El PC2 cuenta con un servidor web Apache. Se puede acceder a él desde el PC1 usando el comando `curl` pero para hacerlo es necesario usar la dirección IPv6 global de PC2 puesto que el servidor web no responde peticiones que lleguen a la dirección de enlace local aunque lo hagan por la misma interfaz y desde maquinas de la misma red.

Para que PC1 pueda llegar es necesario configurar la entrada por defecto de las tablas de enrutamiento de los ordenadores. 

```bash
sudo ip -6 route add default via fe80::ede:a4ff:fec9:0 dev ens4
```
Para comunicarse con el router los equipos de la red usan su dirección de enlace local.

Una vez que la red esta enrutada, el PC1 puede acceder al servidor web del PC2 usando su dirección global con el comando `curl`.

```bash
curl 'http://[2001:db8:1::2000]'
```

><i class="fas fa-lightbulb" aria-hidden="true"></i> Las direcciones IPv6 se cierran entre corchetes en las URL.
{: .notice--primary}

# Acceder al servidor web desde otra red sin hacer NAT

Una nueva máquina se conecta a otra interfaz del router.

Para poder acceder al servidor web de PC2 esta máquina necesita una dirección IPv6 de una red diferente a la de los PC1 y 2:

```bash
ip l set dev ens4 up
sudo ip -6 a add 2001:db8:2::abba dev ens4
```

También necesita una línea en su tabla de enrutamiento que le indique que, por defecto, debe enviar todos los mensajes al router R1:

```bash
sudo ip -6 route add default via fe80::ede:a4ff:fec9:1 dev ens4
```

Además, la interfaz del router que da al PC3 tiene que contar con una dirección global /64 de la misma red que el ordenador en la interfaz que conecta a ambos dispositivos:

```bash
sudo ip -6 a add 2001:db8:2::1/64 dev ens5
```

Con estas condiciones, el PC3 ya puede acceder al servidor web del PC2 desde una red diferente, pasando por un router intermedio y sin necesidad de hacer NAT en ningún punto del recorrido:

```bash
curl 'http://[2001:db8:1::2000]'
```

# Configuración de SLAAC en un router Cisco

Para poder trabajar con IPv6 en routers Cisco se debe habilitar la opción IPv6 unicast routing:

```
R1(config)#ipv6 unicast-routing.
```

Después se asigna la dirección global a la interfaz del router que se conecta a la red a cuyos equipos tiene que enviar la información necesaria a través de SLAAC.

```
R1#conf t
R1(config)#interface fastEthernet 0/0
R1(config-if)#ipv6 address 2001:db8:1::1/64 
R1(config-if)#no shutdown 
R1(config-if)#end
R1#wr
```

Tras arrancar la interfaz, el router ha rellenado automáticamente su tabla de enrutamiento (`show ipv6 route`) con la información de la red a la que está conectado.

Al reiniciar las tarjetas de red de los equipos, ambos renuevan sus direcciones IPv6 usando el protocolo SLAAC y, con estas nuevas direcciones, pueden comunicarse entre ellos a través de sus IPv6 globales.

A diferencia de lo que ocurre en los routers Linux, donde se necesita configurar un daemon para habilitar el protocolo SLAAC para la autoconfiguración de las direcciones IPv6 por parte de los equipos de la red, en los routers Cisco esta opción se incluye por defecto y comienza a funcionar en cuanto se habilita y configura el direccionamiento IPv6 en el router.

Sin embargo, de esta forma no se puede enviar información sobre el DNS a los equipos de la red cómo sí permite el daemon radvd en los routers Linux. Para poder comunicar esta información a los equipos de la red, los routers Cisco usan un método llamado DHCPv6 stateless, que combina el proceso de autoconfiguración de direcciones IPv6 por SLAAC con el protocolo DHCPv6 sólo para trasladar información sobre la dirección del DNS. En este caso, el direccionamiento se otorga por SLAAC y no por DHCPv6.

Para habilitar esta función se crea, en primer lugar, un pool DHCPv6:

```
R1(config)# ipv6 dhcp pool IPV6-STATELESS
```

Después se indica, dentro del pool, la dirección IPv6 del DNS que se va a indicar a los equipos de la red como preferido:

```
R1(config-dhcpv6)# dns-server 2001:db8:1::45
```

Finalmente, se asigna el pool a la interfaz por la que el router contesta las peticiones de la red. Desde el modo de configuración de la interfaz se asignan las direcciones IPv6 de enlace local y global de la interfaz (si no las tenía previamente asignadas) y se vincula el pool DHCPv6 configurado anteriormente. Además, para que el DHCPv6 funcione en modo stateless es necesario activar la bandera other-config, que cambia el bit O de los paquetes DHCP de 0 a 1:

```
R1(config)# interface fastEthernet 0/0
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:1::1/64
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server IPV6-STATELESS
R1(config-if)# no shut
R1(config-if)# end
```

Con esta configuración los equipos de la red reciben una dirección IPv6 por SLAAC pero, además, reciben también la información necesaria para configurar su servidor DNS por defecto.

# Configuración de DHCPv6 en un router Cisco

La configuración del servidor DHCPv6 en los routers Cisco para conceder direcciones IPv6 así como una dirección del servidor DNS por defecto es similar a la contemplada en el apartado anterior durante la configuración del direccionamiento por SLAAC con DHCPv6 stateless.

En este caso, para que le servidor DHCPv6 conceda también direcciones IPv6, se debe usar el modo stateful. La principal diferencia en la configuración del router está en las banderas que se deben activar al asignar el pool DHCP a la interfaz de escucha del router. De esta forma, en primer lugar, tras activar IPv6 en el router se crea un nuevo pool DHCP:

```
R1(config)# ipv6 dhcp pool IPV6-STATEFUL
```

En él se indica el prefijo a partir del cuál se deben generar las direcciones IPv6 que se otorgan a los equipos de la red y la dirección del servidor DNS por defecto:

```
R1(config-dhcpv6)# address prefix 2001:db8:1::/64
R1(config-dhcpv6)# dns-server 2001:db8:1::45
```

Y, finalmente, se asocia el pool DHCP a la interfaz por la que el servidor escucha las peticiones de los equipos de la red local. En este caso, como el modo del servidor es stateful se cambia la bandera M de 0 a 1 con `ipv6 nd managed-config-flag`, así como la bandera A con el comando `ipv6 nd prefix default no-autoconfig`. Esta bandera indica al cliente que no use SLAAC para autoconfigurar su dirección IPv6, sino que tome la que le concede el servidor DHCPv6, de manera que evita posibles problemas derivados de las interferencias entre ambos protocolos. Además, en este paso también se puede asignar una dirección de enlace local y una dirección global a la interfaz, si no las tenía asignadas previamente y se une el pool a ella con ipv6 dhcp server:

```
R1(config)# interface fastEthernet 0/0
R1(config-if)# description Link to LAN
R1(config-if)# ipv6 address fe80::1 link-local
R1(config-if)# ipv6 address 2001:db8:1::1/64
R1(config-if)# ipv6 nd managed-config-flag
R1(config-if)# ipv6 nd prefix default no-autoconfig
R1(config-if)# ipv6 dhcp server IPV6-STATEFUL
R1(config-if)# no shut
R1(config-if)# end
```

Así, el router funciona como servidor DHCPv6 y concede una configuración IPv6 completa a los clientes de la red local que la soliciten.