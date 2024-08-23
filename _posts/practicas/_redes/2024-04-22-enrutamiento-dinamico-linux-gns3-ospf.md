---
title:  "Enrutamiento dinámico con OSPF usando routers Linux en GNS3"
date:   2024-04-22 02:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, router Linux, OSPF]
---
En este post se muestra el funcionamiento del protocolo de enrutamiento dinámico OSPF, un protocolo de red para enrutamiento dinámico que usa el algoritmo Dijkstra, para calcular la ruta más corta entre dos nodos de la red, en los routers Linux de este escenario basado en la saga "Los juegos del hambre".

![](/assets/img/redes/practica14/img1.png)

Las direcciones IP de los diferentes ordenadores son:

- Marvel: 10.0.1.2
- Glimmer: 10.0.1.3
- Cato: 10.0.2.2
- Clover: 10.0.2.3
- Thresh: 10.0.11.2
- Rue: 10.0.11.3
- Peeta: 10.0.12.2
- Katniss: 10.0.12.3

Las interfaces de los routers tienen las siguientes direcciones IP:

- R1 
    - f0/0: 11.0.1.1
    - f0/1: 173.23.0.1
    - f1/0: 193.168.1.2
- R2 
    - f0/0: 11.0.11.1
    - f0/1: 173.23.0.2
    - f1/0: 173.24.0.1
    - f1/1: 193.168.11.2
- R3 
    - f0/0: 11.0.2.1
    - f0/1: 173.25.0.1
    - f1/0: 193.168.2.2
- R4 
    - f0/0: 11.0.12.1
    - f0/1: 173.24.0.2
    - f1/0: 193.168.12.2
    - f1/1: 173.25.0.2
- R5 
    - f0/0: 193.168.11.1
    - f0/1: 193.168.12.1
    - f1/0: 193.168.1.1
    - f1/1: 193.168.2.1
    - f2/0: 11.0.13.1

# Configurar OSPF en un router Linux

## Configurar OSPF desde ficheros

Para poder usar el protocolo OSPF en un router Linux es necesario instalar un paquete que permita gestionar este tipo de protocolos de enrutamiento dinámico. La mayoría de paquetes que cumplen esta función se basan en Zebra, un proyecto que no cuenta con mantenimiento en la actualidad. Uno de estos paquetes es Quagga, que actualmente tampoco está ya disponible en los repositorios de Debian y que ha sido sustituido por FRR (FRRouting), que también se basa en Zebra.

Para instalar FRR se actualiza el gestor de paquetes y se ejecuta la instalación: 

```bash
sudo apt update && sudo apt install frr -y.
```

En este paquete, FRR gestiona la comunicación entre routers vecinos para mantener actualizado el protocolo de enrutamiento dinámico que se emplee en cada caso. Por su parte, Zebra, gestiona y actualiza la tabla de enrutamiento del dispositivo a partir de la información obtenida y procesada por el protocolo de enrutamiento.

El fichero /etc/frr/daemons cuenta con una lista de todos los daemos disponibles en FRR en la que se pueden activar o desactivar. En este caso, para activar el protocolo de OSPF el fichero tiene que contener la línea 

```
ospfd=yes.
```

Tras activar los daemons necesarios (en este caso Zebra está activado por defecto porque debe estar habilitado siempre para el correcto funcionamiento del enrutamiento dinámico) se debe configurar cada uno de ellos a través de sus ficheros de configuración. Una forma de hacerlo es aprovechar los ficheros de ejemplo que se incluyen en /usr/share/doc/frr/examples/ durante la instalación del paquete frr.

Por ejemplo, en el caso de Zebra, se puede copiar el fichero de ejemplo con `cp /usr/share/doc/frr/examples/zebra.conf.sample /etc/frr/zebra.conf` y editar su contenido. En este fichero se indican las direcciones IP de cada una de las interfaces del router. En el caso de R1, este fichero contiene las siguientes líneas:

```
interface ens4
 10.0.1.1/24

interface ens5
 172.23.0.1/24

interface ens6
 193.168.1.2/24
 ```

También se debe seguir un procedimiento similar para configurar el protocolo OSPF en cada router. En este caso, se puede copiar el fichero de ejemplo con cp /usr/share/doc/frr/examples/ospfd.conf.sample /etc/frr/ospfd.conf. En este fichero se almacena la información relacionada con el OSPF en este router: id del router, redes y área a la que pertenece cada una de ellas. Siguiendo con el ejemplo de R1, el fichero contiene estas líneas:

```
router ospf
  ospf router-id 1.1.1.1
  network 10.0.1.0/24 area 0
  network 173.23.0.0/24 area 0
  network 193.168.1.0/24 area 0
```

Además, en ambos ficheros se debe indicar un hostname y contraseña para poder configurar y monitorizar el funcionamiento del protocolo de enrutamiento dinámico posteriormente. Esta información se incluye en ambos ficheros:

```
hostname ospfd
password zebra
enable password zebra
```

De esta manera, queda configurado el OSPF en el router R1.

Esta configuración se repite en todos los routers del escenario para conseguir que puedan actualizar sus tablas de enrutamiento usando este protocolo de enrutamiento dinámico.

## Configurar OSPF desde vtysh

La configuración de los ficheros tal y como se desarrolla en el apartado anterior no da el resultado esperado y no consigue que el protocolo OSPF se ponga en funcionamiento en los routers Linux. Una alternativa a la configuración desde los ficheros es el uso de la terminal vtysh que incorpora el paquete FRR y que usa comandos muy similares a los que usan los routers Cisco.

Para acceder a la terminal se ejecuta el comando vtysh desde un usuario que forme parte del grupo frrvty o como usuario privilegiado. Esto hace que cambie el prompt de la terminal, que muestra un mensaje de bienvenida. Desde esta nueva terminal se pueden ejecutar los comandos de configuración de OSPF en FRR.

Tomando de nuevo como ejemplo el router R1, en primer lugar, se accede a la configuración del router y se activa el modo OSPF y, posteriormente, se añade un identificador del router:

```
debian(config)# router ospf
debian(config-router)# router-id 1.1.1.1
```

Posteriormente, se aporta la información de cada interfaz: su dirección IP y máscara de red. Es este punto también se puede indicar un coste a cada interfaz o un período de “saludo” o de “muerte”.

```
debian(config-router)# interface ens5
debian(config-if)# ip address 173.23.0.1/24
debian(config-if)# interface ens6
debian(config-if)# ip address 193.168.1.2/24
```

Finalmente, se vuelve al modo de configuración de router para añadir las redes que el protocolo debe incluir en la tabla de enrutamiento.

```
debian(config)# router ospf
debian(config-router)# router 1.1.1.1
debian(config-router)# network 173.23.0.0/24 area 0.0.0.0
debian(config-router)# network 193.168.1.0/24 area 0.0.0.0
```

Para terminar se guardan los cambios almacenados en la memoria en el fichero /etc/frr/frr.conf con el comando write memory.

```
debian# write memory
```

De la misma manera, los comandos para aplicar la configuración del OSPF en el router R2 son:

```
debian(config)# router ospf
debian(config-router)# router-id 2.2.2.2
debian(config-router)# interface ens5
debian(config-if)# ip address 173.23.0.2/24
debian(config-if)# interface ens6
debian(config-if)# ip address 173.24.0.1/24
debian(config-if)# interface ens7
debian(config-if)# ip address 193.168.11.2/24
debian(config-if)# router ospf
debian(config-router)# router 2.2.2.2
debian(config-router)# network 173.23.0.0/24 area 0.0.0.0
debian(config-router)# network 173.24.0.0/24 area 0.0.0.0
debian(config-router)# network 193.168.11.0/24 area 0.0.0.0
debian(config-router)# end
debian# write memory
```

En el caso de R3:

```
debian(config)# router ospf
debian(config-router)# router-id 3.3.3.3
debian(config-router)# interface ens5
debian(config-if)# ip address 173.25.0.1/24
debian(config-if)# interface ens6
debian(config-if)# ip address 193.168.2.2/24
debian(config-if)# router ospf
debian(config-router)# router 3.3.3.3
debian(config-router)# network 173.25.0.0/24 area 0.0.0.0
debian(config-router)# network 193.168.2.0/24 area 0.0.0.0
debian(config-router)# end
debian# wr
```

Para R4:

```
debian(config)# router ospf
debian(config-router)# router-id 4.4.4.4
debian(config-router)# interface ens5
debian(config-if)# ip address 173.24.0.2/24
debian(config-if)# interface ens6
debian(config-if)# ip address 193.168.12.2/24
debian(config-if)# interface ens7
debian(config-if)# ip address 173.25.0.2/24
debian(config-if)# router ospf
debian(config-router)# router 4.4.4.4
debian(config-router)# network 173.24.0.0/24 area 0.0.0.0
debian(config-router)# network 173.25.0.0/24 area 0.0.0.0
debian(config-router)# network 193.168.12.0/24 area 0.0.0.0
debian(config-router)# end
debian# wr
```

Y, por último, los comandos para R5 son:

```
debian# configure terminal 
debian(config)# router ospf
debian(config-router)# router-id 5.5.5.5
debian(config-router)# interface ens4
debian(config-if)# ip address 193.168.11.1/24
debian(config-if)# interface ens5
debian(config-if)# ip address 193.168.12.1/24
debian(config-if)# interface ens6
debian(config-if)# ip address 193.168.1.1/24
debian(config-if)# interface ens7
debian(config-if)# ip address 193.168.2.1/24
debian(config-if)# router ospf
debian(config-router)# router 5.5.5.5
debian(config-router)# network 193.168.1.0/24 area 0.0.0.0
debian(config-router)# network 193.168.2.0/24 area 0.0.0.0
debian(config-router)# network 193.168.11.0/24 area 0.0.0.0
debian(config-router)# network 193.168.12.0/24 area 0.0.0.0
debian(config-router)# end
debian# wr
```

# Rutas elegidas por el protocolo

Tras aplicar esta configuración a través de la terminal virtual vtysh, todos los routers se reconocen entre sí y empiezan a modificar sus tablas de enrutamiento.

![](/assets/img/redes/practica15/img8.png)

En las tablas de enrutamiento que genera el protocolo OSPF se indica la IP de la red de destino junto con el coste (entre corchetes), el área y las direcciones IP asignadas a las interfaces por las que un mensaje puede salir hacia ese destino desde cada router. En este caso, OSPF usa como coste por defecto 10 y el coste aumenta con cada salto. De manera que para llegar desde el router R5 (en la última captura) hasta la red 193.168.12.0/24, por ejemplo, el coste es 10 porque sólo hay un salto y el router R5 y R4 (el de destino) están directamente conectados. En cambio, para llegar a la red 173.23.0.0/24 lo puede hacer a través del router R1 (193.168.1.2) o del router R2 (193.168.11.2) pero siempre implica dos saltos, por lo que el coste de esta ruta es 20.

Como se puede observar en estas tablas de enrutamiento, el protocolo es capaz de reconocer todas las rutas que permiten llegar a un destino determinado desde cada router. Esto es clave para poder aplicar una de las características de OSPF: el balanceo de tráfico. Gracias a que el protocolo puede plantear rutas alternativas se puede dirigir parte del tráfico por cada una de ellas para evitar que una se sobrecargue mientras el resto queda libre.

Al retomar los ejemplos del punto 1 de esta práctica en este escenario con routers Linux, se puede comprobar que para llegar al router R4, los paquetes que salen desde el router R1 toman diferentes caminos según la interfaz de destino. Así, para llegar a la interfaz con la IP 193.168.12.2, los paquetes atraviesan el router R5:

![](/assets/img/redes/practica15/img9.png)

En cambio, para llegar a la interfaz con la IP173.25.0.2 los paquetes que salen desde el router R1 toman la ruta que atraviesa el router R2:

![](/assets/img/redes/practica15/img10.png)

# Cambios en las rutas  cuando deja de funcionar el router R5

A diferencia de lo que ocurre en los [routers Cisco](/redes/enrutamiento-dinamico-cisco-gns3-ospf), en el caso de los routers Linux no se notifica a través de un mensaje en la terminal cuando un router se desconecta de la red o deja de funcionar y, por tanto, deja de enviar mensajes de saludo OSPF a sus vecinos. Pero aunque la terminal no muestre una notificación en forma de texto el router sí recibe esta información y es capaz de adaptar su tabla de enrutamiento.

Si se toma como ejemplo el caso de R1, se puede observar cómo su tabla de enrutamiento pasa de esto:

![](/assets/img/redes/practica15/img11.png)

A esto:

![](/assets/img/redes/practica15/img12.png)

El cambio es evidente: el protocolo ha buscado rutas alternativas para llegar a todos los destinos de la red sin tener que pasar por el router R5 y, por tanto, ha eliminado todas las referencias a las IP de ese router de la tabla de enrutamiento de R1.

Para comprobar si el cambio en la tabla de enrutamiento es realmente efectivo, se puede trazar el recorrido que hace un paquete desde R1 hasta la interfaz del router R4 con la IP 193.168.12.2, directamente conectada al router R5. En el punto anterior, este mismo tráfico atravesaba R5. Con R5 apagado, el protocolo lo redirige a través del rotuer R2:

![](/assets/img/redes/practica15/img13.png)

# Cambio del coste de las rutas

Del mismo modo que en los routers Cisco, desde la terminal vtysh de frr se puede modificar el coste de una ruta usando los mismos comandos. Por ejemplo, para aumentar el coste de la ruta entre R4 y R5 de 10 (por defecto) a 100 hay que indicar el nuevo coste en cada una de las interfaces que conecta esta ruta. 

En primer lugar, en en la interfaz ens6 del router R4:

```
debian(config)# interface ens6
debian(config-if)# ip ospf cost 100
```

Y después, en la interfaz ens5 del router R5:

```
debian(config)# interface ens5
debian(config-if)# ip ospf cost 100
```

Al guardar esta configuración, el resto de routers del escenario actualizan de nuevo sus tablas de enrutamiento. Por una parte, en las tablas de R4 y R5, el coste de esta ruta ha aumentado a 100:

![](/assets/img/redes/practica15/img14.png)

En cambio, las tablas de enrutamiento de otros routers del escenario como, por ejemplo, la de R1, reciben esta información y se actualizan sumando, al nuevo coste de la ruta entre R4 y R5 el coste de llegar a ella. Así, el coste de la ruta que va desde R1 a R4 a través del router R5 es ahora no de 110:

![](/assets/img/redes/practica15/img15.png)

Además del coste, en las tablas de enrutamiento de cada router se muestra también la distancia administrativa (110 por defecto para todas las interfaces, en este caso) entre el router y cada destino, de manera que, en caso de que fuese necesario, el protocolo podría tener en cuenta varios criterios para calcular la mejor ruta para que el mensaje llegue a su destino de la forma más eficiente posible.

En este caso, de nuevo, el protocolo elige pasar por R2 para llegar desde R1 a R4 para poder evitar así la ruta con mayor coste del escenario, la que va de R5 a R4:

![](/assets/img/redes/practica15/img16.png)

# Comandos de administración de OSPF en Linux

Tanto durante el proceso de configuración como en fase de producción, el paquete FRR permite el uso de comandos de administración del protocolo OSPF muy similares a los que implementan los routers Cisco. Si bien es cierto que los comandos para la configuración de OSPF desde la terminal virtual vtysh son prácticamente idénticos, en el caso de los comandos de administración hay alguna diferencia.

El comando `show running-config` permite visualizar el contenido del fichero de configuración con la declaración de las interfaces y el router así como con la definición de las redes de las que forma parte.

Con `show ip ospf neighbor` se puede ver cuáles son los routers a los que está directamente conectado el router que se analiza. En Linux con FRR este comando devuelve una tabla en la que se muestra el ID del router vecino, el estado de la conexión, el intervalo del temporizador “Dead”, la dirección IP y la interfaz a la que está conectado cada uno de ellos, entre otra información.

Si el estado de la conexión se FULL, la adyacencia se ha completado exitosamente. Pero esta tabla puede mostrar otros estados. Por ejemplo, si un router tiene un vecino configurado manualmente pero nunca ha recibido paquetes hello desde él, el estado de la conexión será “inactivo”. Los routers vecinos que están en un estado inicial muestran un estado de conexión INIT. En este caso, el router que se analiza ha recibido paquetes de su vecino pero aún no ha podido establecer una comunicación bidireccional. Si ya se ha establecido una comunicación bidireccional pero no se ha completado la adyacencia, el estado de la conexión es 2-WAY.

Junto al estado de conexión se muestran las etiquetas DR o BDR, que indican si el router es un router designado (DR) o un router de respaldo de un router designado (BDR).

La base de datos de OSPF se puede acceder con el comando `show ip ospf database`. En ella se almacena información sobre el estado de los enlaces tanto entre routers como entre redes. En primer lugar, la tabla de estado de los enlaces entre routers lista el identificador de todos los routers almacenados en la base de datos junto con la antigüedad del registro y el número de conexiones que tiene cada uno de ellos. A continuación, también se listan todas las redes a las que se puede acceder desde el router analizado junto al identificador del router que da a esas redes.

Para obtener información sobre las rutas cargadas por el protocolo OSPF se pueden usar comandos como `show ip route`, `show ip route ospf` (que sólo muestra aquellas rutas que se han añadido a la tabla de enrutamiento a través de OSPF) o `show ip ospf route`. En el caso de los routers Linux, también se puede consultar la tabla de enrutamiento con el comando `ip route` sin necesidad de acceder a la terminal virtual de FRR.