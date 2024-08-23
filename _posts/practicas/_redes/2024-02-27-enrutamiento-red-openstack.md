---
title:  "Cómo enrutar una red en OpenStack"
date:   2024-02-27 01:00:00 +0200
categories: redes
tag: [redes, Planificación y Administración de Redes, OpenStack, enrutamiento, Debian, tcpdump, virtualización, router linux, ip route]
excerpt: 
---
OpenStack es un proyecto de cloud computing (computación en la nube) de software libre y código abierto. Ofrece una esctructura como servicio (IaaS) y permite virtualizar equipos en los servidores en los que esté configurado. Además, la plataforma permite crear redes virtuales, de manera que las diferentes máquinas virtuales pueden comunicarse entre sí. En este post se recoge un caso práctico en el que se configura el enrutamiento de una pequeña red de máquinas virtuales creadas en OpenStack.

# Creación de la red en OpenStack

Las redes en OpenStack se pueden crear de forma manual, creando cada una de las máquinas y configurando la conexión entre ellas a través de la interfaz gráfica de la plataforma pero también se pueden crear de forma automatizada. Este tipo de creación de máquinas virtuales en lote requiere la creación previa de un fichero YAML como plantilla en el que se incluye la información necesaria para la creación de las máquinas y redes. Desde el menú "stacks" se puede lanzar la creación de una plantilla YAML plsando el botón "lanzar stack".

Para este caso práctico se usa un escenario formado por seis máquinas, de las cuales tres funcionan como PC y las otras tres como routers.

![](/assets/img/redes/practica11/img1.png)

# Configuración de los nodos

## PC1

Puesto que sólo está conectado a un router por su única tarjeta de red, la tabla de enrutamiento de PC1 se puede simplificar hasta dejarla en una única línea.

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Por defecto | R1: 172.23.1.8 | ens3 |

Para trasladar esta tabla a la configuración del ordenador se puede usar el comando `ip route`: 

```bash
ip route add default via 172.23.1.8
```

## PC2

Puesto que sólo está conectado a un router por su única tarjeta de red, la tabla de enrutamiento de PC2 se puede simplificar hasta dejarla en una única línea.

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Por defecto | R2: 172.25.2.255 | ens3 |

Para trasladar esta tabla a la configuración del ordenador se puede usar el comando `ip route`:

```bash
ip route add default via 172.25.2.255
```

## PC3

Puesto que sólo está conectado a un router por su única tarjeta de red, la tabla de enrutamiento de PC3 se puede simplificar hasta dejarla en una única línea.

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Por defecto | R3: 172.27.1.204 | ens3 |

Por el mismo motivo que en los casos anteriores, para PC3 tampoco es necesario trasladar esta tabla a la configuración de la máquina.

```bash
ip route add default via 172.27.1.204
```

## Router 1

Este router sólo se conecta a un ordenador y otro router. Por tanto, su tabla de enrutamiento sólo necesitará dos entradas: la de la interfaz por la que se comunica con el ordenador al que está conectado y la de la interfaz por la que se comunica con el resto de la red a través del router 2.

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Por defecto | R2: 172.24.2.230 | ens3 |

Para trasladar esta tabla a la configuración de la máquina se puede usar el comando `ip route`:

```bash
ip route add default via 172.24.2.230
```

Además, se debe activar en este dispositivo el bit de forwarding, por ejemplo, modificando el fichero /proc/sys/net/ipv4/ip_forward:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

## Router 2

El router 2 está conectado a un ordenador pero también a dos routers. Esto lo convierte en un dispositivo que deberá enrutar una gran cantidad de tráfico en la red y, además, hace que su tabla de enrutamiento sea algo más compleja que las del resto de dispositivos porque no se pueden simplificar tantas entradas.

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| R1 | R1: 172.24.2.206 | ens3 |
| R3 | R3: 172.26.2.36 | ens5 |
| PC1 | R1: 172.24.2.206 | ens3 |
| PC2 | 172.25.2.142 | ens4 |
| PC3 | R3: 172.26.2.36 | ens5 |

Para trasladar esta tabla a la configuración de la máquina se puede usar el comando `ip route`:

```bash
ip route add 172.24.0.0/16 via 172.24.2.206
ip route add 172.26.0.0/16 via 172.26.2.36
ip route add 172.23.0.0/16 via 172.24.2.206
ip route add 172.27.0.0/16 via 172.26.2.36
```

Además, se debe activar en este dispositivo el bit de forwarding, por ejemplo, modificando el fichero /proc/sys/net/ipv4/ip_forward:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

## Router 3

Este router sólo se conecta a un ordenador y otro router. Por tanto, su tabla de enrutamiento sólo necesitará dos entradas: la de la interfaz por la que se comunica con el ordenador al que está conectado y la de la interfaz por la que se comunica con el resto de la red a través del router 2.

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Por defecto | R2: 172.26.0.146 | ens3 |

Puesto que ya hay conectividad entre PC3 y el router 3, para trasladar esta tabla a la configuración de la máquina se puede usar el comando ip route y, a continuación, se debe activar en este dispositivo el bit de forwarding, por ejemplo, modificando el fichero /proc/sys/net/ipv4/ip_forward:

```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

Una vez que se ha configurado el escenario de acuerdo a la descripción previamente expuesta, todas las máquinas que lo conforman pueden comunicarse entre sí.

# Captura del tráfico entre PC1 y PC3 en router 2

## Captura con `tcpdump`

Con el comando `tcpdump`, la opción `-vvv` que aporta la mayor cantidad de información posible y el argumento host 172.23.1.187 and 172.27.1.204 se puede filtrar el tráfico que circula por la red entre PC1 y PC3 en cualquiera de los dos routers por los que pasa: el router 2 o el router 3.

```bash
tcpdump -vvv host 172.23.1.187 and 172.27.1.204
```

En el caso de la siguiente imagen, el tráfico que se ha capturado en el router 2.

La captura se produce en un momento en el que la red ya estaba totalmente configurada y en el que se había estado trabajando en el enrutamiento de los diferentes dispositivos de la red recientemente. Esto hace que en la captura de tráfico que se muestra no aparezcan paquetes que usan el protocolo ARP para descubrir las direcciones IP de los dispositivos vecinos puesto que las tablas de memoria ARP tienen esa información almacenada.

![](/assets/img/redes/practica11/img2.png)

Por tanto, en la captura de tráfico se recogen 18 paquetes que han pasado por la tarjeta de red del router 2 que se ha usado para realizarla. En ella, se alternan las líneas tanto de petición como de respuesta de un ping que se ha enviado desde el PC1 al PC3. En cada una de estas líneas se incluye, en primer lugar, un código de tiempo con la hora a la que se ha capturado el paquete. A continuación, se muestra una serie de datos relevantes para el análisis del tráfico capturado. Algunos de ellos tienen una relevancia mayor que otros. Por ejemplo, se muestra el ttl de cada paquete o el protocolo que se está usando, en este caso ICMP (ping).

Posteriormente se muestra la IP de origen y destino de cada uno de los paquetes capturados. En el caso de las peticiones, el origen es la IP del PC1 (172.23.1.187) y el destino es la IP del PC3 (172.27.1.204). En las líneas que se corresponden con los paquetes de respuesta del ping, el orden de las direcciones IP es el inverso puesto que el origen de estos mensajes es el PC3 y su destino el PC1. Posteriormente, la captura especifica el tipo de paquete del que se trata, bien ICMP echo request si es el ping que envía el PC1 al PC3 o bien ICMP echo reply si es la respuesta de ping que el PC3 devuelve al PC1. También se muestra el número de secuencia de cada paquete dentro del ping, así como la longitud del paquete: 64 bits.

## Captura con tshark

### Instalación de tshark

Para instalar el paquete tshark en el router 2 (en el que se va a realizar la captura) es necesario conectar el equipo a Internet. En primer lugar  se debe conectar de alguna manera  este escenario con la red externa de OpenStack que permite accede a Internet. Un opción es crear un nuevo router y conectarlo tanto a la red exterior como a un dispositivo del escenario de enrutamiento, en este caso, el router 2.

![](/assets/img/redes/practica11/img3.png)

Posteriormente, se debe añadir una interfaz al router:

![](/assets/img/redes/practica11/img4.png)

Y, en la ventana de configuración de la nueva interfaz se debe indicar la red de la que va a formar parte (en este caso la 172.24.0.0/16), así como la dirección IP que funcionará como puerta de enlace para la red:

![](/assets/img/redes/practica11/img5.png)

Una vez que la red ya tiene esta conexión física con Internet, el router 2 ya puede hacer ping fuera de la red, por ejemplo, a 8.8.8.8.

![](/assets/img/redes/practica11/img6.png)

Pero para que pueda resolver nombres de dominio y acceder a los respositorios de debian es necesario configurar el DNS. Esto se puede hacer añadiendo una línea al fichero /etc/resolvconf indicando la dirección de un servidor DNS a través del comando resolvectl, teniendo en cuenta que se debe reiniciar el servicio después:

```bash
resolvectl dns ens3 8.8.8.8
systemctl restart networkd
```

Con esta configuración ya se pueden resolver nombres de dominio y, por tanto, instalar paquetes desde los repositorios de Debian.

### Captura con tshark

En el caso de la captura con tshark, la información que se recoge es muy similar a la que se encuentra en la captura con tcpdump:

![](/assets/img/redes/practica11/img7.png)

La mayor parte del tráfico que se registra es tráfico de petición de respuesta del ping que hace PC1 (172.23.1.187) a PC3 (172.27.1.204). En estos paquetes se puede ver la dirección IP de origen y de destino, el protocolo que usan (ICMP) así como su tamaño (98 bytes) y su tipo (Echo (ping) request o reply según si se trata de una petición o una respuesta). También se indica el número de secuencia dentro del ping y el ttl. Además, de la misma manera que se muestra en la interfaz gráfica de wireshark, se indica a qué petición corresponde cada respuesta del ping.

En esta captura aparecen, además, dos paquetes que no habían aparecido en la captura realizada con tcpdump y que corresponden a una petición y una respuesta ARP. En ellos se ve cómo primero el equipo con la IP 172.24.0.2 (un servidor DHCP) hace una petición ARP para conocer la dirección MAC del equipo con la IP 172.24.2.230 (el router 2). A continuación el router 2 contesta a esa petición con su dirección MAC.

A diferencia de las peticiones ARP habituales, en este caso, el equipo con la IP 172.24.0.2 envía la petición no a la dirección de broadcast (FF:FF:FF:FF:FF:FF) sino a la MAC del router 2. De esta manera, en la captura de estos dos paquetes se muestra, en primer lugar la MAC de origen, a continuación la de destino, después el protocolo usado (ARP) y el tamaño del paquete (42 bytes). Finalmente, se incluye también el contenido del mensaje en formato legible, ya sea la pregunta o la respuesta.