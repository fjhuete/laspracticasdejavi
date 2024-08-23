---
title:  "Enrutamiento dinámico con OSPF usando routers Cisco en GNS3"
date:   2024-04-22 02:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, Cisco, OSPF]
excerpt: OSPF (Open Shortest Path First), "Abrir el camino más corto primero" en español, es un protocolo de red para enrutamiento dinámico que usa el algoritmo Dijkstra, para calcular la ruta más corta entre dos nodos.
---
OSPF (Open Shortest Path First), "Abrir el camino más corto primero" en español, es un protocolo de red para enrutamiento dinámico que usa el algoritmo Dijkstra, para calcular la ruta más corta entre dos nodos.

A continuación se muestra el funcionamiento del protocolo de enrutamiento dinámico OSPF en este escenario basado en la saga "Los juegos del hambre".

![](/assets/img/redes/practica12/img1.png)

El direccionamiento IP de los VPCS del escenario es el siguiente:

- Marvel: 10.0.1.2
- Glimmer: 10.0.1.3
- Cato: 10.0.2.2
- Clover: 10.0.2.3
- Thresh: 10.0.11.2
- Rue: 10.0.11.3
- Peeta: 10.0.12.2
- Katniss: 10.0.12.3

El direccionamiento IP de los routers del escenario es el siguiente:

- R1 
    - f0/0: 10.0.1.1
    - f0/1: 172.23.0.1
    - f1/0: 192.168.1.2
- R2 
    - f0/0: 10.0.11.1
    - f0/1: 172.23.0.2
    - f1/0: 172.24.0.1
    - f1/1: 192.168.11.2
- R3 
    - f0/0: 10.0.2.1
    - f0/1: 172.25.0.1
    - f1/0: 192.168.2.2
- R4 
    - f0/0: 10.0.12.1
    - f0/1: 172.24.0.2
    - f1/0: 192.168.12.2
    - f1/1: 172.25.0.2
- R5 
    - f0/0: 192.168.11.1
    - f0/1: 192.168.12.1
    - f1/0: 192.168.1.1
    - f1/1: 192.168.2.1
    - f2/0: 10.0.13.1

# Configurar OSPF en un router Cisco

La configuración del protocolo OSPF en los routers Cisco requiere acceder al modo `config terminal` del router y, desde él, ejecutar en primer lugar el comando `router ospf <id>` en el que se inicia un proceso OSPF y se le asigna un identificador. Este comando abre el modo `config-router`. Entonces se indica un identificador para el router con el comando `router-id <n.n.n.n>` y se añaden las diferentes interfaces a las que se aplica este protocolo con el comando `network <ip> <máscara comodín>`.

Por ejemplo, en el caso del router R1 en este escenario se accede al modo `config-rotuer` con `router ospf 1` y, a continuación, se indica un identificador al router con `router-id 1.1.1.1`. Finalmente, se añade la información sobre todas las redes a las que este dispositivo está conectado:

```
R1#config terminal
R1(config)#router ospf 1
R1(config-router)#router-id 1.1.1.1
R1(config-router)#network 10.0.1.0 0.0.0.255 area 0
R1(config-router)#network 172.23.0.0 0.0.0.255 area 0
R1(config-router)#network 192.168.1.0 0.0.0.255 area 0
```

La misma configuración se establece también pero indicando los valores correspondientes al resto de routers del escenario.

# Rutas elegidas por el protocolo OSPF

Tras configurar el OSPF en todos los routers del escenario, las tablas de enrutamiento de cada uno de ellos sufren modificaciones y, además contienen nueva información. Ahora las entradas de las tablas de enrutamiento son de dos tipos. Aquellas que se refieren a una dirección a la que el router está directamente conectado se marcan como C (conected) y el resto se marcan con una O (OSPF). Además, cada entrada incluye información del coste de la ruta. En una primera aproximación al uso de OSPF se establece en todos los routers la misma distancia por defecto para todas las rutas: 110. Junto a este valor de distancia, cada entrada de la tabla de enrutamiento recoge el número de saltos necesarios para llegar a la dirección de destino.

Por ejemplo, en la tabla de enrutamiento del router R1 las rutas a las redes 172.23.0.0/24 y 192.168.1.0/24 está marcadas como conectadas. El resto de destinos se descubren y añaden a la tabla de enrutamiento gracias al protocolo OSPF. Todas las rutas que no están conectadas directamente a este dispositivo muestran una distancia administrativa por defecto de 110. Mientras que algunas de ellas cuentan con 2 saltos, otras requieren tres para llegar al destino. Además, en varios casos como ocurre con la red 172.25.0.0/24 o la 192.168.11.0/24 la tabla de enrutamiento de este router recoge dos opciones diferentes para llegar al mismo destino con diferente latencia.

De la tabla de enrutamiento de R1 se desprende que el protocolo OSPF optaría por usar el router R5 en todos los casos en los que el mensaje necesita atravesar un router intermedio para llegar a su destino puesto que estas son las rutas que presentan una menor latencia.

![](/assets/img/redes/practica15/img1.png)

Se puede comprobar que las rutas recogidas en la tabla de enrutamiento se están cumpliendo de manera efectiva al comunicar, por ejemplo, el router R1 con el router R4. En este caso, el mensaje viaja desde el router R1 al R5 y, posteriormente, llega a R4.

![](/assets/img/redes/practica15/img2.png)

Si se traza la ruta desde R1 hasta la interfaz f1/1 del router R4, el router R1 devuelve dos posibles caminos: a través del router R2 (172.23.0.2 y 172.24.0.2) o a través del router R5 (192.168.1.1 y 192.168.2.2).

![](/assets/img/redes/practica15/img3.png)

Un análisis similar se puede hacer de la tabla de enrutamiento que OSPF genera en el router R2: se puede observar que el protocolo de enrutamiento dinámico ha priorizado aquellas rutas en las que el mensaje puede llegar a su destino en menos saltos, por ejemplo, para ir del router R2 al R4 no pasa por R5.

Así se demuestra si se traza el recorrido de un paquete desde el router R2 al router R4. El mensaje va de R2 a R4 directamente a través de la interfaz f1/0 (R2) y f0/0 (R4).

En este caso, si se traza el camino hasta otra interfaz del router R4, por ejemplo la f1/1 (172.25.0.2) el router R2 sólo devuelve una ruta porque cualquier otra tendría más saltos y, por lo tanto, un mayor coste.

# Cambios en las rutas cuando deja de funcionar el router R5

Uno de los objetivos principales del enrutamiento dinámico es mantener la disponibilidad de las conexiones, de manera que los mensajes puedan llegar a su destino independientemente de si alguno de los dispositivos que, en principio, debería atravesar está estropeado o no funciona en un momento determinado.

En este caso, si se apaga uno de los routers del escenario, por ejemplo el router R5, todo el resto de dispositivos de la red recibe, poco segundos después, esta información:

![](/assets/img/redes/practica15/img4.png)

Para garantizar que se pueden mantener las comunicaciones entre todos los equipos con, OSPF modifica las tablas de enrutamiento de los routers, de manera que busca alternativas para encaminar los mensajes.

Por ejemplo, en la tabla de enrutamiento del router R1 desaparecen todas las referencias a las direcciones de R5 y se sustituyen por otras IP de alguno de los otros routers a los que está conectado para establecer rutas alternativas a todos los destinos de la red.

![](/assets/img/redes/practica15/img5.png)

En este caso, al repetir el análisis de la ruta entre el router R1 y R4 se pueden encontrar diferencias significativas. Por ejemplo, si se usa la interfaz f1/0 de R4 (192.168.12.2) directamente conectada al router R5, el protocolo de enrutamiento encuentra una vía alternativa a través de la interfaz f0/0, conectada a R2. Así, el mensaje puede llegar a su destino atravesando R2 en vez de R5.

![](/assets/img/redes/practica15/img6.png)

Esa misma es la ruta que elige para llegar a la interfaz f1/1 de R4 (172.25.0.2), que está directamente conectada al router R3. En este caso, a diferencia del ejemplo anterior en el que había dos opciones posibles para hacer esta ruta, como R5 está apagado, el análisis del recorrido del mensaje con `trace` siempre muestra la única ruta disponible.

![](/assets/img/redes/practica15/img7.png)

De la misma manera, el resto de routers también eliminan de sus tablas de enrutamiento las referencias a las rutas que atraviesan R5 y que sí recogían en un primer momento.

# Cambio del coste de las rutas

Para cambiar el coste de las rutas en los routers Cisco hay que asignar un nuevo coste a cada interfaz individualmente usando el comando `ip ospf cost <coste>` desde el modo de configuración de interfaz. Por defecto, el coste se calcula dividiendo 100.000.000 entre el ancho de banda en bps de la conexión porque, en esta configuración, el término de coste está orientado a la velocidad de conexión. Pero hay otros criterios que pueden influir en el coste que se le asigna a una ruta como la propiedad de la línea, el número de saltos, la latencia, etc. Por eso, el coste de cada interfaz se puede modificar manualmente.

Por ejemplo, si el router R5 vuelve a funcionar envía a todo el resto de routers del escenario varios paquetes en su proceso de arranque. Los demás reciben esta información y algunos de ellos vuelven a modificar sus tablas de enrutamiento para recuperar aquellas rutas que usan este dispositivo.

Para modificar el coste de conectarse a cada interfaz, se accede a cada una de ellas a través de `interface FastEthernet <id>` y, desde el modo config-if se ejecuta el comando `ip ospf cost 120` que, en este caso, asigna un coste de 120 a la conexión a esa interfaz del router R5. El comando se tiene que repetir en la interfaz del otro router a la que esté conectada esa tarjeta de red para asignar el coste de la ruta. En este caso, en la interfaz f1/0 del router R1.

En este escenario, el ejemplo analizado en casos anteriores devuelve un resultado diferente: ante el alto coste de la ruta que conecta el router R1 con R5, el protocolo OSPF usa una ruta alternativa que lleva el mensaje desde R1 a R2, desde R2 a R5 y, finalmente, desde R5 llega a su destino en R4. En este caso, el protocolo prefiere usar una ruta con una distancia administrativa mayor, puesto que conlleva dos saltos entre routers en vez de uno, pero que tiene un coste menor puesto que el coste entre R1 y R5 es 120 mientras que el coste tanto entre R2 y R5 como entre R5 y R4 es 1.

# Comandos de administración de OSPF en Cisco

En los routers Cisco existen varios comandos para comprobar el correcto funcionamiento del OSPF tanto durante su configuración como durante su fase en producción.

Por ejemplo, `show running-config` muestra la configuración del router. A diferencia de lo que ocurre con este mismo comando en FRR en los routers Linux, en el caso de los routers Cisco muestra toda la configuración del router y no sólo la de OSPF. Además, `show ip protocols` muestra información sobre los protocolos que se aplican en el router. En el caso del OSPF se recoge el identificador del router junto con el número de áreas de las que forma parte y el número de rutas a las que está conectado. Además, lista esas redes y muestra los routers desde los que obtiene la información con su identificador, distancia administrativa y última actualización.

Con `show ip ospf interface` se puede obtener información sobre cómo afecta el OSPF a cada interfaz. Esta información incluye la IP, el identificador del router, el coste de la ruta, el identificador del router designado en esa ruta, el identificador del router de respaldo designado, los intervalos de tiempo tanto de Hello como Dead, el tiempo restante para el siguiente mensaje de Hello, una lista de vecinos adyacentes, el estado de la conexión, etc. Este comando se puede particularizar añadiendo un nombre de interfaz concreta para ver sólo la información relativa a esa interfaz o con `show ip ospf interface brief` para mostrar la información de forma más resumida.

Sobre los vecinos se puede obtener información con `show ip ospf neighbor`. De forma muy similar a lo que ocurre en los routers Linux con FRR, este comando muestra en una tabla el identificador del vecino, el estado de la conexión, el intervalo de “Dead” y la dirección y la interfaz por la que se conecta el router a cada vecino adyacente.

Además, es muy práctico revisar las tablas de enrutamiento con `show ip route` para ver cómo les afecta OSPF en cada momento.