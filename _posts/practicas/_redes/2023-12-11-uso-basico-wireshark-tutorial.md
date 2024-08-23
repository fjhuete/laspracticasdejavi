---
title:  "Uso de Wireshark"
date:   2023-12-11 01:00:00 +0200
categories: redes
tag: [Wireshark, redes, Planificación y Administración de Redes]
---

Wireshark es una herramienta que captura del tráfico que circula por una red y permite analizar el contenido de los paquetes que se envían y reciben desde las máquinas conectadas. Este software permite capturar los paquetes que viajan por una red, filtrarlos, y ver su contenido para poder analizar el tipo de tráfico del que se trata.

# Tráfico básico en una comunicación

![](/assets/img/redes/practica6/img1.png)

En esta captura aparecen paquetes que usan protocolos como MDNS. Entre el resto de paquetes aparecen varios paquetes que usan el protocolo UDP con una IP de origen 192.168.0.177 y destino de broadcast que mi ordenador está recibiendo periódicamente. Más allá de eso encontramos, en primer lugar una petición y una respuesta ARP que mi ordenador usa para conocer la dirección MAC del router de casa, varias peticiones y respuestas DNS que el ordenador necesita para averiguar la dirección IP a la que debe hacer ping (la de www.google.com) y, finalmente, una sucesión de peticiones y respuestas de mensajes Echo (ping) que usan el protocolo ICMP. Entre tanto, aparece también alguna petición y respuesta ARP que llega desde la puerta de enlace en busca de la IP de mi ordenador.

# Tráfico generado por `ping`

Se realiza una captura de 10 segundos haciendo ping a www.google.com en condiciones similares a las planteadas para el ejercicio anterior con la opción del modo promiscuo activada.

![](/assets/img/redes/practica6/img2.png)

Durante la captura se han registrado los siguientes paquetes:

| ARP | 2 |
| IP | 35 |
| ICMP | 22 |
| TCP | 0 |
| UDP | 13 |
| DNS | 10 |

![](/assets/img/redes/practica6/img3.png)

Los datos del primer paquete IP recibido son:

| Dir. IP origen | 192.168.0.1 |
| Dir. IP destino | 192.168.0.32 |
| Protocolo | DNS |
| Tamaño | 101 bytes |
| TTL | 64 |
| Identificador | 0x9d29 (40233) |

![](/assets/img/redes/practica6/img4.png)

# Los campos en el resumen de los paquetes capturados en Wireshark

En la imagen se muestra una captura de los paquetes ICMP generados tras la ejecución de la orden ping www.elpais.com. En total se capturan 8 mensajes: 4 de tipo echo (ping) request y otros cuatro de tipo echo (ping) reply.

Si analizamos estos mensajes, podemos observar que son mensajes de tipo 8: echo (ping) request o de tipo 0: echo (ping) reply, que todos ellos usan el código 0 y que el tamaño de cada uno de los paquetes es de 98 bytes de datos.

Además, el análisis de las cabeceras IP de estos mensajes indica que cuentan con cabeceras IP de una longitud de 20 bytes. La longitud total es de 84 bytes y estos mensajes cuentan con 48 bytes de datos.

![](/assets/img/redes/practica6/img5.png)

# Ejemplo de uso de los filtros de captura de Wireshark

Con el filtro de captura `udp and src port 53 or dst port 53` se pueden filtrar únicamente aquellos paquetes que se envían o reciben para realizar peticiones o recibir respuestas desde el servidor DNS.

![](/assets/img/redes/practica6/img6.png)

El datagrama UDP de estos mensajes tiene una longitud variable en cada caso. En la captura aparecen paquetes tanto de 82 bytes como de 174, 158, 96, 97 ó 162 bytes. Otro elemento que cambia en cada una de las peticiones DNS que se han registrado en esta captura es el puerto del ordenador. Mientras que el puerto del servidor siempre es constante (puerto 53), el puerto del ordenador no es el mismo en cada petición y el sistema operativo asigna un puerto diferente a cada una de ellas, de manera que el puerto de origen o destino cambia en cada pareja de peticiones y respuestas al servidor DNS.

![](/assets/img/redes/practica6/img7.png)

Si se desactiva el filtro de captura, se puede comprobar que, al hacer un ping a la dirección IP de la máquina en la que está alojada la web www.nba.com, la 92.123.57.194, wireshark no consigue capturar ningún paquete que se corresponda con una petición o una respuesta DNS. Esto se debe a que, al no usar una URL a la hora de hacer el ping, no es necesario traducir ningún nombre de dominio a la IP y, por tanto, no es necesario usar ese servicio.

![](/assets/img/redes/practica6/img8.png)

# Análisis del tráfico ARP en Wireshark

Durante la ejecución del comando ping a www.barrapunto.com se han capturado con el filtro `arp` cuatro mensajes ARP, dos de ellos de petición y otros dos de respuesta.

El primero de ellos es una petición ARP que lanza el ordenador a la dirección de difusión para buscar la dirección MAC de la puerta de enlace, a la que debe enviar los paquetes de petición Echo. A continuación, se captura de la respuesta a esta petición ARP, en la que el router contesta con su dirección MAC.

Por último, aparecen otros dos mensajes de petición y respuesta ARP en los que el router solicita la MAC del ordenador y el ordenador contesta con esa información al router.

![](/assets/img/redes/practica6/img9.png)

Tras esta captura de tráfico, y sin volver a borrar la memoria caché ARP se realiza otra captura con el mismo filtro aplicado. En este caso, Wireshark no puede encontrar ningún paquete durante la captura que cumpla con los requisitos indicados en el filtro.

Esto es así porque en esta ocasión el ordenador conserva en su memoria caché ARP la dirección MAC de la puerta de enlace a la que debe enviar las peticiones Echo, de la misma manera que el router conservar en su memoria caché ARP la dirección MAC de la tarjeta de red del ordenador al que tiene que enviar las respuestas según van llegando.

# Análisis del tráfico generado por `traceroute`

El primer tipo de paquete capturado en este ejercicios es una serie de paquetes DNS que corresponden a la petición DNS desde el ordenador para conocer la dirección IP de www.rae.es. Se trata de dos peticiones y dos respuestas que permiten al ordenador conocer a qué dirección IP debe enviar los mensajes necesarios para ejecutar el comando traceroute, en este caso, la 104.26.1.151.

![](/assets/img/redes/practica6/img10.png)

En segundo lugar se captura un bloque de 16 mensajes UDP con origen en el ordenador (192.168.0.32) y destino en www.rae.es (104.26.1.151). Pero estos mensajes no se envían al puerto privilegiado asignado al servicio web, sino que se envían a puertos que van del 33434 al 33449, aumentando progresivamente en cada mensaje.

Todos ellos son paquetes de 74 bytes que contienen un mensaje de 32 bytes de datos.

![](/assets/img/redes/practica6/img11.png)

Estos 16 paquetes se agrupan en bloques de 3, cada uno de ellos con un TTL diferente. De esta forma, los tres primeros paquetes capturados tiene un time-to-live de 1 y aumenta progresivamente hasta los paquetes capturados en el orden 13, 14 y 15 que presentan un TTL de 5 cada uno de ellos. La excepción está en el último paquete de este bloque, cuyo TTL es 6 pero este dato sólo se encuentra en un paquete y no en tres como en los casos anteriores. Los otros dos paquetes de este bloque se encuentran a continuación, tras el primer grupo de mensajes ICMP ttl exceeded que se recibe.

![](/assets/img/redes/practica6/img12.png)

A continuación, se registran en la captura una serie de mensajes del protocolo ICMP ordenados en grupos de tres. Cada grupo tiene asignada una IP de origen diferente, de manera que los tres primeros provienen de la IP 192.168.0.1, es decir, la puerta de enlace de la red, los tres siguientes de la 10.195.192.1, otra IP privada correspondiente a otra red, los tres siguientes provienen de la 193.149.1.56, que es una IP pública de Internet, otros tres llegan desde la IP 172.70.58.2 y, por último, hay tres paquetes que tienen como IP de origen la IP pública de la web www.rae.es, la 104.26.1.151.

Estas direcciones IP corresponden con las que devuelve el comando traceroute durante su ejecución.

![](/assets/img/redes/practica6/img13.png)

Todos estos mensajes son de tipo 11 (time-to-live exceeded) y su código es el 0 (time to live exceeded in transit). Para todos ellos el TTL es 1.

![](/assets/img/redes/practica6/img14.png)

Los siguientes paquetes que se han registrado en esta captura forman un nuevo bloque de mensajes UDP con origen en el ordenador (192.168.0.32) y destino en www.rae.es (104.26.1.151). Realmente, lo que ocurre aquí es que, a la vez que se enviaba el bloque de mensajes UDP se han empezado a recibir los mensajes ICMP anteriores, lo que ha hecho que aparezcan intercalados en la captura. Sin embargo, este bloque de mensajes UDP no es diferente, sino la continuación del bloque anterior. De hecho, los primeros dos mensajes de este bloque completan, junto con el último mensaje inmediatamente anterior a los paquetes ICMP, el conjunto de 3 mensajes con el mismo TTL del bloque anterior.

De manera sucesiva se siguen enviando mensajes de este tipo a puertos consecutivos de la IP de destino, en este caso, desde el 33450 (el siguiente al usado en el último mensaje de este tipo) hasta el 33459. También sucesivamente, se va aumentando el TTL hasta llegar a 9 en los últimos dos mensajes del bloque. En este caso, tampoco se registra en la captura un grupo de 3 mensajes con el mismo TTL, sino que son solo dos los que tienen un TTL con valor 9.

![](/assets/img/redes/practica6/img15.png)

Por último, se registra un último bloque de mensajes ICMP. En él se incluyen dos mensajes que realmente pertenecen al bloque anterior y, aunque no aparezcan los primeros en este bloque, si se revisa el campo “stream index” de cada uno de ellos, se puede comprobar que, en realidad, se transmitieron antes del primer mensaje capturado en este bloque (resaltado en azul en la captura de pantalla).

![](/assets/img/redes/practica6/img16.png)

Por tanto, la última parte relevante de esta captura de tráfico es este bloque de 8 paquetes ICMP de tipo 3 (destination unreachable) y código 3 (port unreachable). Se trata de mensajes que se envian desde la IP pública de www.rae.es destinados al ordenador. Todos ellos hacen referencia a un número de puerto determinado dentro del rango que se ha usado para enviar los mensajes UDP comentados anteriormente. En concreto, los puertos que han devuelto mensajes de este tipo son los puertos del 33452 al 33459.

![](/assets/img/redes/practica6/img17.png)

# Análisis del triple apretón de manos (3-way handshake)

Durante la captura de tráfico planteada en este ejercicio (solicitar la página web www.nba.com desde un navegador), se han registrado un total de 9824 paquetes TCP o UDP. De ellos, 8583 son paquetes UDP y los 1241 restantes son paquetes TCP.

Con la herramienta para seguir el flujo TCP se puede comprobar que, en este caso, se ha realizado una petición HTTP al servidor web que ha tenido su correspondiente respuesta.

![](/assets/img/redes/practica6/img18.png)

Gracias a esta opción, se pueden seguir en la captura todos los paquetes transmitidos entre cliente y servidor en este proceso.

![](/assets/img/redes/practica6/img19.png)

Tras aplicar el filtro que permite esta herramienta aparecen tres paquetes TCP marcados como SYN, SYN-ACK y ACK respectivamente, que hacen referencia al mecanismo de TCP 3-Way Handshake (triple apretón de manos) para crear la sesión entre cliente y servidor. 

![](/assets/img/redes/practica6/img20.png)

A partir de aquí, cliente y servidor empiezan a intercambiar información HTTP. ya pueden empezar a enviar toda la información HTTP. El primer mensaje que envía el cliente al servidor tras el establecimiento de la conexión es una petición tipo GET con destino al puerto 80 de la IP pública del servidor web.

En la cabecera del nivel de aplicación de este mensaje se puede comprobar que el cliente solicita la web www.nba.com e indica que la versión HTTP empleada es la 1.1. Esta cabecera recoge también información sobre el host o servidor en el que está alojada esta web.

![](/assets/img/redes/practica6/img21.png)

El siguiente mensaje en el flujo TCP analizado es la respuesta HTTP que el servidor devuelve al cliente. Esta respuesta incluye un código de estado 301, que indica que el recurso solicitado ha sido movido permanentemente y redirige a dicho recurso. Al seleccionar el paquete en wireshark, se puede acceder a todas las cabeceras del mensaje que contienen información relevante en este sentido.

Finalmente, se sucede un bloque de peticiones y respuestas marcadas como ACK, FIN – ACK y RST que indican la finalización de la conexión entre el cliente y el servidor.

![](/assets/img/redes/practica6/img22.png)

# Identificar ficheros descargados vía HTTP con Wireshark

En primer lugar, con un filtro de captura que permita registrar sólo los paquetes relacionados con HTTPS, por ejemplo, aquellos que usen un puerto 80, se captura el tráfico de una petición web en la que se descargue algún fichero.

Entonces se debe localizar la línea de la petición GET en la captura de tráfico para acceder, desde ahí al seguimiento del flujo HTTP.

![](/assets/img/redes/practica6/img23.png)

En la ventana emergente de seguimiento del flujo HTTP se pueden ver las peticiones del cliente al servidor en rojo y las respuestas del servidor en azul.

Cada respuesta viene precedida de un encabezado con información sobre el tipo de petición, tipo de conexión, tamaño del contenido, tipo de servidor, última modificación de la web, fecha de la petición y otros datos de interés. A continuación, se puede ver el contenido del fichero descargado vía HTTP.

![](/assets/img/redes/practica6/img24.png)

De forma sucesiva, aparecen en este registro todas las peticiones con sus correspondientes respuestas así como todos los ficheros descargados con cada una de las respuestas.

# La opción Expert Information de Wireshark

La opción Expert Information de Wireshark es un diálogo que permite registrar información de las posibles anomalías y elementos de interés que forman parte de un fichero de captura. Su objetivo es aportar una mejor idea del comportamiento inesperado en una red, de manera que se puedan localizar e identificar los posibles problemas de manera más rápida y ágil que con una búsqueda manual a través de la lista de paquetes.

El cuadro de diálogo Expert Information agrupa los sucesos que recoge por nivel de gravedad y los muestra con un código de color. Así, usa el azul para aquellos eventos propios del transcurso normal de una red, el cian para eventos relevantes como códigos de errores comunes, el amarillo para advertencias de sucesos como que una aplicación devuelva un código de error poco común y el rojo para errores como la recepción de paquetes dañados.

Junto al nivel de gravedad del suceso recogido, este cuadro de diálogo también muestra otra información como un breve resumen de cada entrada, así como el tipo de incidencia por la que se ha incluido en el diálogo cada elemento y el protocolo que utiliza.
Desde las preferencias de Wireshark, se puede añadir a la lista de paquetes capturados que el programa muestra por defecto una nueva columna en la que se añade el nivel de gravedad de aquellos paquetes que constituyan una incidencia recogida en “expert information”.

Como ejemplo, en el cuadro de diálogo “Expert information” de la captura de tráfico anterior se recogen las siguientes incidencias:

![](/assets/img/redes/practica6/img25.png)

Como se puede ver en la imagen, aparecen agrupadas por gravedad, de mayor a menor, junto a su descripción, tipo y protocolo. En este caso, todas las incidencias forman parte del grupo “sequence”, esto es, se trata problemas relacionados con el número de secuencia de los paquetes, es decir, se representan en esta lista paquetes que se han recibido desordenados o que han necesitado ser retransmitidos.

En este otro ejemplo, aparecen elementos que forman parte de otros grupos, por ejemplo, el grupo “protocol”, que indica que se ha producido una violación de las especificaciones del protocolo, o el grupo “undecoded”, que indica que ese paquete contiene información que no ha podido ser decodificada correctamente.

![](/assets/img/redes/practica6/img26.png)

Por último, en este otro caso, se observa como algunas incidencias incluyen una posible explicación en su campo de resumen. Por ejemplo, en la segunda fila aparece un elemento del protocolo TCP con la marca de gravedad advertencia y del grupo secuencia que indica en su descripción que se ha incluido como tal porque el segmento anterior no se capturó. Sin embargo, en este mismo campo se añade una aclaración: se trata de un error común al inicio de las capturas.

Por otra parte, cada una de estas filas de incidencias permiten desplegar un contenido que muestra todos aquellos paquetes a los que hacen referencia, incluyendo su número en la secuencia de registro de la captura junto a la información del paquete que se aporta en la ventana principal de capturas de Wireshark, así como el grupo al que pertenece la incidencia y el protocolo que usa el paquete. Esto aporta más información relevante como, por ejemplo, si se trata de errores en paquetes consecutivos o aislados, si las incidencias pueden estar o no relacionadas y más información sobre los paquetes concretos implicados en cada una de estas entradas.

![](/assets/img/redes/practica6/img26.png)