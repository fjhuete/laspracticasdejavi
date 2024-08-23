---
title:  "Configuración de ACL en routers Cisco"
date:   2024-03-03 02:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, Cisco, ACL, los juegos del hambre, hunger games]
---
Las ACL o listas de control de acceso son un mecanismo que permite controlar el tráfico que atraviesa en router en una red. En este post se demuestra el funcionamiento de las ACL en los routers Cisco con un escenario basado en los personajes de la saga "Los juegos del hambre".

![](/assets/img/redes/practica12/img1.png)

En concreto, el router que se usa en este caso práctico es el Cisco 7200. 

# Importar el router Cisco 7200

El 7200 es el router Cisco más popular en el marketplace de GNS3. Para instalarlo el primer paso es descargar la imagen desde este [marketplace](https://gns3.com/marketplace/appliances/cisco-7200).

A continuación se debe importar a GNS3 desde el menú de añadir plantilla:

![](/assets/img/redes/practica12/img2.png)

Después se elige el modelo en la lista de routers.

![](/assets/img/redes/practica12/img3.png)

Y se importa la imagen descargada desde el marketplace:

![](/assets/img/redes/practica12/img4.png)

Tras confirmar la instalación, el dispositivo ya está disponible en GNS3.

# Asignación de direcciones IP

Para este escenario se usan los VPCS de GNS3 como ordenadores. 

El direccionamiento IP de los VPCS del escenario es el siguiente:

- Marvel: 10.0.1.2
- Glimmer: 10.0.1.3
- Cato: 10.0.2.2
- Clover: 10.0.2.3
- Thresh: 10.0.11.2
- Rue: 10.0.11.3
- Peeta: 10.0.12.2
- Katniss: 10.0.12.3

A cada uno de los ordenadores se les asigna la IP indicada usando el comando `ip`. Junto a la dirección IP se indica también su máscara de red y la IP de la puerta de enlace al resto de redes del escenario que, en este caso coincide en las cuatro redes con la IP de la interfaz del router que se conecta a cada swtich.

```
ip 10.0.1.2/24 10.0.1.1
```

El direccionamiento IP de los routers del escenario es el siguiente:

- R1 f0/0: 10.0.1.1
- R1 f0/1: 172.23.0.1
- R1 f1/0: 192.168.1.2
- R2 f0/0: 10.0.11.1
- R2 f0/1: 172.23.0.2
- R2 f1/0: 172.24.0.1
- R2 f1/1: 192.168.11.2
- R3 f0/0: 10.0.2.1
- R3 f0/1: 172.25.0.1
- R3 f1/0: 192.168.2.2
- R4 f0/0: 10.0.12.1
- R4 f0/1: 172.24.0.2
- R4 f1/0: 192.168.12.2
- R4 f1/1: 172.25.0.2
- R5 f0/0: 192.168.11.1
- R5 f0/1: 192.168.12.1
- R5 f1/0: 192.168.1.1
- R5 f1/1: 192.168.2.1
- R5 f2/0: 10.0.13.1

Para asignar las direcciones IP a los routers se usa el comando ip address seguido de la dirección IP y la máscara de red desde el modo  de configuración de cada una de las interfaces de los routers.

```
R1#conf t
R1(config)#interface fastEthernet 0/0
R1(config-if)#ip address 10.0.1.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface fastEthernet 0/1
R1(config-if)#ip address 172.23.0.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit
R1(config)#interface fastEthernet 1/0
R1(config-if)#ip address 192.168.1.2 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#end
R1#write
```

# Enrutamiento del escenario

En primer lugar se permite la comunicación entre todos los dispositivos del escenario y el primer paso para garantizar la comunicación entre todos los dispositivos de la red es crear sus tablas de enrutamiento.

## Tablas de enrutamiento

### Marvel

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R1 f0/0 (10.0.1.1) | eth0 |

### Glimer

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R1 f0/0 (10.0.1.1) | eth0 |

### Cato 

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R3 f0/0 (10.0.2.1) | eth0 |

### Clover 

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R3 f0/0 (10.0.2.1) | eth0 |

### Thresh

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R2 f0/0 (10.0.11.1) | eth0 |

### Rue 

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R2 f0/0 (10.0.11.1) | eth0 |

### Peeta

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R4 f0/0 (10.0.12.1) | eth0 |

### Katniss

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 0.0.0.0 | R4 f0/0 (10.0.12.1) | eth0 |

### Router 1

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Distrito 1 (10.0.1.0) | 0.0.0.0 | f0/0 |
| Distrito 2 (10.0.2.0) | R5 f1/0 (192.168.1.1) | f1/0 |
| Distrito 11 (10.0.11.0) | R2 f0/1 (172.23.0.2) | f0/1 |
| Distrito 12 (10.0.12.0) | R2 f0/1 (172.23.0.2) | f0/1 |
| R2 (172.23.0.0) | 0.0.0.0 | f0/1 |
| R2 (192.168.11.0) | R2 f0/1 (172.23.0.2)| f0/1 |
| R3 (172.25.0.0) | R5 f1/0 (192.168.1.1)| f1/0 |
| R3 (192.168.2.0) | R5 f1/0 (192.168.1.1) | f1/0 |
| R4 (172.24.0.0) | R2 f0/1 (172.23.0.2) | f0/1 |
| R4 (192.168.12.0) | R2 f0/1 (172.23.0.2) | f0/1 |
| R5  (192.168.1.0) | 0.0.0.0 | f1/0 |
| R5 (10.0.13.0) | R5 f1/0 (192.168.1.1) | f1/0 |

### Router 2

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Distrito 1 (10.0.1.0) | R1 f0/1 (172.23.0.1) | f0/1 |
| Distrito 2 (10.0.2.0) | R4 f0/1 (172.24.0.2) | f1/0 |
| Distrito 11 (10.0.11.0) | 0.0.0.0 | f0/0 |
| Distrito 12 (10.0.12.0) | R4 f0/1 (172.24.0.2) | f1/0 |
| R1 (172.23.0.0) | 0.0.0.0 | f0/1 |
| R1 (192.168.1.0) | R1 f0/1 (172.23.0.1) | f0/1 |
| R3 (172.25.0.0) | R4 f0/1 (172.24.0.2) | f1/0 |
| R3 (192.168.2.0) | R4 f0/1 (172.24.0.2) | f1/0 |
| R4 (172.24.0.0) | 0.0.0.0 | f1/0 |
| R4 (192.168.12.0) | R4 f0/1 (172.24.0.2) | f1/0 |
| R5  (192.168.1.0) | R5 f0/0 (192.168.11.1) | f0/1 |
| R5 (10.0.13.0) | R5 f0/0 (192.168.11.1) | f0/1 |

### Router 3

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Distrito 1 (10.0.1.0) | R5 f1/1 (192.168.2.1) | f1/0 |
| Distrito 2 (10.0.2.0) | 0.0.0.0 | f0/0 |
| Distrito 11 (10.0.11.0) | R4 1/1 (172.25.0.2) | f0/1 |
| Distrito 12 (10.0.12.0) | R4 1/1 (172.25.0.2) | f0/1 |
| R1 (172.23.0.0) | R5 f1/1 (192.168.2.1) | f1/0 |
| R1 (192.168.1.0) | R5 f1/1 (192.168.2.1) | f1/0 |
| R2 (172.23.0.0) | R4 1/1 (172.25.0.2) | f0/1 |
| R2 (192.168.11.0) | R4 1/1 (172.25.0.2) | f0/1 |
| R4 (172.24.0.0) | R4 1/1 (172.25.0.2) | f0/1 |
| R4 (192.168.12.0) | R4 1/1 (172.25.0.2) | f0/1 |
| R5  (192.168.1.0) | R5 f1/1 (192.168.2.1) | f1/0 |
| R5 (10.0.13.0) | R5 f1/1 (192.168.2.1) | f1/0 |

### Router 4

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Distrito 1 (10.0.1.0) | R2 f1/0 (172.24.0.1) | f0/1 |
| Distrito 2 (10.0.2.0) | R3 f0/1 (172.25.0.1) | f1/1 |
| Distrito 11 (10.0.11.0) | R2 f1/0 (172.24.0.1) | f0/1 |
| Distrito 12 (10.0.12.0) | 0.0.0.0 | f0/0 |
| R1 (172.23.0.0) | R2 f1/0 (172.24.0.1) | f0/1 |
| R1 (192.168.1.0) | R2 f1/0 (172.24.0.1) | f0/1 |
| R2 (172.23.0.0) | R2 f1/0 (172.24.0.1) | f0/1 |
| R2 (192.168.11.0) | R2 f1/0 (172.24.0.1) | f0/1 |
| R3 (172.25.0.0) | 0.0.0.0 | f1/1 |
| R3 (192.168.2.0) | R3 f0/1 (172.25.0.1) | f1/1 |
| R5  (192.168.1.0) | R5 f0/1 (192.168.12.1) | f1/0 |
| R5 (10.0.13.0) | R5 f0/1 (192.168.12.1) | f1/0 |

### Router 5

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Distrito 1 (10.0.1.0) | R1 f1/0 (192.168.1.2) | f1/0 |
| Distrito 2 (10.0.2.0) | R3 f1/0 (192.168.2.2) | f1/1 |
| Distrito 11 (10.0.11.0) | R2 f1/1 (192.168.11.2) | f0/0 |
| Distrito 12 (10.0.12.0) | R4 f1/0 (192.168.12.2) | f0/1 |
| R1 (172.23.0.0) | R1 f1/0 (192.168.1.2) | f1/0 |
| R1 (192.168.1.0) | 0.0.0.0 | f1/0 |
| R2 (172.23.0.0) | R2 f1/1 (192.168.11.2) | f0/0 |
| R2 (192.168.11.0) | 0.0.0.0 | f0/0 |
| R3 (172.25.0.0) | R3 f1/0 (192.168.2.2) | f1/1 |
| R3 (192.168.2.0) | 0.0.0.0 | f1/1 |
| R4 (172.24.0.0) | R4 f1/0 (192.168.12.2) | f0/1 |
| R4 (192.168.12.0) | 0.0.0.0 | f0/1 |

Para trasladar estas tablas de enrutamiento a los diferentes dispositivos que conforman el escenario se usa el comando `ip route` en los routers Cisco:

```
R1#conf t
R1(config)#ip route 10.0.2.0 255.255.255.0 192.168.1.1
R1(config)#ip route 10.0.3.0 255.255.255.0 172.23.0.2
R1(config)#ip route 10.0.11.0 255.255.255.0 172.23.0.2
R1(config)#ip route 10.0.12.0 255.255.255.0 172.23.0.2
R1(config)#ip route 192.168.11.0 255.255.255.0 172.23.0.2
R1(config)#ip route 172.24.0.0 255.255.255.0 172.23.0.2
R1(config)#ip route 192.168.10.0 255.255.255.0 172.23.0.2
R1(config)#ip route 172.25.0.0 255.255.255.0 192.168.1.1
R1(config)#ip route 192.168.2.0 255.255.255.0 192.168.1.1
R1(config)#ip route 10.0.13.0 255.255.255.0 192.168.1.1
R1(config)#end
R1#write
```

# Creación de las ACL

## Diseño de las ACL

En primer lugar, para evitar que a los ordenadores de la red de los superpijos lleguen mensajes del resto de dispositivos se pueden implementar varias ACL. Una opción sería usar ACL extendidas en los router por los que salen al resto de redes los ordenadores de cada una de las redes o distritos, de esta manera, se podría filtrar, en el router de origen, el tráfico que se dirija hacia el distrito de los superpijos desde cada una del resto de las redes. Otra opción es usar una ACL estándar en el router 1 que impida a esta red recibir tráfico del resto de redes del escenario.

Ambas opciones tienen sus ventajas y sus inconvenientes. Por una parte, la primera opción implica usar ACL extendidas en tres de los routers del escenario, lo que supone un implementación algo más compleja para conseguir el objetivo propuesto. Por otra parte, la segunda opción implica que todo el tráfico que se genere en el escenario dirigido a la red de los superpijos navegará por varios routers antes de llegar a la ACL que bloquee su paso, generando una cierta cantidad de tráfico que no va a ninguna parte por todas las redes. En cambio, la implementación es más sencilla porque se puede conseguir con una ACL estándar en un único router del escenario.

De una manera similar, la situación de los indigentes se podría abordar también desde varias perspectivas. Por una parte, se podrían implementar ACL extendidas en los routers de los pijos y los superpijos para bloquear el tráfico que llegue a esas redes desde la red de los indigentes. Sin embargo, parece una opción más sencilla implementar una ACL estándar en el router 4 que impida que el tráfico de esta red salga hacia el resto de redes del escenario. En este caso, para garantizar que el tráfico no llegue a los pijos ni a los superpijos habría que implementar una segunda ACL en el router 2 que impidiese que este tráfico llegase, a través de él, al router 5 y de ahí al 3, que da a la red de los pijos. Teniendo en cuenta que el tráfico debe poder llegar al router 2 para establecer la comunicación entre pobres e indigentes, el uso de esta segunda ACL es obligatorio para conseguir el objetivo planteado.

Finalmente, para que los pobres y los pijos puedan comunicarse entre ellos a través del router 5, esta ACL en el router 2 debe permitir pasar el tráfico que se origine en la red de los pobres. Para garantizar que este tráfico pasa exclusivamente por el router 5 y no por el 4, se deberá configurar una ACL en este router que bloquee la salida del tráfico que proviene de esta red por la interfaz que comunica el router 4 con el router 3. Esta ACL se deberá combinar, por tanto, con la que se debe implementar también en esa misma interfaz para evitar que los indigentes se puedan comunicar con el resto de distritos. 

Pero existen más opciones para configurar las ACL en este escenario. Para conseguir que los superpijos no se puedan comunicar con ninguna otra red la opción más evidente y práctica parece implementar una ACL en todas las interfaces del router 1 que dan al resto de redes del escenario. Para conseguir los otros dos objetivos, en cambio, puede ser suficiente con configurar un par de ACL en el router 3. Si se bloquea la entrada de tráfico de los indigentes por ambas interfaces de este router, éstos sólo se podrán comunicar ya con los pobres. Además, al bloquear el tráfico tanto de entrada desde los pobres como de salida desde los pijos por la interfaz que no conecta al router 5 se evita la comunicación entre ambas redes por cualquier vía que no sea el router 5.

De esta manera, los routers e interfaces en los que se deberían implementar las siguientes reglas ACL son estos:

- R1:
    - f1/0: ACL básica que bloquee el tráfico de entrada al router de cualquier red.
    - f0/1: ACL básica que bloquee el tráfico de entrada al router de cualquier red.
- R3:
    - f0/1: ACL básica que bloquee el tráfico de entrada tanto de las redes de los indigentes como de los pobres y el tráfico de salida de la red de los pijos.
    - f1/0: ACL básica que bloquee el tráfico de entrada desde la red de los indigentes.

Con esta configuración de ACL, los superpijos no recibirán tráfico de ninguna red, los indigentes podrán enviar mensajes a los pobres a través de la interfaz f0/1 del router 3 y los pobres a los indigentes a través de la interfaz f1/1 del router 2. Los pobres y los pijos se podrán comunicar únicamente a través del router 5 porque los mensajes de los pobres no pueden salir del router 4 al tres y los de los pijos no pueden salir del 3 al 4.

## Implementación de las ACL

### Router 1

```
R1#conf t
R1(config)#acces-list 1 deny any
R1(config)#interface fastEthernet 0/1
R1(config-if)#ip access-group 1 in
R1(config-if)#exit
R1(config)#interface fastEthernet 1/0
R1(config-if)#ip access-group 1 in
R1(config-if)#end
R1#write
```

### Router 3

```
R3#conf t
R3(config)#acces-list 1 deny 10.0.12.0 0.0.0.255
R3(config)#acces-list 1 deny 10.0.11.0 0.0.0.255
R3(config)#acces-list 1 permit any
R3(config)#acces-list 2 deny 10.0.2.0 0.0.0.255
R3(config)#acces-list 2 permit any
R3(config)#acces-list 3 deny 10.0.12.0 0.0.0.255
R3(config)#acces-list 3 permit any
R3(config)#interface fastEthernet 0/1
R3(config-if)#ip access-group 1 in
R3(config-if)#ip access-group 2 out
R3(config-if)#no shutdown
R3(config-if)#exit
R3(config)#interface fastEthernet 1/0
R3(config-if)#ip access-group 3 in
R3(config-if)#no shutdown
R3(config-if)#end
R3#write
```

# Configuración de un servidor DHCP

## Configuración del servidor

El primer paso para configurar el servidor DHCP de los superpijos es indicarle al router el rango de direcciones que tiene disponible para asignar a través de este protocolo. De todas las combinaciones posibles de direcciones dentro de su red, el servidor DHCP deberá reservar la 10.0.1.1 para el router. Además, el servidor debe asignar sólo direcciones que se encuentren en el rango de las 5 primeras, por tanto, las direcciones entre la 10.0.1.6 y la 10.0.1.255 también deberán estar excluidas.

Posteriormente, se puede crear un pool DHCP que indique al servidor que puede otorgar direcciones de la red 10.0.1.0/24 a los dispositivos que las soliciten. Además, en este momento de la configuración del servidor DHCP se debe indicar también la dirección IP de la puerta de enlace de la red. Además, se pueden configurar algunos aspectos más del servicio DHCP como el servidor DNS por defecto, el nombre de dominio de la red o el tiempo de préstamos de las direcciones aunque, en este caso, no es necesario.

```
R1#conf t
R1(config)#ip dchp excluded-address 10.0.1.1
R1(config)#ip dchp excluded-address 10.0.1.6 10.0.1.255
R1(dhcp-config)#network 10.0.1.0 255.255.255.0
R1(dhcp-config)#default-router 10.0.1.1
R1(dhcp-config)#lease 0 0 10
R1(dhcp-config)#end
R1#wr
```

# ACL extendidas

## Diseño de las ACL

En el nuevo escenario que se plantea es necesario implementar varias modificaciones. A excepción del router 1, que aún debe mantener las mismas reglas que impidan la conexión entre los dispositivos de su red y el resto de redes, todas las demás indicaciones han cambiado. Por tanto, si bien es cierto que una modificación de las ACL existentes podría ser compatible con el nuevo escenario parece más lógico eliminar las ACL creadas previamente en el resto de routers puesto que todas ellas impedirían que se cumpliesen los objetivos que se plantean y, sobre todo, porque para mantenerlas habría que sustituir la mayoría de ellas por ACL extendidas, lo cual implica, en la práctica, la eliminación de la ACL básica y la creación de una ACL extendida en su lugar.

En primer lugar, para permitir la comunicación entre Peeta y Clove se pueden implementarse dos reglas diferentes. Por una parte, se puede establecer una ACL en la interfaz f0/1 del router 3 que permita el tráfico desde Clove hasta Peeta y deniegue el resto. Por otra, se puede establecer una ACL en la interfaz f1/1 del router 4 que permita el tráfico desde Peeta hasta Clove y deniegue el resto.

A esta condición hay que sumar que Katniss y Cato deben poder comunicarse también. Por tanto, la ACL que permita la comunicación entre Peeta y Clove debe incluir una segunda condición previa a la denegación de todo el tráfico que permita el tráfico desde Cato a Katniss (si se aplica en el router 3) o desde Katniss a Cato (si se aplica en router 4).

De la misma manera, para bloquear la comunicación entre Katniss y Rue pero garantizar que Peeta se pueda seguir comunicando con los pobres también existen dos opciones: se puede implementar una ACL en la interfaz f0/1 del router 4 que deniegue los mensajes desde Katniss a Rue o bien se puede implementar una ACL en la interfaz f1/0 del router 2 que deniegue los mensajes desde Rue a Katniss.

Finalmente, para que Cato y Thresh no puedan comunicarse, y aunque también se podría tratar de implementar una ACL en el router 5, las opciones más óptimas parecen el uso de una ACL en la interfaz f1/0 del router 3 que deniegue el tráfico desde Cato hacia Thresh y permita el resto o el uso de una ACL en la interfaz f0/1 del router 2 que deniegue el tráfico de Thresh a Cato y permita el resto.

En todos estos casos será necesario el uso de ACL extendidas porque será necesario que las reglas incluyan, de manera explícita, información tanto del origen como del destino de los mensajes que deben filtrar.

Así, los routers e interfaces en los que es necesario implementar estas reglas ACL para conseguir los objetivos propuestos son los siguientes:

- R2:
    - f1/1: ACL extendida que deniegue el tráfico de salida desde Thresh hacia Cato y permita el resto.
    - f1/0: ACL extendida que deniegue el tráfico de salida desde Rue hacia Katniss y permita el resto.
- R3:
    - f0/1: ACL extendida que permita el tráfico de salida desde Clove hacia Peeta y el tráfico de salida desde Cato hacia Katniss y deniegue el resto.

## Implementación de las ACL

### Router 2

```
R2#conf t
R2(config)#access-list 101 deny ip 10.0.11.2 0.0.0.0 10.0.2.2 0.0.0.0
R2(config)#interfaz fastEthernet 1/1
R2(config-if)#ip access-group 101 out
R2(config-if)#exit
R2(config)#access-list 101 permit ip any any
R2(config)#access-list 102 deny ip 10.0.11.3 0.0.0.0 10.0.12.3 0.0.0.0
R2(config)#access-list 102 permit ip any any
R2(config)#interfaz fastEthernet 1/0
R2(config-if)#ip access-group 102 out
R2(config-if)#end
R2#wr
```

### Router 3

```
R3#conf t
R3(config)#access-list 101 permit ip 10.0.2.3 0.0.0.0 10.0.12.2 0.0.0.0
R3(config)#access-list 101 permit ip 10.0.2.2 0.0.0.0 10.0.12.3 0.0.0.0
R3(config)#interfaz fastEthernet 0/1
R3(config-if)#ip access-group 101 out
R3(config-if)#end
R3#wr
```

# Incorporar un servidor web al escenario

Para completar este caso práctico, se añade un servidor web instalado en una máquina Debian 11 al escenario.

Para que el resto de redes puedan comunicarse con el servidor web es necesario incluir su IP en la tabla de enrutamiento de los dispositivos que forman parte de estas redes. Teniendo en cuenta que la interfaz del router 5 que se conecta al servidor web tiene la IP 10.0.13.1, del rango de direcciones IP de la red 10.0.13.0/24, el servidor web también tiene que tener una dirección dentro de este rango, por ejemplo, la 10.0.13.2.

><i class="fas fa-exclamation-circle" aria-hidden="true"></i> El proceso para añadir y configurar un servidor web en GNS3 se desarrolla detalladamente en [este otro post](/redes/simular-servidor-web-gns3-nginx).
{: .notice--primary}

Para que tanto los ordenadores del distrito 11 como los del distrito 12 puedan acceder a este servidor web es necesario que los routers 2 y 4 incluyan en sus tablas de enrutamiento la información necesaria para alcanzar este destino. En el caso del router 2 sería necesario añadir la siguiente línea a la tabla de enrutamiento:

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Servidor web (10.0.13.0) | R5 f0/0 (192.168.11.1) | f0/1 |

```
R2(config)#ip route 10.0.13.0 255.255.255.0 192.168.11.1
```

En el router 4 la línea que se debe añadir a la tabla de enrutamiento es la siguiente:

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| Servidor web (10.0.13.0) | R5 f0/1 (192.168.12.1) | f1/0 |

```
R4(config)#ip route 10.0.13.0 255.255.255.0 192.168.12.1
```

Una vez configurado el enrutamiento, los equipos en la red 10.0.12.0/24 ya pueden establecer la comunicación con el servidor web, sin embargo, los equipos de la red 10.0.11.0/24 tienen la comunicación prohibida con este servidor debido al uso previo de ACL en el router.

![](/assets/img/redes/practica12/img9.png)

Por tanto, es necesario modificar también las ACL implementadas en estos routers para garantizar que se permita la comunicación entre los ordenadores de estos distritos y el servidor web.

```
R2(config)#access-list 101 permit tcp 10.0.11.0 0.0.0.255 10.0.13.0 0.0.0.255 eq 80
```

Con esta nueva regla ACL, la red 10.0.11.0/24 ya tendrá permitida la comunicación con el servidor web ubicado en la red 10.0.13.0/24 siempre que se use el protocolo HTTP, es decir, siempre que la comunicación se establezca a través del puerto 80.

Por último, queda implementar una última ACL que permita cumplir con la condición de que sólo los miembros de los distritos 11 y 12 puedan acceder al servidor web. Para ello, es necesario implementar una ACL en la interfaz f2/0 del router 5 que permita el tráfico de entrada al router que tenga como origen la red del servidor (10.0.13.0/24) y el puerto 80 y que tenga como destino las redes 10.0.12.0/24 o 10.0.11.0/24 y que deniegue todo el resto del tráfico.

```
R5#conf t
R5(config)#access-list 101 permit tcp 10.0.13.0 0.0.0.255 eq 80 10.0.11.0 0.0.0.255 eq 80
R5(config)#access-list 101 permit tcp 10.0.13.0 0.0.0.255 eq 80 10.0.12.0 0.0.0.255 eq 80
R5(config)#interface fastEthernet 2/0
R5(config-if)#ip access-group 101 in
R5(config-if)#end
R5#wr
```