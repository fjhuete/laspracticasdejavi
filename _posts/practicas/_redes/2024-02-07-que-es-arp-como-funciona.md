---
title:  "Qué es ARP y cómo funciona"
date:   2024-02-07 01:00:00 +0200
categories: redes
tag: [redes, ARP, caché ARP, MAC, MAC flooding, MAC spoofing, Planificación y Administración de Redes]
---

El protocolo de resolución de direcciones (ARP) es el protocolo de comunicaciones responsable de encontrar la dirección MAC que corresponde a una determinada dirección IP. Lo hace a través de un conjunto de tramas de petición y respuesta.

# Las fases del protocolo ARP

## Petición ARP

La petición ARP debe ser un mensaje de difusión porque debe llegar a todas las máquinas de la red, de manera que pueda recibirlo aquella cuya dirección MAC se pretende conocer.

En una captura de Wireshark una petición ARP se muestra con el siguiente resumen:

![Petición ARP](/assets/img/redes/practica10/img1.png)
*Petición ARP (en azul)*

Se trata de un mensaje que, en este caso, tiene origen en el dispositivo Private_66:68:00 y que tiene como destino la dirección de broadcast de la red. Su longitud es de 64 bytes y, como información, Wireshark muestra el mensaje “¿Quién tiene la IP 192.168.122.1? Díselo a 192.168.122.25”.

## Respuesta ARP

La respuesta ARP no necesita ser un mensaje de difusión porque su destino es únicamente el mismo dispositivo que ha lanzado la petición de broadcast a toda la red, de manera que el dispositivo que tiene configurada la IP por la que pregunta el equipo de origen contesta sólo a ese equipo con la información de su dirección MAC a través de esta respuesta.

En una captura de Wireshark una respuesta ARP se muestra con el siguiente resumen:

![Respuesta ARP](/assets/img/redes/practica10/img2.png)
*Respuesta ARP (en azul)*

Se trata de un mensaje que, en este caso, tiene origen en el dispositivo RealtekU_e7:19:e0 y que tiene como destino el dispositivo que había lanzando anteriormente la petición: Private_66:68:00. Su longitud es de 46 bytes y, como información, Wireshark muestra el mensaje “192.168.122.1 está en 52:54:00:e7:19:e0”, es decir, un mensaje que relaciona la IP indicada en la petición de broadcast con la MAC del dispositivo que está contestando a la petición.

En el detalle, tanto la trama de la petición como la de la respuesta ARP son muy similares y sólo presentan dos diferencias. En primera lugar, la trama de la petición tiene como destino la MAC de broadcast mientras que la respuesta tiene tanto en su origen como en su destino la MAC de dos máquinas concretas dentro de la red.

Por otra parte, la trama de respuesta incluye información sobre la VLAN a la que pertenece el dispositivo al que se dirige el mensaje.

![Petición ARP](/assets/img/redes/practica10/img3.png)
*Petición ARP*

![Respuesta ARP](/assets/img/redes/practica10/img4.png)
*Respuesta ARP*

# La caché ARP

El funcionamiento de la caché ARP se puede demostrar con un pequeño escenario de cuatro ordenadores conectados a un switch. Al crear el escenario en GNS3 y consultar la tabla de caché ARP de cada uno de los ordenadores conectados al switch, la tabla está vacía.

Después de hacer ping entre el PC1 y el PC2, ambos guardan una entrada en su tabla ARP. En la tabla ARP de PC1 se recoge la dirección MAC del PC2 junto a su dirección IP y, de la misma manera, en la tabla ARP del PC2 se recoge la dirección MAC del PC1 junto a su IP. En cambio, la tabla ARP del resto de ordenadores continúa vacía.

Si desde el PC1 se hace un ping al PC3 las tablas ARP de ambos ordenadores se actualizan, de manera que PC1 conserva información de la dirección MAC e IP de PC3 y PC3 tiene información de la dirección MAC e IP de PC1.

Sin embargo, la información de la dirección MAC del PC2 que el PC1 había almacenado después del primer ping ha desaparecido de la tabla. Esto se debe a que esta tabla es de tipo caché y la información que contiene sólo se almacena de forma temporal. De hecho, al consultar la tabla, se muestra también el tiempo que queda para que se borren, de manera automática, cada una de las entras que contiene.

![](/assets/img/redes/practica10/img5.png)

De esta manera, si se hace un ping desde PC1 a cada uno de los otros tres ordenadores y se consulta la tabla ARP en un corto período de tiempo, PC1 tendrá almacenadas las direcciones MAC de todos los otros ordenadores de la red. Y el resto de dispositivos tendrán almacenada la dirección MAC de PC1. Pero transcurrido el tiempo indicado en las respectivas tablas ARP para la eliminación de estos registros, todas ellas volverán a estar vacías.

# El comando `ip neigh`

El comando `ip neigh` se puede usar para ver o manipular las entradas de la tabla ARP de los dispositivos. Con las diferentes órdenes que admite, `ip neigh` puede mostrar los dispositivos conectados a la red, mostrar las propiedades de estos dispositivos, añadir nuevas entradas a la tabla ARP o eliminar entradas antiguas de esta tabla.

La orden `ip neighour` add añade una nueva entrada a la tabla ARP, la orden `ip neighour change` modificada una entrada en la tabla ARP, `ip neigh delete` elimina una entrada de la tabla ARP, la orden `ip neighour replace` añade una nueva entrada en la tabla ARP o la modifica si ya existía, `ip neighour delete` elimina entradas de la tabla ARP, la orden `ip neighour flush` vacía la tabla ARP o elimina las entradas indicadas, `ip neighour show` lista las entradas de esta tabla y la orden `ip neigh get` busca la entrada de la tabla ARP de un destino que corresponde a un dispositivo indicado.

En definitiva, el comando `ip neigh` tiene dos utilidades importantes: por una parte, mostrar la tabla ARP del equipo y filtrar la información que ofrece y, por otra, intervenir sobre los datos que recoge esa tabla.

Para mostrar la caché ARP se puede ejecutar el comando `ip neigh` sin más opciones ni argumentos o se puede indicar la opción show. Ambas sintaxis son equivalentes. Este comando devuelve la caché ARP. Todas las entradas de esta tabla mantienen la misma estructura, de manera que recogen, en primer lugar, la IP del dispositivo vecino, a continuación el nombre de la tarjeta de red a la que está conectado ese dispositivo vecino, la dirección del nivel de enlace (lladdr) o MAC y  el estado, que se puede identificar como stale, si el dispositivo vecino es válido pero inalcanzable; delay, si la información está obsoleta y se está esperando un respuesta ARP; probe, si se está probando la conexión con el dispositivo vecino; failed, si el dispositivo vecino no es accesible; o reachable, si el dispositivo vecino es válido y accesible.

Una alternativa al comando `ip neighbour` o `ip neighbour show` es el comando `arp` (o `arp -a`), que muestra la misma información con una estructura ligeramente diferente. En este caso lista primero el nombre del dispositivo vecino (junto a su dirección IP si se especifica la opción `-a`). También se muestra la dirección MAC y el estándar que usa la comunicación entre ambos dispositivos. En el formato sin la opción `-a` se añade también la información sobre el índice de máscara. Finalmente, se indica la interfaz de red a la que se conecta cada uno de los dispositivos vecinos.

La otra utilidad principal del comando permite manipular esta tabla ARP añadiendo, modificando o eliminando entradas.

Para añadir una entrada se debe usar la orden `ip neighbour add`. En esta orden se debe indicar la IP del dispositivo vecino que se quiere añadir a la tabla, su dirección MAC precedida de la palabra `lladdr`, el nombre del dispositivo al que se conecta el dispositivo vecino precedido de la palabra `dev` y el estado de la entrada ARP precedido de la palabra `nud`. Algunas opciones para el estado de las entradas de la tabla ARP, además de las mencionadas anteriormente stale y reachable, son permanent (o perm) para indicar que la entrada es válida siempre y sólo puede ser eliminada de forma manual por el administrador de la red o noarp para indicar que no es necesario que la entrada se valide porque es válida siempre pero se puede eliminar de forma automática cuando termine su vida útil.

```
ip neigh add 192.168.10.10 lladdr 00:5v:9d:16:v8:02 dev eth0 nud perm
```

Al igual que ocurre con `ip neighbour show`, la opción de añadir entradas a la tabla ARP también tiene un equivalente usando el comando arp y la opción` -s`. En ese caso la sintaxis es `arp -s` IP MAC.

```
arp -s 192.168.10.10 00:5v:9d:16:v8:02
```

Una de las modificaciones habituales que se puede hacer precisamente usando el comando `ip neighbour` sobre una tabla ARP es cambiar esta etiqueta de estado en las diferentes entradas. Para ello se debe usar la orden` ip neighbour chg` acompañada de la IP del dispositivo vecino, el nombre de la tarjeta de red a la que se conecta ese dispositivo vecino precedido de la palabra `dev` y el nuevo estado precedido de la palabra `nud`.

Para eliminar entradas de esta tabla se usa la orden `ip neighbour delete`, acompañada de la IP del dispositivo vecino junto al nombre de la interfaz a la que se conecta ese vecino precedido de la palabra `dev`.

```
ip neigh del 192.168.10.10 dev eth0
```

Su equivalente en el comando `arp` es la opción `-d`, seguida de la IP cuya entrada se quiere eliminar de la tabla ARP.

```
arp -d 192.168.10.10
```

Además, para eliminar una o todas las entradas de una tabla ARP se puede usar también la opción `ip neighbour flush` seguido de `all` si se pretende vaciar la caché.

```
ip -s -s neigh flush all
```

O seguido de la dirección IP correspondiente a la entrada de la caché ARP que se busque eliminar.

```
ip -s -s neigh flush 192.168.10.10
```

# ARP gratuito

Los mensajes ARP gratuitos son aquellos que se envían sin esperar ninguna respuesta ARP a cambio o que no responden a ninguna petición ARP. Son mensajes que los equipos de una red utilizan para informar a sus dispositivos vecinos de cuál es su dirección IP y su dirección MAC.

Este tipo de mensajes siempre tiene como MAC de destino la dirección de difusión. Una particularidad del ARP gratuito es que usa como IP de destino y también como IP de origen la IP del propio equipo que emite el mensaje, de manera que el mensaje confirma al receptor la IP que se relaciona con la dirección MAC de origen.

El ARP gratuito es útil en el proceso de arranque de una máquina, cuando el equipo lo puede usar para comunicar su IP y su MAC al resto de la red. El objetivo de estos mensajes en este caso es anunciar a la red que existe el dispositivo que se acaba de conectar y que forma parte de la misma e incluir su información en la caché ARP de los dispositivos vecinos sin pedirles iniciar un proceso de comunicación ARP completo. 

Los dispositivos que reciben este tipo de mensajes no necesariamente guardarán esta información en sus tablas ARP, por lo que no siempre es beneficioso para los equipos de la red emplear el ARP gratuito. Sin embargo, tampoco supone ningún problema su uso.

Además, este tipo de mensajes ARP también se usa para mantener actualizadas las tablas ARP de dispositivos como los switches. En estos casos, un nodo puede enviar un mensaje de difusión ARP gratuito para informar al resto de dispositivos de la red de que la dirección MAC que se corresponde con su IP ha cambiado y esa información necesita ser actualizada en la tabla ARP del resto de dispositivos vecinos. 

Este escenario se puede producir cuando se modifica manualmente la dirección MAC de un dispositivo. Aunque esto es poco habitual, puede ocurrir, por ejemplo, al abrir una máquina virtual en un dispositivo diferente al de su creación. En este caso, la máquina virtual tendrá la misma IP pero su dirección MAC será diferente.

El ARP gratuito es fundamental, además, en redes en las que hay dispositivos configurados de manera redundante. En estos casos, se pueden incluir en las redes dispositivos que comparten una misma IP y pueden compartir o no una dirección MAC. Cuando esto ocurre, la única forma de garantizar que la comunicación se mantiene entre todos los dispositivos de la red es a través del uso de los mensajes de ARP gratuito.

En el caso de que haya redundancia de IP entre dos dispositivos, es decir, cuando dos dispositivos tienen una misma IP pero direcciones MAC diferentes, el ARP gratuito se usa para informar al resto de dispositivos de la red con cual de los dos dispositivos se debe comunicar en cada momento. Por ejemplo, si alguno de ellos experimenta un error en su funcionamiento, el otro enviará un mensaje ARP gratuito indicando al resto de vecinos de la red que la suya es la nueva dirección MAC a la que se deben dirigir los paquetes que tengan como destino la IP redundante. Este mensaje modificará las tablas ARP de los dispositivos de la red para garantizar la comunicación entre ellos.

En ocasiones esta redundancia también se da no sólo entre la IP de dos dispositivos sino también entre su dirección MAC de manera que ambos quedan identificados por la misma IP y MAC dentro de la red. En este caso, la información de las tablas ARP del resto de dispositivos de la red no debe experimentar ninguna modificación en caso de avería de alguno de estos dispositivos redundantes pero el switch sí necesita saber que este cambio se ha producido.

Para ello, ante un fallo en el funcionamiento de uno de los dispositivos redundantes, el otro envía un paquete ARP gratuito a la red. Al recibir este paquete, el switch actualiza su tabla de direccionamiento, de manera que pueda redirigir los paquetes que tienen como destino la IP y la MAC redundante hacia el puerto en el que esté conectado el dispositivo que ha emitido el mensaje ARP gratuito. Esta modificación garantiza la comunicación entre el dispositivo con IP y MAC redundante y el resto de dispositivos de la red.

# ARP spoofing

El ARP spoofing consiste en la suplantación de un equipo de la red local a través del protocolo ARP. Esto se consigue enviando información falsa a los equipos conectados a una red de manera que el atacante suplanta con su propia dirección MAC la MAC de uno de los equipos de la red. Habitualmente, la MAC suplantada suele ser aquella que esté asociada a la IP de la puerta de enlace, lo que permite interceptar todas las comunicaciones que circulan entre la red local y el resto de redes. Para suplantar una dirección MAC, los atacantes suelen enviar respuestas ARP gratuitas que no responden a ninguna petición de ningún equipo y así alteran las tablas ARP de los dispositivos de la red.

Una vez que ha suplantado a este dispositivo, el atacante puede recopilar la información que circula por la red dirigida a la IP asociada a la MAC suplantada y también puede controlar el tráfico de la red porque una vez que recibe la información dirigida a la MAC suplantada puede reenviar el tráfico a la MAC real que ha sido suplantada, modificarlos o bloquearlos.

Si el atacante reenvía el tráfico a la MAC original el ataque puede ser pasivo o activo según si altera o no esos datos durante el ataque, respectivamente. En estos casos se puede usar el ARP spoofing para realizar un ataque de tipo Man in the middle, en el que el atacante intercepta las comunicaciones entre dos partes de forma clandestina para extraer información o para controlar o manipular los mensajes que circulan en esa comunicación.

Si el atacante interrumpe el tráfico y no lo reenvía a la MAC suplantada se puede usar el ARP spoofing para realizar un ataque de tipo DoS (denegación de servicio), que consiste en enviar una gran cantidad de mensajes ARP asignando múltiples direcciones IP a una sola MAC hasta que la máquina de destino no pueda gestionar semejante cantidad de respuestas ARP.

Por las características de este tipo de ataques, el ARP spoofing no se puede llevar a cabo de manera remota excepto si se puede acceder a una máquina que forme parte de la red que se quiere atacar. El envenenamiento ARP requiere que la máquina que lo ejecute esté conectada a la red sobre la que se lleva a cabo el ataque, ya sea ésta la máquina del atacante o una máquina de la red vulnerable a la que el atacante pueda acceder de manera remota.

Existen varias formas de proteger una red frente a este tipo de ataques. La más evidente es usar tablas ARP estáticas, evitando el uso de caché dinámicas y asignando de manera manual una dirección MAC a cada IP de cada dispositivo de la red en cada uno de los dispositivos que deban establecer conexiones entre ellos. Evidentemente, esta solución no es práctica en la mayoría de casos y sólo es viable en redes muy pequeñas. En el resto de casos, crear y mantener actualizadas las tablas ARP de forma manual resulta una tarea completamente inabordable.

Con la inspección o snooping de DHCP un dispositivo de red puede guardar un registro de las direcciones MAC de todos los dispositivos que están conectados a cada uno de sus puertos para detectar así cualquier suplantación de ARP.

Además, existe software desarrollado con el objetivo de detectar estas suplantaciones como Arpwatch, que monitoriza las tablas ARP de la red y notifica a su administrador cada vez que se produce un cambio en una entrada de una de ellas. Este tipo de herramientas también permite controlar qué dispositivos hay conectados a la red y qué programas están usando la conexión. Igualmente, pueden llegar a detectar e identificar posibles dispositivos que puedan ser intrusos en la red para evitar que accedan a ella.

Una práctica útil para evitar o minimizar la gravedad de los ataques con ARP spoofing puede ser subdividir la red, ya sea de manera física o de manera lógica mediante el uso de VLAN. Así se consigue evitar que un ataque acceda a toda la red y se limita su impacto únicamente a la parte que presenta alguna vulnerabilidad.

En sistemas y redes modernas se pueden implementar también protocolos de seguridad como el Secure Neighbour Discovery (SEND), que garantiza, a través de algoritmos de encriptación, que los mensajes que recibe un equipo de una red provienen del equipo de origen del que dicen provenir y, por lo tanto, verifica que estos mensajes no han sido interceptados en el trayecto.

Por último, está también la posibilidad de comprobar si hay direcciones MAC clonadas en la red. Habitualmente, cuando una dirección MAC corresponde a más de una dirección IP se puede tomar como un indicio de que se puede estar produciendo una suplantación de ARP, aunque no siempre tiene por qué ser así. Se puede usar el protocolo RARP que, a la inversa que el ARP, permite consultar las direcciones IP asociadas a una dirección MAC para comprobar si existen en la red direcciones MAC que cumplan esta característica y analizar los motivos por los que esa MAC tiene más de una IP asociada. De esta forma, se pueden detectar este tipo de ataques ARP spoofing.

[![Ataque ARP spoofing en Wireshark](https://www.researchgate.net/profile/Sameena-Naaz-3/publication/335810050/figure/fig4/AS:807399380221952@1569510421761/ARP-spoofing-as-captured-in-Wireshark.jpg)](https://www.researchgate.net/publication/335810050_Wireshark_as_a_Tool_for_Detection_of_Various_LAN_Attacks)
*Ataque ARP spoofing en Wireshark*

Un ataque de tipo ARP spoofing se puede detectar en Wireshark atendiendo a dos criterios. En primer lugar, como se puede observar en los dos primeros paquetes capturados, el dispositivo que realiza el ataque, con la MAC 00:0C:29:A5:89:97 tiene dos direcciones IP diferentes asociadas a su misma dirección MAC.

Además, se recogen varios paquetes de tipo ARP reply desde el dispositivo atacante (00:0C:29:A5:89:97) a las otras dos máquinas de la red que no responden a ninguna petición desde éstas. Es decir, no hay ningún mensaje con el contenido “¿Quién es 192.168.253.131? Díselo a 192.168.253.128” o “¿Quién es 192.168.253.128? Díselo a 192.168.253.133” pero sí que hay varios paquetes con el mensaje “192.168.253.131 está en 00:0C:29:A5:89:97” y “192.168.253.128 está en 00:0C:29:A5:89:97”, que son los que el atacante está utilizando para hacer llegar su MAC a las tablas ARP de los otros dos ordenadores de la red.

# MAC flooding

La técnica de MAC flooding o MAC attack se utiliza para atacar a los switches de una red local e interceptar información de sus usuarios. Consiste en inundar la tabla de direcciones MAC de los switches de la red con direcciones falsas. Para ello, el atacante envía múltiples paquetes con direcciones MAC de origen falsas que el switch guarda en su tabla de direcciones MAC hasta que se llena y no es capaz de almacenar más información.

Entonces, el switch comienza a tener un funcionamiento más parecido al de un hub, de manera que transmite todos los paquetes que recibe a todos sus puertos para que pueda mantenerse la comunicación entre todos los dispositivos de la red. Esta técnica da acceso al atacante a todos los dispositivos conectados a cada uno de estos puertos del switch.

Cuando esta técnica tiene éxito, el atacante puede usar una herramienta de análisis de paquetes para capturar la información sensible que circula por la red entre otros equipos y que no sería accesible si la tabla de direccionamiento MAC del switch estuviese funcionando correctamente. Tras realizar un ataque MAC flooding se puede ejecutar un ataque ARP spoofing sobre la red vulnerable, lo que permitiría al atacante seguir teniendo acceso a la red incluso aunque la tabla de direccionamiento MAC del switch recupere su correcto funcionamiento.

Es difícil detectar este tipo de ataques si no se mantiene una monitorización de la red, ya que los indicios de que se está produciendo un ataque pueden responder a múltiples factores. Por ejemplo, este tipo de ataques se puede detectar cuando a un dispositivo comienza a llegar tráfico de la red que está dirigido a otros equipos. Adicionalmente, los ataques MAC suelen implicar un aumento drástico del tráfico en la red o un reducción en la velocidad.

Aunque las medidas de seguridad para proteger a las redes frente a los ataques de tipo ARP spoofing pueden ayudar a prevenir ataques MAC, para evitar este tipo de ataques existen estrategias de seguridad específicas como el port security, un mecanismo que incluyen los routers y los switches que limita el número de direcciones MAC que pueden añadir a su tabla de direccionamiento a la vez que permite especificar que unas direcciones MAC determinadas no se puedan sobrescribir, lo que le permite al dispositivo conservar la información de las direcciones MAC reales de la red en caso de un desbordamiento de direcciones MAC. Además, port security permite al switch bloquear el puerto desde el que sospecha que se está produciendo uno de estos ataques.

En este caso, segmentar la red mediante la configuración de VLAN virtuales no tendria efecto para limitar un ataque MAC flooding, ya que en caso de que éste se produzca, el switch desbordará la información que pase por él a todos los puertos, independientemente de la VLAN a la que pertenezcan.

Adicionalmente, el filtrado de direcciones MAC permite que el switch sólo acepte paquetes que tengan como origen una MAC conocida, de manera que todas aquellas direcciones MAC de origen de los paquetes recibidos por el switch que no estén incluidas en la lista de direcciones conocidas no se guardarán en su tabla de direccionamiento MAC y, por tanto, se evitará así que esta tabla se pueda llenar.

Finalmente, se debe tener en cuenta que el uso de herramientas para monitorizar el tráfico de la red es una estrategia fundamental en la prevención y detección de este y otros tipos de ataque. Algunas de estas herramientas cuentan con configuraciones específicas que permiten detectar de forma automática estos ataques y bloquear el tráfico proveniente de los dispositivos que los están ejecutando.

[![Ataque MAC flooding en Wireshark](https://nz1okaph1l.github.io/assets/img/Posts/l2mac/mac-2.png)](https://nz1okaph1l.github.io/posts/macspoofing/#mac-flooding)
*Ataque MAC flooding en Wireshark*

Como se muestra en esta imagen, una forma sencilla de detectar un ataque de MAC flooding en una captura de tráfico de Wireshark es observando cómo aparecen en ella más direcciones MAC que dispositivos hay en la red. De hecho, en este ejemplo concreto es muy evidente que lo que se está produciendo es un ataque de este tipo porque se recoge un aumento drástico del tráfico en un momento puntual y, además, se encuentra un paquete, el seleccionado en azul, que corresponde a un ping entre dos ordenadores (192.168.12.1 y 192.168.12.2) ajenos en la red que, si no se estuviese produciendo el ataque, no debería llegar al ordenador que realiza la captura (192.168.12.16).

# MAC spoofing

La técnica de MAC spoofing o broadcast spoofing consiste en cambiar a ojos del sistema operativo la dirección MAC de un dispositivo. Esta técnica inhibe la utilidad de las listas de control de acceso en routers y switches además de permitir que se oculte un dispositivo en la red o que suplante la dirección MAC de otro.

Para llevar a cabo un ataque con MAC spoofing un atacante puede emplear diferentes estrategias. Entre ellas, puede manipular directamente el hardware de su tarjeta de red con un switch interno que le permita modificar la MAC de origen en los mensajes que salen de ella pero también puede usar un tercer dispositivo entre el atacante y el switch de la red para hacer llegar sus mensajes con la dirección MAC del dispositivo legítimo de la red. Otra técnica de MAC spoofing empleada habitualmente consiste en usar la dirección MAC de un punto de acceso legítimo de la red o interceptar paquetes que circulan por la red con una MAC de origen legítima que se quiere suplantar y modificar el contenido de esos paquetes para hacerlos llegar al destino deseado.

Esta técnica se usa con mucha frecuencia para atacar redes inalámbricas, especialmente redes wifi, y tener acceso a las credenciales de los usuarios. Por ejemplo, permite crear puntos de acceso no autorizados que suplantan la dirección MAC del punto de acceso legítimo a los que los usuarios se conectan usando sus credenciales. De esta manera, el atacante puede tener acceso a esta información, que pasa por el falso punto de acceso antes de llegar al legítimo.

Como ocurre con los ataques de ARP spoofing y de MAC flooding, la técnica del MAC spoofing requiere que el ataque se realice desde dentro de la propia red local, lo que obliga al atacante a estar físicamente cerca de esta red.

Algunas medidas de prevención contra los ataques MAC spoofing pueden ser controlar que la red no es accesible a través de ningún puerto o servicio que no sea necesario, usar Firewall que añadan niveles de seguridad a la red y permitan un mayor control sobre los datos que circulan por ella o usar métodos de autenticación seguros como, por ejemplo, la autenticación en dos pasos.

Conviene tener en cuenta que tecnologías como el Bluetooth pueden ser especialmente vulnerables a este tipo de ataques y pueden permitir al atacante tener acceso a información como la dirección MAC del dispositivo con la que podría suplantar un punto de acceso no autorizado.