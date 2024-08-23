---
title:  "Configuración de switches en GNS3"
date:   2024-01-28 01:00:00 +0200
categories: redes
tag: [redes, switch, router, GNS3, VLAN, port bonding, port mirroring, Planificación y Administración de Redes]
---
En este post se muestran varios ejemplos de configuración de switches gestionables en el simulador GNS3. A los dispositivos se les aplican configuraciones como la creación de VLAN, la configuración del port bonding entre switches o el uso del port mirroring para monitorizar el tráfico de la red.

Este caso práctico parte del siguiente escenario:

![](/assets/img/redes/practica9/img1.png)

><i class="fas fa-exclamation-circle" aria-hidden="true"></i> Antes de seguir el desarrollo de este post conviene conocer [cómo importar los dispositivos](/redes/importar-dispostivos-GNS3/) necesarios al simulador GNS3.
{: .notice--primary}

# Creación de dos VLAN

En primer lugar, se debe asignar una dirección IP a los dispositivos conectados a los diferentes swtiches para que estén todos en la misma red. Se usará la siguiente configuración:

- PC1: 192.168.0.1/24
- PC2: 192.168.0.2/24
- PC3: 192.168.0.3/24
- PC4: 192.168.0.4/24
- Servidor de datos: 192.168.0.10/24

Para asignar la dirección IP a un VPCS de GNS3:

```
ip 192.168.0.1/24
save
```
A continuación se establecen las VLAN en los switches de la red:

- Swtich 1:
    - Puerto 0: VLAN 1 tipo dot1q
    - Puerto 1: VLAN 1 tipo dot1q

![](/assets/img/redes/practica9/img2.png)

- Swtich 2:
    - Puerto 0: VLAN 1 tipo dot1q
    - Puerto 6: VLAN 20 tipo access
    - Puerto 7: VLAN 10 tipo access

![](/assets/img/redes/practica9/img3.png)

- Switch 3:
    - Puerto 0: VLAN 1 tipo dot1q
    - Puerto 6: VLAN 10 tipo access
    - Puerto 7: VLAN 20 tipo access

![](/assets/img/redes/practica9/img4.png)

Con esta configuración vemos cómo mientras que el PC2 (192.168.0.2) se puede conectar el PC4 (192.168.0.4), no existe conectividad entre PC2 (192.168.0.2) y PC3 (192.168.0.3):

![](/assets/img/redes/practica9/img5.png)

Aunque los switches por defecto de GNS3 son más limitados que unos de Cisco, por ejemplo, cuentan con una pequeña interfaz de configuración en la que se puede trabajar con los elementos imprescindibles de un switch gestionable, de manera que se puede realizar este ejercicio. En este caso, esto ha sido posible porque esta interfaz de configuración permite trabajar con las VLAN y los enlaces encapsulados dot1q.

Los distintos tipos de puertos que se han usado en este ejercicio para configurar los switches Ethernet de GNS3 han sido access y dot1q. Se debe asignar como tipo “access” aquellos puertos que se van a conectar a los equipos finales, es decir, cada una de las máquinas que van a formar parte de una VLAN. El tipo dot1q, en cambio, hace referencia al protocolo IEEE 802.1Q, que desarrolla un mecanismo que permite a múltiples redes compartir de forma transparente el mismo medio físico, sin problemas de interferencia entre ellas, llamado trunking. También se conoce como dot1Q el protocolo de encapsulamiento usado para implementar este mecanismo en redes Ethernet, tal y como se ha hecho en este ejercicio.

# Configuración de una VLAN asimétrica

## Configuración para permitir el acceso de PC1 y PC3 a Internet

Para conseguir que PC1 y PC3 puedan acceder a Internet hay que configurar el puerto que conecta el switch 1 con la nube NAT como un puerto de tipo “access” que forma parte de la VLAN 10, la misma que los ordenadores PC1 y PC3:
- Swtich 1:
    - Puerto 0: VLAN 1 tipo dot1q
    - Puerto 1: VLAN 1 tipo dot1q
    - Puerto 7: VLAN 10 tipo access

![](/assets/img/redes/practica9/img6.png)

De esta manera, sólo los equipos conectados a la VLAN 10 pueden tener acceso a Internet.

El siguiente paso es configurar la tarjeta de red tanto de PC1 como de PC3 para que puedan solicitar una configuración de red al servidor DHCP que tiene la nube NAT. En los VPCS de GNS3 esto se hace con el comando `dhcp`.

Una vez obtenida la configuración DHCP tanto PC1 como PC3 pueden acceder a Internet.

Sin embargo, PC2 y PC4, que no forman parte de la VLAN 10, en la que está la nube NAT, no pueden hacerlo. De hecho, ni siquiera pueden solicitar una configuración DHCP a la nube NAT porque, tal y como se ve en las siguientes capturas, ambos equipos envían tres mensajes DHCP discover cada vez que se ejecuta el comando dhcp pero éste no llega al servidor DHCP y, por tanto, no reciben ningún mensaje DHCP offer:

![](/assets/img/redes/practica9/img7.png)

## Configuración para permitir el acceso de todos los ordenadores al servidor de datos

Para que todos los ordenadores puedan acceder al servidor de datos se debe modificar la configuración del switch 1 de la siguiente forma:

- Swtich 1:
    - Puerto 0: VLAN 1 tipo dot1q
    - Puerto 1: VLAN 1 tipo dot1q
    - Puerto 6: VLAN 1 tipo dot1q
    - Puerto 7: VLAN 10 tipo access

![](/assets/img/redes/practica9/img8.png)

A continuación, le asignamos una IP dentro de la red local (192.168.0.10) al servidor de datos para que el resto de equipos se pueda conectar a él.

```
ip 192.168.0.10/24
save
```

Para que todos los equipos estén configurados dentro de la misma red, es necesario volver a establecer una dirección IP estática en los ordenadores PC1 y PC3.

Una vez hecho esto, todo parecería indicar que, con esta configuración, todos los equipos de la red deberían tener acceso al servidor de datos, sin embargo, ninguno de ellos lo consigue. Esto se debe a la limitación que presentan los switches instalados por defecto en GNS3 para trabajar con VLAN asimétricas.

En este punto no se puede seguir trabajando con los switches Ethernet por defecto de GNS3. Parece que la configuración que se ha usado para conectar entre sí los diferentes swtiches de la red usando el tipo de puerto dot1q sólo funciona entre este tipo de dispositivos y no permite comunicar ordenadores, servidores y otros equipos con todas las VLAN presentes en la red a la vez.

Una vez encontrada a esta limitación, parece que es momento de continuar usando los router con módulo swtich Cisco 3725.

Para llevar a cabo el proceso de configuración de los routers Cisco 3725 en este escenario, se intentarán seguir las instrucciones proporcionadas por José Domingo Muñoz en la entrada “[Simulando switch Cisco en GNS3](https://www.josedomingo.org/pledin/2014/02/simulando-switch-cisco-en-gns3/)” de su blog.

El primer paso, por tanto, debería ser activar los puertos en todos los switches, sin embargo, al usar el comando indicado, parece que los puertos que se van a usar como switch (fe1/0 - fe1/15) ya están activados en los tres dispositivos y, por tanto, la conectividad entre los cuatro equipos ya es completa.

En este caso, las VLAN no se pueden configurar desde la ventana de configuración de la interfaz gráfica de GNS3, sino que es necesario acceder a la configuración de los switches a través de la terminal. Primero se crean las VLAN.

```
ESW1#vlan database
ESW1(vlan)# vlan 10
ESW1(vlan)# vlan 20
ESW1(vlan)# exit
#write
```

Una vez creadas las dos VLAN que se están usando en esta práctica (10 y 20), se asignan los puertos que corresponden a cada una de ellas.

```
ESW2#conf t
ESW2(config)#interface fastEthernet 1/6
ESW2(config-if)#switchport access vlan 20
ESW2(config-if)#exit
ESW2(config)#interface fastEthernet 1/7
ESW2(config-if)#switchport access vlan 10
ESW2(config-if)#exit
ESW2(config)#exit
ESW2#wr
```

Tras repetir esta operación en los tres switches, se configuran también el trunk.

```
ESW3#conf t
ESW3(config)#interface fastEthernet 1/0
ESW3(config-if)#switchport mode trunk
ESW3(config-if)#switchport trunk allowed vlan 1-1005
ESW3(config-if)#exit
ESW3(config)#exit
ESW3#wr
```

Finalmente, una vez realizada esta configuración, se consigue la conectividad entre PC1 y PC3, por una parte, y PC2 y PC4 por otra.

Además, si se solicita una configuración TCP/IP al servidor DHCP, los equipos conectados a la VLAN 10 también pueden acceder a Internet con esta configuración, mientras que los equipos de la VLAN 20 no tienen acceso a ese servidor DHCP.

Parecería lógico entender que la forma de permitir el acceso de todos los dispositivos al servidor de aplicaciones es conectarlo a la red a través de un puerto en modo trunk. Sin embargo, el resultado es que con esta configuración ni los equipos de la VLAN 10 ni los de la VLAN 20 tienen acceso al servidor.

Esto se debe a que el modo trunk de los puertos del switch Cisco se reserva exclusivamente para las conexiones entre dispositivos similares y sólo se pueden conectar a este tipo de puertos switches o routers. El servidor, aunque sea un dispositivo que debe ser accedido por todas las máquinas de la red, es entendido por estos switches como un dispositivo final y, por tanto, no puede usar puertos de tipo trunk.

Una vez alcanzada esta limitación, parece que otra opción posible para conseguir el objetivo del ejercicio es incluir el puerto al que se conecta el servidor de aplicaciones en ambas VLAN de manera simultánea.

Al intentar implementar esta configuración nos encontramos que los swtiches Cisco empleados en este caso práctico no permiten asignar un mismo puerto del dispositivo a más de una VLAN a la vez. Así, al configurar dos VLAN en el mismo puerto, primero la VLAN 10 y, posteriormente, la VLAN 20, en este caso se observa que en la configuración del switch únicamente se ha guardado la última modificación, de manera que se ha asignado el puerto al que está conectado el servidor de aplicaciones, de manera exclusiva, a la VLAN 20.

![](/assets/img/redes/practica9/img9.png)

De esta forma, el servidor es ahora accesible para los dispositivos que forman parte de esa VLAN pero no para el resto. Además, como la nube NAT forma parte de la VLAN 10, el servidor de aplicaciones tampoco tiene acceso a Internet como el resto de dispositivos de la VLAN 20, de la que forma parte a todos los efectos con esta configuración.

Para poder configurar un enrutamiento entre diferentes VLAN se debe asignar una dirección IP diferente a los equipos que forman parte de cada una de ellas. En este caso, los equipos de la VLAN 10 tendrán unas ip del rango 192.168.10.0/24 y los de la VLAN 20 estarán en el rango de la red 192.168.20.0/24.

A continuación, se debe configurar la interfaz de switch virtual (SVI) que va a permitir usar el switch como un dispositivo de nivel 3 y, por tanto, asociar una dirección IP a cada VLAN para poder crear un enrutamiento entre ellas.

```
ESW1#conf t
ESW1(config)#interface vlan 10
ESW1(config-if)#ip add 192.168.10.1 255.255.255.0
ESW1(config-if)#no shutdown
ESW1(config-if)#exit
ESW1(config)#interface vlan 20
ESW1(config-if)#ip add 192.168.20.1 255.255.255.0
ESW1(config-if)#no shutdown
ESW1(config-if)#exit
ESW1(config)#exit
ESW1#wr
```

Finalmente, tras configurar las IP de ambas VLAN en todos los routers, se debe habilitar el enrutamiento por IP para poder usar los puertos de switch como puertos de nivel 3 con el comando ip routing en todos los switches.

```
ESW3#conf t
ESW3(config)#ip routing
ESW3(config)#exit
ESW3#wr
```

En este punto, cada ordenador llega tanto a los dispositivos de su propia VLAN como a los de otras aunque llama la atención al diferencia entre el tiempo que se tarda en completar un ping entre dos ordenadores de la misma VLAN (0,676 ms entre PC1 y PC3, por ejemplo) y el que se necesita para completar un ping entre dos ordenadores de VLAN diferente (16,234 ms entre PC1 y PC4, por ejemplo).

![](/assets/img/redes/practica9/img10.png)

Para permitir el acceso de todos los dispositivos en todas las VLAN al servidor de datos se ha configurado el puerto f1/6 del switch 1, conectado al servidor, como puerto enrutado de nivel 3 usando el comando no switchport. Esto debería permitir que se pueda dotar a este puerto de una configuración IPv4 para poder hacer un enrutamiento entre el servidor y las VLAN.

Al intentar asignar una IP a este puerto enrutado el propio router limita el uso de direcciones IP para que la del puerto f1/6 no coincida con ninguna del rango ni de la VLAN 10, ni de la VLAN 20.

```
ESW1#conf t
ESW1(config)#interface fastEthernet 1/6
ESW1(config-if)#ip add 192.168.10.1 255.255.255.0
ESW1(config-if)#no switchport
ESW1(config-if)#no shutdown
ESW1(config-if)#end
ESW1#wr
```

Ahora que el servidor ya está conectado a un puerto enrutado se puede configurar este enrutamiento para permitir a las VLAN 10 y 20 el acceso al servidor. En el switch 1:

```
ESW1#conf t
ESW1(config)#router ospf 10
ESW1(config-router)#network 192.168.10.0 0.0.0.255 area 0
ESW1(config-router)#network 192.168.20.0 0.0.0.255 area 0
ESW1(config-router)#network 192.168.1.0 0.0.0.255 area 0
ESW1(config-router)#end
ESW1#wr
```

De esta forma, tanto las VLAN 10 y 20 como el servidor de datos quedan correctamente enrutados (f1/6).

![](/assets/img/redes/practica9/img11.png)

Con esta configuración, todos los ordenadores de la red pueden acceder al servidor de datos (192.168.1.2). Sin embargo, también pueden comunicarse entre las diferentes VLAN que se han creado. Para garantizar que las VLAN cumplen su función y generan dos redes estancas que no se pueden comunicar entre ellas, se deben implementar listas de control de acceso (ACL) en los routers. Para hacerlo, el primer paso debe ser crear en cada uno de los routers que conectar equipos que pertenecen a una única VLAN las listas de acceso.

En el router 2, se deben indicar dos ACL diferentes. Cada una de ellas se deberá asignar posteriormente a una de las interfaces que conecta cada equipo de la VLAN 10 o de la VLAN 20. Para el equipo de la VLAN 10 se configura la lista de acceso 10, que le impide recibir paquetes provinientes de las IP del rango 192.168.20.0/24, es decir, de la VLAN 20. En esta caso, junto a la IP no se indica la máscara de red, sino una máscara llamada wildcard, que es la inversa a la máscara de red y es el tipo de máscara que el sistema operativo de los dispositivos de Cisco usa para la configuración de las listas de control de acceso. La segunda orden de esta ACL es permitir todo el resto del tráfico que circula por la red.

Para el equipo de la VLAN 20 se configura la lista de acceso 20, que le impide recibir paquetes privinietnes de las IP del rango 192.168.10.0/24, es decir, de la VLAN 20 y le permite recibir todo el resto del tráfico que circula por la red.

```
ESW2#conf t
ESW2(config)#access-list 10 deny 192.168.20.0 0.0.0.255
ESW2(config)#access-list 10 permit any
ESW2(config)#access-list 20 deny 192.168.10.0 0.0.0.255
ESW2(config)#access-list 10 permit any
ESW2(config)#exit
ESW2#wr
```

Una vez que se han creado todas las ACL que se necesitan para regular el tráfico que pasa por este router, se debe asignar cada una de ella a una interfaz del dispositivo. En este caso se asigna a la interfaz f1/7 (que conecta el switch con el PC1) la ACL 10 y a la interfaz f1/6 (que conecta el switch con el PC2) la ACL 20.

Ambas ACL se asignan en modo “in” de manera que el router debe filtrar los paquetes que van a salir por esa interfaz y permitir o denegar su paso según los criterios especificados anteriormente. Otra opción es asignar las ACL a las interfaces en modo “out”, lo que impediría que los paquetes dirigidos a las direcciones denegadas saliesen a circular por la red, reduciendo así el tráfico dentro de la misma. Sin embargo, la documentación de Cisco recomienda implementar las ACL lo más cerca posible del destino. Además, el modelo de router con el que se está trabajando en esta práctica no soporta la configuración “out” en las ACL.

```
ESW2#conf t
ESW2(config)#interface fastEthernet 1/7
ESW2(config-if)#ip access-group 10 in
ESW2(config-if)#exit
ESW2(config)#interface fastEthernet 1/6
ESW2(config-if)#ip access-group 20 in
ESW2(config-if)#end
ESW2#wr
```

Pero a pesar de haber establecido esta configuración de listas de accesos tanto en el switch 2 como en el 3, los ordenadores de las diferentes VLAN de la red siguen pudiendo establecer comunicación entre ellos a través de ping.

# Configuración de un port bonding

El primer paso para crear el portbonding es configurar todos los puertos implicados con las mismas características, es decir, todos ellos deben estar en el mismo modo, en este caso, trunk y pertenecer a la misma VLAN o rango de VLAN.

```
ESW1#conf t
ESW1(config)#interface fastEthernet 1/1
ESW1(config-if)#switchport mode trunk
ESW1(config-if)#switchport trunk allowed vlan 1-1005
ESW1(config-if)#exit
ESW1(config)#interface fastEthernet 1/3
ESW1(config-if)#switchport mode trunk
ESW1(config-if)#switchport trunk allowed vlan 1-1005
ESW1(config-if)#end
ESW1#wr
```

Tras repetir esta operación en todos los switches, se debe configurar el portbonding en cada uno de ellos de la siguiente forma:

```
ESW1#conf t
ESW1(config)#interface range fastEthernet 1/1 , fastEthernet 1/3
ESW1(config-if-range)#channel-group 3 mode on
ESW1(config-if-range)#end
ESW1#wr
```

De esta manera, la configuración queda establecida con dos channel-group llamados grupo 2 (entre el switch ESW1 y el switch ESW2) y grupo 3 (entre el switch ESW1 y el switch ESW3), cada uno de ellos con dos puertos asignados en cada uno de los routers.

![](/assets/img/redes/practica9/img12.png)

En este punto el port bonding está configurado. Se puede demostrar su correcta configuración a través de las diferentes herramientas del switch Cisco. Por ejemplo, con el comando `show interfaces port-channel 2` en el switch ESW1, se muestra que este port bonding entre el switch de cabecera y el switch 2 cuenta con un ancho de banda de 20000 Kbps y una latencia de 1000 microsegundos. Además, indica que se trata de una conexión de tipo full duplex a 100Mbps y que el port bonding está formado pro dos miembros: las interfaces Fa1/0 y Fa1/2.

![](/assets/img/redes/practica9/img13.png)

En el otro extremo de este port-channel se muestra la misma información especificando está vez las interfaces Fa1/0 y Fa1/1 como miembros de este prot bonding en el switch ESW2.

![](/assets/img/redes/practica9/img14.png)

Por otra parte, el port-channel 3, que conecta el switch de cabecera con el switch ESW3, también presenta un ancho de banda de 200000 Kbps y una latencia de 1000 microsegundos. También se identifica como una conexión de tipo full duplex a 100Mbps y cuenta con los puertos Fa1/1 y Fa1/3 de este switch.

![](/assets/img/redes/practica9/img15.png)

En el otro extremo del cable, el switch 3 muestra la misma información indicando esta vez que las interfaces que forman parte de este port bonding son los puertos Fa1/0 y Fa1/1 de este switch.

![](/assets/img/redes/practica9/img16.png)

El comando `show etherchannel <n> summary`, donde `<n>` es el número de portchannel sobre el que se desea obtener información muestra el estado del port bonding. Para describir el estado, este comando usa diferentes flags para indicar el estado de los elementos del portchannel. Por ejemplo, el flag D indica que un puerto del portchannel está apagado, el flag P indica que un puerto forma parte de un portchannel y el flag s indica que un puerto está suspendido. Para los portchannel que usan el protocolo LACP, el flag H puede indicar que se encuentra en modo standby.

Además, otros flags también se usan para indicar el estado del propio portbonding. Algunos de ellos son el flag R para indicar que el portchannel es de nivel 3, el S para indicar que es de nivel 2, los flags U o N para indicar si está o no en uso, respectivamente o el flag f para indicar un fallo en la localización de los puertos.

![](/assets/img/redes/practica9/img17.png)

# Configuración de un port mirroring

En primer lugar, se conecta el PC5 a uno de los puertos libres del switch. En este caso, la conexión se ha realizado entre el PC5 y el puerto f1/15 del switch ESW1.

Tras establecer esta conexión, se debe configurar el router 1 para que el PC5 pueda acceder al tráfico de la VLAN 10 que llega por su puerto f1/0.

Sin embargo, tras incluir el port-channel 2 (formado por los puertos f1/0 y f1/2) del switch ESW1 en la sesión de monitorización, al intentar filtrar el tráfico que se busca monitorizar por la VLAN, en este caso la 10, para poder discernir el tráfico proveniente del PC1 del proveniente del PC2, el switch 1 devuelve un mensaje de error que indica que el dispositivo no soporta el filtro por VLAN.

Una forma de solucionar este problema es conectar el PC5 al switch ESW2, donde no es necesario filtrar por el tráfico por vlan puesto que el tráfico que circula por su puerto f1/7 es únicamente el del PC1. Para conectar el PC5 al switch ESW2 se va a usar, de nuevo, en este caso, el puerto f1/15 de este switch.

En este caso ya se puede configurar la sesión de monitorización indicando como puerto de origen el f1/7 y como puerto de destino el f1/15.

```
ESW2#conf t
ESW2(config)#monitor session 1 source interface fastEthernet 1/7
ESW2(config)#monitor session 1 destination interface fastEthernet 1/15
ESW2(config)#end
ESW2#wr
```

Así, se puede comenzar a monitorizar el tráfico de la red. Si se hace una captura del tráfico que circula entre el PC1 y el resto de la red se aprecian diferentes bloques claramente diferenciados.

![](/assets/img/redes/practica9/img18.png)

En primer lugar (en azul), se observan un conjunto de mensajes del protocolo DHCP que circulan por la red. En concreto, se trata de mensajes de tipo DHCP Discover, Request y Decline todos ellos enviados a la dirección IP de difusión de la red (255.255.255.255) y que no tienen respuesta por parte del PC1.

Posteriormente (en amarillo) se produce un proceso de comunicación del protocolo ARP. Se cuentan varios mensajes enviados por PC1 a la dirección de difusión de la red para descubrir la MAC de destino con la que tiene que establecer la comunicación. En los tres primeros de estos mensajes, el dispositivo que funciona como puerta de enlace (con la IP 192.168.122.1) busca la MAC que corresponde a la tarjeta de red que tiene asociada la IP 192.168.122.65. El cuarto, es un mensaje ARP que envía PC1 para descubrir la dirección MAC a la que debe enviar al mensaje que tiene como IP de destino la dirección 192.168.122.1, la de la puerta de enlace.

Finalmente, en el quinto mensaje del protocolo ARP que aparece en la captura, el PC1 recibe la información de la dirección MAC asociada a la puerta de enlace de la red.

A continuación, sigue un bloque de una cantidad mayor de mensajes del protocolo ICMP (en rosa) que reflejan el intercambio de información que se produce al hacer ping entre el PC1 y una dirección IP de Internet, en este caso, se ha usado la 8.8.8.8.

Al final de la captura, hay otros dos mensajes ARP (en amarillo) en los que el PC1 recibe un mensaje de la puerta de enlace preguntando su dirección MAC y, posteriormente, PC1 contesta esa petición.