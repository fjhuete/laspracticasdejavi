---
title:  "Escenario de enrutamiento en GNS3"
date:   2023-11-24 00:00:00 +0200
categories: redes
tag: [GNS3, enrutamiento, Wireshark, redes, Planificación y Administración de Redes]
---
En esta práctica se parte de un escenario recreado en el simulador GNS3 y, a partir de él, se enruta el tráfico entre los diferentes equipos que forman la red para permitir la comunicación entre todas las máquinas.

![](/assets/img/redes/practica5/img1.png)

# Montar el escenario en GNS3 completando las direcciones IP

Para configurar el escenario indicado en la práctica se deben ordenar y nombrar las diferentes redes. 
En primer lugar aparece una red con dos ordenadores conectados (H1 y H2). Se indica que la dirección IP de H2 es 192.168.10.2/24, por tanto, para que H1 esté en la misma red tiene que tener una dirección IP como por ejemplo  192.168.10.3/24.

Para configurar esta red en GNS3 se deben crear los dos ordenadores junto al switch al que deben ir conectados. Para mantener el orden, los switches adoptarán en su nombre el último número de la máscara de red de cada red. En este caso, el switch se llamará Switch10.

Una vez que se han creado todos los dispositivos, se deben configurar. En el caso de los ordenadores es necesario acceder al archivo de configuración y, en él, descomentar la línea indicada para asignar una dirección IP de forma manual al equipo.

Este procedimiento se debe repetir con todos los ordenadores que forman parte de cada una de las redes establecidas en la práctica.

En la siguiente red sólo hay un ordenador, con la IP  192.168.20.2/24. Este equipo se llama H3.

Los ordenadores H4 y H5 están conectados al Switch30. El H4 tiene la IP  192.168.30.2/24 y el H5 usa la IP 192.168.30.3/24.

Finalmente, hay una cuarta red formada por los ordenadores H6, con la IP 192.168.40.2/24 y H7, con la IP 192.168.40.3/24, conectados al Switch40.

Todos los equipos se han configurado para asignarle sus direcciones IP y máscaras de red correspondientes. A continuación hay que incluir los routers a esta red para poder establecer las conexiones que indica el enunciado.

El primer paso para incorporar un router al escenario de GNS3 es importar la plantilla al programa, eligiendo la opción “nueva plantilla” de la ventana de dispositivos del área de trabajo.

Esta importación se puede hacer de forma manual, desde el servidor o a través de un archivo con extensión .gns3a. En el caso de usar la opción de importación manual, GNS3 pide que se indique el archivo con extensión .gns3a que se quiere utilizar.

![](/assets/img/redes/practica5/img2.png)

El siguiente paso es elegir un tipo de servidor para instalar la plantilla.

Posteriormente se debe importar la imagen del SO del router que se pretende importar. En este caso es necesario marcar la opción “permitir ficheros personalizados” para poder instalar la imagen descargada de Internet.

![](/assets/img/redes/practica5/img3.png)

Tras confirmar la instalación de la versión de la imagen del router de Cisco que corresponde al fichero de imagen que se ha seleccionado durante el proceso de importación de la plantilla, el nuevo router ya está disponible en la lista de dispositivos del proyecto de GNS3.

Finalmente, para llegar al escenario planteado en el enunciado de la práctica sólo queda colocar los diferentes routers en cada punto y conectarlos a los dispositivos indicados.

![](/assets/img/redes/practica5/img4.png)

Una vez que están colocados y conectados todos los dispositivos, es necesario asignarle sus correspondientes direcciones IP a cada una de sus interfaces:

| R1 | f0/0 | 192.168.10.1/24 |
|    | f1/0 | 192.168.50.1/24 |
| R2 | f0/0 | 192.168.20.1/24 |
|    | f1/0 | 192.168.50.2/24 |
|    | f2/0 | 192.168.60.2/24 |
|    | f3/0 | 192.168.70.2/24 |
|    | f4/0 | 80.1.1.1/8      |
| R3 | f0/0 | 192.168.30.1/24 |
|    | f1/0 | 192.168.60.1/24 |
| R4 | f0/0 | 192.168.40.1/24 |
|    | f1/0 | 192.168.70.1/24 |
| R5 | f0/0 | 80.59.1.152/8 |
|    | f1/0 | 90.0.0.1/8 |
|ISP |      | 90.1.1.1/8 |

Para trasladar esta información al escenario de GNS3, se puede acceder a cada uno de los routers a través de la consola y, desde allí, asignarle la dirección IP y máscara de red a las tarjetas de red de estos dispositivos.

```
R4#conf t
R4(config)#interface f0/0
R4(config-if)#ip address 192.168.40.1 255.255.255.0
R4(config-if)#no shutdown
```

# Tablas de enrutamiento

## Tabla de enrutamiento de H1 y H2

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.10.0 | 0.0.0.0 | e0 |
| 0.0.0.0 | 192.168.10.1 | e0 |

## Tabla de enrutamiento de H3

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.20.0 | 0.0.0.0 | e0 |
| 0.0.0.0 | 192.168.20.1 | e0 |

## Tabla de enrutamiento de H4 y H5

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.30.0 | 0.0.0.0 | e0 |
| 0.0.0.0 | 192.168.30.1 | e0 |

## Tabla de enrutamiento de H6 y H7

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.40.0 | 0.0.0.0 | e0 |
| 0.0.0.0 | 192.168.40.1 | e0 |

## Tabla de enrutamiento de R1

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.10.0 | 0.0.0.0 | f0/0 |
| 0.0.0.0 | 192.168.50.2 | f1/0 |

## Tabla de enrutamiento de R2

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.10.0 | 192.168.50.1 | f1/0 |
| 192.168.20.0 | 0.0.0.0 | f0/0 |
| 192.168.30.0 | 192.168.60.1 | f2/0 |
| 192.168.40.0 | 192.168.70.1 | f3/0 |
| 0.0.0.0 | 80.59.1.152 | e0 |

## Tabla de enrutamiento de R3

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.30.0 | 0.0.0.0 | f0/0 |
| 0.0.0.0 | 192.168.60.2 | f1/0 |

## Tabla de enrutamiento de R4

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.40.0 | 0.0.0.0 | f0/0 |
| 0.0.0.0 | 192.168.70.2 | f1/0 |

## Tabla de enrutamiento de R5

| Destino | Siguiente router | Interfaz de salida |
|:---:|:---:|:---:|
| 192.168.10.0 | 80.1.1.1 | f0/0 |
| 192.168.20.0 | 80.1.1.1 | f0/0 |
| 192.168.30.0 | 80.1.1.1 | f0/0 |
| 192.168.40.0 | 80.1.1.1 | f0/0 |
| 80.0.0.0 | 0.0.0.0 | f0/0 |
| 0.0.0.0 | 90.1.1.1 | f1/0 |

## Configuración de las tablas de enrutamiento en los routers Cisco en GNS3

Una vez que las direcciones IP de todas las interfaces de red están asignadas a los routers de la escena de GNS3, la mayor parte de entradas de las tablas de enrutamiento se completan por defecto. Sin embargo, conviene revisar y, si fuese necesario, complementar la información almacenada en estos dispositivos.

# Análisis de las cachés ARP

Para limpiar la memoria caché ARP de los VPCS H6 y H7 se ejecuta el comando `clear arp`.

El comando para limpiar la memoria caché ARP de los routers es `clear arp`, sin embargo, en este caso, la tabla ARP no se llega a vaciar en ningún momento. Esto se debe a que los routers Cisco que se están usando en esta práctica elaboran esta tabla de forma automática sin necesidad de que haya tráfico en la red. Es decir, en el momento en el que se ejecuta el comando `clear arp` y se eliminan las entradas de la memoria caché ARP del router, este vuelve a lanzar peticiones ARP a todos los dispositivos conectados a él para volver a elaborar esta tabla.

Tras limpiar todas las cachés ARP de los equipos indicados se ejecuta el ping entre H6 y R5. Tres construir el mensaje en el nivel de aplicación y asignarle los puertos en el de transporte, en el nivel de red se añaden las direcciones IP de origen y destino a la trama. En este caso, no interviene el protocolo DNS porque se está indicando la IP de destino directamente al ejecutar el comando ping. La IP de origen se asigna también de forma automática por parte del ordenador, que ya tiene su dirección IP previamente configurada.

En el nivel de enlace, en cambio, sí que interviene el protocolo ARP en el proceso de asignación de las direcciones MAC de destino y origen a las cabeceras de la trama. En este primer paso de la transmisión del mensaje al ejecutar el ping que se produce en el ordenador H6, el equipo asigna su propia dirección MAC a la cabecera del nivel de enlace de la trama como dirección MAC de origen y envía una petición ARP request a la red en busca de la dirección MAC que corresponde a la tarjeta de red con la IP 192.168.40.1, es decir, la tarjeta de red del router de su red que funciona como puerta de enlace para este equipo.

El resto de equipos de la red recibe esta petición y el router contesta con una respuesta ARP reply, que llega al ordenador H6 con la dirección MAC de la tarjeta de red del router. Este ordenador la incluye en la cabecera de la trama y, además, la guarda en su memoria caché ARP.

![](/assets/img/redes/practica5/img5.png)

Sin embargo, aunque la petición ARP request que envió el ordenador H6 ha llegado a todos los dispositivos de la red, la respuesta ARP reply sólo se ha enviado al ordenador que solicitó esta información, de manera que el otro ordenador conectado a la misma red, H7, no ha recibido el mensaje ARP reply del router y, por tanto, no conoce su dirección MAC. Esto se puede comprobar al consultar la tabla de la memoria caché ARP de este equipo, que sigue vacía.

El router conectado a esta red, R4, es el siguiente punto en el camino del mensaje ping entre H6 y R5. Este router tiene ya guardad en su memoria caché ARP la dirección MAC del ordenador H6, que lanzó una petición ARP en el primer paso de la transmisión del mensaje.

En el nivel de enlace, este router vuelve a realizar una petición ARP para conocer la MAC de destino. Para ello, en primer lugar consulta su tabla de enrutamiento para comprobar la IP del siguiente router al que tiene que dirigir el mensaje, en este caso, la 192.168.70.2 y, cuando la conoce, lanza una petición ARP a todos los dispositivos conectados para solicitar la dirección MAC que corresponde a esa IP.

El dispositivo cuya tarjeta de red tiene configurada esa dirección, responde la petición de R4 con una respuesta ARP que incluye su dirección MAC. Así, el router R4 guarda esa información en la cabecera de la trama y, como se puede comprobar en la siguiente captura, la almacena también en su tabla de memoria caché ARP.

![](/assets/img/redes/practica5/img6.png)

En el siguiente router, R2, se repite el proceso. En este caso no se aplica ninguna modificación a la trama en el nivel de red pero sí en el nivel de enlace. En primer lugar, el router comprueba la dirección IP de destino incluida en la trama y la busca en su tabla de enrutamiento para conocer el siguiente paso en el camino, en este caso, la dirección de destino 80.59.1.152 ya está indicada en la tabla de enrutamiento. Entonces, R2 envía una petición ARP al resto de equipos de la red para solicitar la dirección MAC asociada a la IP de destino y R5 devuelve una respuesta ARP con su dirección MAC. El router R2 modifica la información de la cabecera del nivel de enlace de la trama para modificar la MAC de origen por su propia MAC y la de destino por la de R5 y guarda la dirección MAC de R5 en su memoria caché ARP.

![](/assets/img/redes/practica5/img7.png)

Finalmente, el mensaje ping consigue llegar a su destino en el router R5.

Una vez que R5 recibe el mensaje ping que envió el ordenador H6, tiene que devolver una respuesta. Para construir la trama, en primer lugar elabora el mensaje ping de vuelta en el nivel de aplicación, con los puertos correspondientes en el nivel de transporte y, a continuación, añade la cabecera del nivel de red incluyendo como IP de origen su propia dirección IP y como IP de destino la IP que aparece como IP de origen en la trama que ha recibido.

Para completar la cabecera del nivel de enlace, R5 tiene que consultar su tabla de enrutamiento y descubrir a qué IP debe dirigir el mensaje para que llegue a su destino, en este caso la 80.1.1.1. Cuando la conoce, lanza una petición ARP a su red solicitando la MAC asociada a esa IP que R2 contesta con una respuesta ARP con la información sobre su dirección MAC. Entonces, R5 construye la cabecera del nivel de enlace con su MAC como MAC de origen y la de R2 como MAC de destino. También almacena la dirección MAC de R2 en su memoria caché ARP.

![](/assets/img/redes/practica5/img8.png)

El recorrido de vuelta se hace más sencillo. Cuando el ping de vuelta vuelve a llegar a R2, este router puede modificar la cabecera del nivel de enlace sin necesidad de realizar ninguna petición ARP porque conserva la dirección MAC del destino del mensaje, la de R4, en su memoria caché ARP. Con esta información puede modificar la cabecera de la trama y transmitir el mensaje.

Cuando R4 recibe el mensaje, como también conserva la dirección MAC del ordenador H6 simplemente modifica esa información en la cabecera del nivel de enlace de la trama y transmite el mensaje al ordenador.

Con este último paso, el ping de vuelta llega hasta el ordenador H6. Y todo esto ocurre en sólo unos 47 milisegundos.

# Captura del tráfico con Wireshark

## Captura del tráfico en el punto A

### Petición ARP

El primer campo relevante en la captura del tráfico en el punto indicado como A en la práctica muestra la petición ARP que hace el router R1 a los dispositivos conectados a la misma red. Tal y como la describe Wireshark, el router pregunta qué dispositivo tiene la dirección IP 80.59.1.152 y pide que se transmita esa información a su IP 192.168.50.1.

En la información de este campo se puede observar que la dirección de destino de la petición ARP es la dirección MAC broadcast (ff:ff:ff:ff:ff:ff) y el origen del mensaje es la dirección MAC ca:01:17:f4:00:1c, que corresponde a la IP 192.168.50.1, asociada a la tarjeta de red 1/0 del router R1.

![](/assets/img/redes/practica5/img9.png)

A continuación, también indica que se trata de una petición ARP (Address Resolution Protocol), a través de Ethernet e IPv4, que es de tipo petición y recoge también la dirección MAC e IP del remitente así como la IP del destinatario. El campo de la dirección MAC de destino está a 0 porque el objetivo de esta petición es, precisamente, obtener esa información.

![](/assets/img/redes/practica5/img10.png)

### Respuesta ARP

El siguiente campo en la captura muestra la respuesta a esta petición. En este caso, el software resume la respuesta ARP que devuelve el router R2 como “80.59.1.152 is at ca:02:0e:20:00:1c”. Es decir, identifica este mensaje como una respuesta ARP que incluye la información de la dirección MAC que el router R1 había solicitado en el mensaje anterior.

En este caso, como destino se muestra la dirección MAC del router R1, ca:01:17:f4:00:1c, y como fuente del mensaje, se identifica la dirección MAC del router R2,  ca:02:0e:20:00:1c. También se indica que el tipo del mensaje es ARP.

![](/assets/img/redes/practica5/img11.png)

Concretamente, se identifica el mensaje como una respuesta ARP y, por tanto, se le asigna el código de operación 2. Como este mensaje ya sí incluye la información sobre la dirección MAC del router R2, este dato se muestra como contenido del mensaje en la dirección MAC del remitente y se indica como direcciones MAC e IP del destinatario las de la tarjeta de red 1/0 del router R1.

![](/assets/img/redes/practica5/img12.png)

### Petición echo (ping)

Una vez conocida la dirección MAC a la que el router R1 tiene que transmitir los mensajes, comienza la transmisión del ping propiamente dicho. En los siguientes campos se alternan, de forma consecutiva, peticiones y respuestas de ping.

En el resumen, se observa este siguiente campo que contiene un mensaje de tipo ping con origen en 192.168.10.3 y destino en 80.59.1.152. Pero el análisis detallado de las diferentes cabeceras de la trama puede aportar más información.

![](/assets/img/redes/practica5/img13.png)

En primer lugar, en el nivel de aplicación, se construye un mensaje de 56 bytes, que es el que se enviará al dispositivo que se ha indicado como destino del ping.

![](/assets/img/redes/practica5/img14.png)

En el nivel de transporte se identifica el protocolo como Internet Control Message Protocol (ICMP) y el mensaje aparece identificado como un mensaje de tipo 8, es decir, una petición echo (ping). En esta cabecera de la trama se identifican también el origen y destino del mensaje con un código numérico.

![](/assets/img/redes/practica5/img15.png)

A continuación, en el nivel de red se añade a la trama la información de las IP de origen y destino. En este caso, se indica que se usan direcciones del protocolo IPv4 y se especifican cuál corresponde al origen de la trama, 192.168.10.3, y cuál al destino, 80.59.1.152.

![](/assets/img/redes/practica5/img16.png)

Por último, en la cabecera del nivel de enlace se añade a la trama la información sobre la dirección MAC de origen y destino del mensaje.

![](/assets/img/redes/practica5/img17.png)

### Respuesta echo (ping)

El siguiente campo registra la respuesta echo (ping) a la petición anterior, tal y como se indica en el resumen. Se trata de un mensaje ICMP con origen en la IP 80.59.1.152 y destino en la 192.168.10.3.

![](/assets/img/redes/practica5/img18.png)

En el nivel de aplicación, se construye en mensaje con los mismos 56 bytes de información que se habían enviado previamente en la petición por parte del ordenador H1.

![](/assets/img/redes/practica5/img19.png)

En el nivel de enlace se añade a la cabecera de la trama información relacionada con el tipo de mensaje, en este caso un mensaje de tipo 0, una respuesta de echo (ping) que usa el protocolo Internet Control Message Protocol (ICMP). Además, se identifica el origen y destino del mensaje con los códigos numéricos 2749 y 48394.

En este caso, se incluye también información adicional como el tiempo de respuesta (30,377ms).

![](/assets/img/redes/practica5/img20.png)

En la cabecera del nivel de red se invierte ahora el orden de las direcciones IP que se muestran. En este caso, la dirección IP de origen es la 80.59.1.152 y la de destino es la 192.168.10.3.

![](/assets/img/redes/practica5/img21.png)

Por último, en el nivel de enlace, también se altera el orden de las direcciones MAC, de manera que en la respuesta, la MAC de origen es la dirección que en la petición correspondía a la MAC de destino y la de destino es la dirección MAC que en la petición correspondía al origen.

![](/assets/img/redes/practica5/img22.png)

## Captrua del tráfico en el punto B

La captura en el punto B indicado en la práctica presenta algunas diferencias respecto al tráfico capturado en el punto A. Para encontrar algunas de ellas es necesario analizar detenidamente el contenido de las cabeceras de las tramas transmitidas, sin embargo, hay una diferencia que es evidente al ver una imagen general de la captura de tráfico en wireshark.

En primer lugar, llama la atención que, en este caso, no se ha podido detectar ningún mensaje ARP ni de petición ni de respuesta. Esto se explica porque esta captura de tráfico se ha llevado a cabo a continuación de la anterior y, por tanto, los dispositivos implicados ya contaban con las direcciones MAC del resto de dispositivos de su red implicados en la ruta en su memoria caché ARP, de manera que no es necesario que vuelvan a pedir esta información.

### Petición echo (ping)

Para encontrar el resto de novedades en esta captura de tráfico conviene analizar con más detenimiento un par de los campos que muestra wireshark.

![](/assets/img/redes/practica5/img23.png)

En el primero de ellos no se aprecia diferencia alguna en el nivel de aplicación. Nuevamente, el mensaje está formado por 56 bytes de información que se transmiten desde el ordenador H1 al router R5.

![](/assets/img/redes/practica5/img24.png)

La cabecera del nivel de transporte ofrece también una información muy similar a la que incluían las tramas capturadas en el punto A: el tipo de mensaje que se está transmitiendo (tipo 8, petición echo (ping)), junto a los códigos numéricos que identifican el destino y el origen del mensaje, 29887 y 49012.

![](/assets/img/redes/practica5/img25.png)

Tampoco hay diferencias en la información que añade a la trama la cabecera en el nivel de red: las direcciones IP de origen y destino coinciden con las que se incluyen en las tramas capturadas en el punto A.

![](/assets/img/redes/practica5/img26.png)

En cambio, en el nivel de enlace, el mensaje sí presenta diferencias respecto a la información que contenían las cabeceras de este nivel en el punto A. En este caso, el mensaje se ha capturado entre el router R2 y el router R5, por tanto, las direcciones MAC que se incluyen en esta cabecera son la de la tarjeta de red del router R2 como MAC de origen y la de la tarjeta de red del router R5 como MAC de destino.

Esto es así porque mientras que el mensaje debe conservar durante todo el proceso de transmisión la IP de destino para saber cuál es el último punto al que se debe dirigir y la IP de origen para que el último equipo pueda saber a dónde debe dirigir la respuesta, la dirección MAC necesita cambiar en cada paso de la ruta, de manera que la información se pueda transmitir al siguiente router del camino.

![](/assets/img/redes/practica5/img27.png)

### Respuesta echo (ping)

En el caso de la respuesta capturada en el punto B, las similitudes y diferencias con la respuesta capturada en el punto A van a ser las mismas que las analizadas en el mensaje  de petición.

![](/assets/img/redes/practica5/img28.png)

Así, se puede observar que en el nivel de aplicación nuevamente se ha construido un mensaje de 56 bytes de información.

![](/assets/img/redes/practica5/img29.png)

En el nivel de transporte se ha identificado el mensaje como una respuesta echo (ping) que usa el protocolo ICMP y se han asignado también los códigos numéricos correspondientes al origen y destino del mensaje. Además, como ocurre en el punto A, se incluye información sobre el tiempo de respuesta (7,046ms). En este caso, como el mensaje se ha capturado mucho más cerca del dispositivo que ha generado la respuesta que en el análisis del punto A, el tiempo de respuesta es considerablemente más reducido.

![](/assets/img/redes/practica5/img30.png)

Y en el nivel de red, la cabecera alternado la información de las direcciones IP de origen y destino especificando ahora que el origen del mensaje es el router R5 (80.59.1.152) y el destino el ordenador H1 (192.168.10.3).

![](/assets/img/redes/practica5/img31.png)

Finalmente, la cabecera del nivel de enlace recoge en esta caso como MAC de origen la dirección del router R5 y como MAC de destino la del router R2, de manera que las direcciones MAC son las inversas que las que se mostraban en la cabecera del nivel de enlace de la trama correspondiente a la petición de echo (ping) en este mismo punto.

![](/assets/img/redes/practica5/img32.png)