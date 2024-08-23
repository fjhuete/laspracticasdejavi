---
title:  "Configuración de reglas de cortafuegos en router Linux usando iptables"
date:   2024-04-12 02:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, router Linux, cortafuegos, iptables, los juegos del hambre, hunger games, sinsajo]
---
Existen diferentes formas de implementar un cortafuegos en una red, ya sea por nodos o de forma perimetral. En ocasiones se puede dedicar un dispositivo exclusivamente para esta finalidad pero los routers también pueden cumplir esta función. En este post se muestra cómo configurar reglas de cortafuegos en routers Linux en este escenario basado en la saga "Los juegos del hambre", que ya sirvió de inspiración para este otro post.

![](/assets/img/redes/practica14/img1.png)

En este caso, todos los ordenadores que tienen el nombre de un participante masculino tienen instalado un servidor SSH, un [servidor web](/redes/simular-servidor-web-gns3-nginx-apache) y un cliente de base de datos. Los ordenadores que tienen nombre de una participante femenina cuentan con un [servidor de base de datos](/redes/simular-servidor-postgresql-gns3). Además, la máquina del Distrito 13 aloja tanto un servidor web como un servidor de base de datos.

# Direccionamiento del escenario

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

Para asignar una dirección IP estática a cada una de las máquinas que componen el escenario, se debe editar el fichero de configuración /etc/network/interfaces. En él se indica la dirección IP de cada una de las interfaces del dispositivo, su máscara de red y puerta de enlace.

Por ejemplo, el fichero /etc/network/interfaces de Marvel debe incluir las siguientes líneas:

```
#Static config for ens4
auto ens4
iface ens4 inet static
        address 10.0.1.2
        netmask 255.255.255.0
        gateway 10.0.1.1
        dns-nameservers 10.0.1.1
```

De la misma manera, el fichero /etc/network/interfaces del router R1 debe incluir estas líneas:

```
#Static config for ens4
auto ens4
iface ens4 inet static
        address 10.0.1.1
        netmask 255.255.255.0

#Static config for ens5
auto ens5
iface ens5 inet static
        address 173.23.0.1
        netmask 255.255.255.0

#Static config for ens6
auto ens6
iface ens6 inet static
        address 193.168.1.2
        netmask 255.255.255.0
```

En el caso de los routers, como tienen más de una tarjeta de red, no se puede indicar la puerta de enlace en este fichero puesto que los sistemas Linux sólo pueden aceptar una puerta de enlace por dispositivo y no se puede indicar una diferente para cada una de las redes a las que pertenecen los routers.

Así se configuran todas las tarjetas de red de todos los dispositivos del escenario.

# Activar el bit de forwarding

Para configurar las máquinas Linux para que funcionen como router es necesario poner a 1 el bit de forwarding. Esto se puede conseguir de varias formas, tanto de manera temporal como persistente. En este caso, para hacerlo de manera persistente y conseguir que la configuración no se modifique a través de los reinicios de las diferentes máquinas, se usa la opción de incluir la línea `net.ipv4.ip_forward = 1` en el fichero de configuración /etc/sysctl.conf.
Para activar esta línea al fichero se puede editar el mismo con un editor de texto y descomentarla o, de manera más sencilla, usar un echo para redireccionar el texto al fichero y anexarlo al final de su contenido: `echo “net.ipv4.ip_forward = 1” » /etc/sysctl.conf`.

Este paso se repite en todos los router.

# Enrutamiento del escenario

## Tablas de enrutamiento 

### Ordenadores

#### Marvel

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.1.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.1.1 | ens4 |

#### Glimmer

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.1.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.1.1 | ens4 |

#### Cato

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.2.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.2.1 | ens4 |

#### Clove

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.2.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.2.1 | ens4 |

#### Thresh

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.11.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.11.1 | ens4 |

#### Rue

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.11.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.11.1 | ens4 |

#### Peeta

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.12.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.12.1 | ens4 |

#### Katniss

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.12.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.12.1 | ens4 |

#### Distrito 13

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.13.0/24 | 0.0.0.0 | ens4 |
| 0.0.0.0 | 10.0.13.1 | ens4 |

### Routers

#### R1

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.1.0 | 0.0.0.0 | ens4 |
| 173.23.0.0 | 0.0.0.0 | ens5 |
| 173.24.0.0 | 173.23.0.2 | ens5 |
| 173.25.0.0 | 193.168.1.1 | ens6 |
| 193.168.1.0 | 0.0.0.0 | ens6 |
| 193.168.2.0 | 173.23.0.2 | ens5 |
| 193.168.11.0 | 193.168.1.1 | ens6 |
| 193.168.12.0 | 193.168.1.1 | ens6 |

#### R2

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.11.0 | 0.0.0.0 | ens4 |
| 173.23.0.0 | 0.0.0.0 | ens5 |
| 173.24.0.0 | 0.0.0.0 | ens6 |
| 173.25.0.0 | 173.24.0.2 | ens6 |
| 193.168.1.0 | 173.23.0.1 | ens5 |
| 193.168.2.0 | 193.168.11.1 | ens7 |
| 193.168.11.0 | 0.0.0.0 | ens7 |
| 193.168.12.0 | 173.24.0.2 | ens6 |

#### R3

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.2.0 | 0.0.0.0 | ens4 |
| 173.23.0.0 | 193.168.2.1 | ens6 |
| 173.24.0.0 | 173.25.0.2 | ens5 |
| 173.25.0.0 | 0.0.0.0 | ens5 |
| 193.168.1.0 | 193.168.2.1 | ens6 |
| 193.168.2.0 | 0.0.0.0 | ens6 |
| 193.168.11.0 | 193.168.2.1 | ens6 |
| 193.168.12.0 | 173.25.0.2 | ens5 |

#### R4

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.12.0 | 0.0.0.0 | ens4 |
| 173.23.0.0 | 173.24.0.1 | ens5 |
| 173.24.0.0 | 0.0.0.0 | ens5 |
| 173.25.0.0 | 0.0.0.0 | ens7 |
| 193.168.1.0 | 193.168.12.1 | ens6 |
| 193.168.2.0 | 173.24.0.1 | ens5 |
| 193.168.11.0 | 193.168.12.1 | ens6 |
| 193.168.12.0 | 0.0.0.0 | ens6 |

#### R5

| Destino | Siguiente router | Interfaz de salida |
|---|---|---|
| 10.0.13.0 | 0.0.0.0 | ens8 |
| 173.23.0.0 | 193.168.1.2 | ens6 |
| 173.24.0.0 | 193.168.11.2 | ens4 |
| 173.25.0.0 | 193.168.2.2 | ens7 |
| 193.168.1.0 | 0.0.0.0 | ens6 |
| 193.168.2.0 | 0.0.0.0 | ens7 |
| 193.168.11.0 | 0.0.0.0 | ens4 |
| 193.168.12.0 | 0.0.0.0 | ens5 |

## Configuración del enrutamiento en los routers Linux

Para trasladar la información de las tablas de enrutamiento a las máquinas Linux tanto de los participantes como a los routers existen varias opciones. La primera de ellas es añadir líneas a la tabla de enrutamiento usando el comando `ip route add` pero esta forma de volcar al información conlleva un problema: al reiniciar el equipo, la información se pierde.

Para hacer persistente la configuración de la tabla de enrutamiento se puede atender a otras alternativas. Por ejemplo, se puede guardar en el directorio /etc/network/if-up.d un script que añada las líneas de la tabla de enrutamiento cada vez que el estado de una interfaz del router cambie de apagado (down) a encendido (up).

Otra posibilidad es añadir esta información al fichero de configuración /etc/network/interfaces. Por ejemplo, este fichero en el caso del PC de Marvel quedaría así:

```
#Static config for ens4
auto ens4
iface ens4 inet static
        address 10.0.1.2
        netmask 255.255.255.0
        gateway 10.0.1.1
        dns-nameservers 10.0.1.1
        up ip route add default via 10.0.1.1
```

Y en el router R1 contendría la siguiente información:

```
#Static config for ens4
auto ens4
iface ens4 inet static
        address 10.0.1.1
        netmask 255.255.255.0

#Static config for ens5
auto ens5
iface ens5 inet static
        address 173.23.0.1
        netmask 255.255.255.0
        up ip route add 173.24.0.0/24 via 173.23.0.2
        up ip route add 193.168.2.0/24 via 173.23.0.2

#Static config for ens6
auto ens6
iface ens6 inet static
        address 193.168.1.2
        netmask 255.255.255.0
        up ip route add 173.25.0.0/24 via 193.168.1.1
        up ip route add 193.168.11.0/24 via 193.168.1.1
        up ip route add 193.168.12.0/24 via 193.168.1.1
```

De esta manera, se configuran las tablas de enrutamiento de todos los dispositivos del escenario.

# Configuración del NAT

## NAT en R1

En este escenario, cada router tiene una interfaz que da a una red privada y dos o más interfaces que dan a Internet. Por tanto, la configuración NAT se vuelve algo más compleja.

En cada router, se deben aplicar tantas reglas SNAT como interfaces con IP públicas tengan, de manera que todos los paquetes que los atraviesan modifiquen su contenido de IP de origen tanto si salen por una interfaz o por otra.

Por ejemplo, en el caso del router R1, es necesario aplicar dos reglas de SNAT correspondientes a cada una de sus dos interfaces públicas:

```bash
iptables -t nat -A POSTROUTING -s 10.0.1.0/24 -o ens5 -j SNAT --to 173.23.0.1
iptables -t nat -A POSTROUTING -s 10.0.1.0/24 -o ens6 -j SNAT --to 193.168.1.2
```

De esta manera, en los paquetes que salen por la interfaz ens5 hacia el router R2 se modifica su IP de origen de la 10.0.1.2 ó 10.0.1.3 a la 173.23.0.1. En el caso de que los paquetes salgan del router por la interfaz ens6 hacia el router R5, la IP de origen cambia de la 10.0.1.2 ó 10.0.1.3 a la 193.168.1.2.

En el caso del DNAT, en cambio, parece que la opción más adecuada es eliminar del comando de iptables la opción -i, que especifica la interfaz de entrada de los paquetes, de manera que todos los paquetes que lleven como puerto de destino uno de los puertos por los que escuchan los diferentes servidores instalados en la red, cambian su IP de destino por la IP del equipo que ofrece ese servicio, independientemente de la interfaz por la que el router reciba ese paquete.

Por ejemplo, en el router R1, se pueden añadir las siguientes reglas DNAT:

```bash
iptables -t nat -A PREROUTING -p tcp --dport 8085 -i ens5 -j DNAT --to 10.0.1.2:80
iptables -t nat -A PREROUTING -p tcp --dport 8085 -i ens6 -j DNAT --to 10.0.1.2:80
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens5 -j DNAT --to 10.0.1.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens6 -j DNAT --to 10.0.1.2
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens5 -j DNAT --to 10.0.1.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens6 -j DNAT --to 10.0.1.2:22
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens5 -j DNAT --to 10.0.1.3
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens6 -j DNAT --to 10.0.1.3
```

Con esta configuración, todos los paquetes dirigidos al servidor web (puertos 80 y 443) cambian su IP de destino de la 173.23.0.1 ó la 193.168.1.2 a la 10.0.1.2, la del ordenador que aloja este servidor; los dirigidos al servidor SSH (puerto 22) modifican su IP de destino de la  173.23.0.1, si llegan desde el router R2, ó la 193.168.1.2, si llegan desde el router R5, a la 10.0.1.2; y los dirigidos al servidor Postgres (puerto 5432) la modifican de una de las dos IP públicas del router a la privada de la máquina que cuenta con ese servidor: la 10.0.1.3.

Como último paso para aplicar a cada router la configuración NAT necesaria para permitir el acceso a todos los participantes a todos los servicios del escenario, se puede hacer que las reglas NAT sean persistentes en el sistema. Para ello, es imprescindible contar con un fichero que almacene el contenido de las tablas con las que trabaja `iptables`. Para volcar esta información se usa el comando `iptables-save`: 

```bash
iptables-save > /etc/iptables/rules.v4.
```

La información contenida en este fichero se puede recuperar usando el comando `iptables-restore`. Para hacerlo de forma automatizada, se puede incluir este comando en un script con permiso de ejecución con el siguiente contenido:

```bash
#!/usr/bin/env bash
iptables-restore < /etc/iptables/rules.v4
```

De esta manera, al ejecutar el script, se carga en memoria toda la información almacenada en el fichero de reglas de `iptables` que, en este caso, se llama rules.v4 y está almacenado en el directorio /etc/iptables/.

Para terminar de configurar este proceso y garantizar que la configuración de `iptables` se cargue de forma totalmente automática en cada reinicio del sistema, se puede crear, además, un servicio que se encargue de ejecutar este script cada vez que arranque al máquina. Para crear un servicio hay que generar un fichero en el directorio /etc/systemd/system/ con el nombre del servicio, en este caso, iptables.service, por ejemplo.

En este fichero, se guarda la configuración del servicio:

```
[Unit]
Description=Reglas de iptables
After=systemd-sysctl.service
[Service]
Type=oneshot
ExecStart=/usr/local/bin/iptables.sh
[Install]
WantedBy=multi-user.target
```

Y, tras haberlo creado, se habilita y se activa.

```bash
systemctl enable
systemctl start
```

Es importante tener en cuenta que cada vez que se modifique el contenido de las tablas de iptables se debe volcar la nueva configuración al fichero rules.v4, que será la que el servicio creado restaurará tras cada reinicio.

Para hacer posible la comunicación entre todos los participantes a través de  las IP públicas, la configuración NAT se tiene que implementar en todos los routers del escenario.

## NAT en R2

Las reglas SNAT y DNAT son muy similares para todos los routers. Por ejemplo, el SNAT para el router R2 se configura con las reglas:

```bash
iptables -t nat -A POSTROUTING -s 10.0.11.0/24 -o ens5 -j SNAT --to 173.23.0.2
iptables -t nat -A POSTROUTING -s 10.0.11.0/24 -o ens6 -j SNAT --to 173.24.0.1
iptables -t nat -A POSTROUTING -s 10.0.11.0/24 -o ens7 -j SNAT --to 193.168.11.2
```

Y el DNAT de este router incluye las reglas:

```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens5 -j DNAT --to 10.0.11.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens6 -j DNAT --to 10.0.11.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens7 -j DNAT --to 10.0.11.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens5 -j DNAT --to 10.0.11.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens6 -j DNAT --to 10.0.11.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens7 -j DNAT --to 10.0.11.2
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens5 -j DNAT --to 10.0.11.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens6 -j DNAT --to 10.0.11.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens7 -j DNAT --to 10.0.11.2:22
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens5 -j DNAT --to 10.0.11.3
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens6 -j DNAT --to 10.0.11.3
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens7 -j DNAT --to 10.0.11.3
```

## NAT en R3

En el caso del router R3 se indican las siguientes reglas para el SNAT:

```bash
iptables -t nat -A POSTROUTING -s 10.0.2.0/24 -o ens5 -j SNAT --to 173.25.0.1
iptables -t nat -A POSTROUTING -s 10.0.2.0/24 -o ens6 -j SNAT --to  193.168.2.2
```

Y para el DNAT:

```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens5 -j DNAT --to 10.0.2.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens6 -j DNAT --to 10.0.2.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens5 -j DNAT --to 10.0.2.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens6 -j DNAT --to 10.0.2.2
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens5 -j DNAT --to 10.0.2.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens6 -j DNAT --to 10.0.2.2:22
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens5 -j DNAT --to 10.0.2.3
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens6 -j DNAT --to 10.0.2.3
```

## NAT en R4

Si se toma como ejemplo el router R4, las reglas para el SNAT con las que debe contar son:

```bash
iptables -t nat -A POSTROUTING -s 10.0.12.0/24 -o ens5 -j SNAT --to 173.24.0.2
iptables -t nat -A POSTROUTING -s 10.0.12.0/24 -o ens6 -j SNAT --to 193.168.12.2
iptables -t nat -A POSTROUTING -s 10.0.12.0/24 -o ens7 -j SNAT --to 173.25.0.2
```

Y en el caso del DNAT:

```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens5 -j DNAT --to 10.0.12.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens6 -j DNAT --to 10.0.12.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens7 -j DNAT --to 10.0.12.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens5 -j DNAT --to 10.0.12.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens6 -j DNAT --to 10.0.12.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens7 -j DNAT --to 10.0.12.2

iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens6 -j DNAT --to 10.0.12.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens7 -j DNAT --to 10.0.12.2:22
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens5 -j DNAT --to 10.0.12.3
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens6 -j DNAT --to 10.0.12.3
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens7 -j DNAT --to 10.0.12.3
```

## NAT en R5

Por último, las reglas para el router R5 presentan algunas diferencias puesto que a él sólo se conecta un servidor y, en cambio, cuenta con más interfaces públicas que el resto de routers del escenario. Para hacer SNAT necesita las siguientes reglas:

```bash
iptables -t nat -A POSTROUTING -s 10.0.13.0/24 -o ens4 -j SNAT --to 193.168.11.1
iptables -t nat -A POSTROUTING -s 10.0.13.0/24 -o ens5 -j SNAT --to 193.168.12.1
iptables -t nat -A POSTROUTING -s 10.0.13.0/24 -o ens6 -j SNAT --to 193.168.1.1
iptables -t nat -A POSTROUTING -s 10.0.13.0/24 -o ens7 -j SNAT --to 193.168.2.1
```

Y para hacer DNAT se deben indicar estas reglas:

```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens4 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens5 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens6 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens7 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens4 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens5 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens6 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens7 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens4 -j DNAT --to 10.0.13.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens5 -j DNAT --to 10.0.13.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens6 -j DNAT --to 10.0.13.2:22
iptables -t nat -A PREROUTING -p tcp --dport 2222 -i ens7 -j DNAT --to 10.0.13.2:22
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens4 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens5 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens6 -j DNAT --to 10.0.13.2
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens7 -j DNAT --to 10.0.13.2
```

# Establecer la política DROP por defecto

><i class="fas fa-info-circle" aria-hidden="true"></i> En este caso, se configura un cortafuegos de lista blanca, que bloque cualquier tráfico que no esté permitido expresamente. Para ello se debe establecer una política DROP por defecto.
{: .notice--primary}

## Conserver el acceso a los routers por SSH

Antes de establecer las reglas de cortafuegos que bloquean todo el tráfico en los routers, hay que garantizar el acceso por SSH a estos dispositivos para que sigan siendo accesibles para las tareas de administración. Para garantizar este acceso hay que incluir dos reglas de iptables.

En primer lugar, es necesario añadir una regla a la tabla input que acepte el tráfico que tenga como destino el rango de IP de la red local del router y como puerto de destino el 22. Con esta regla, se abre el tráfico SSH de entrada al router para que pueda escuchar peticiones.

Por otra parte, hay que añadir una regla a la tabla output que acepte la salida de tráfico hacia las direcciones IP del rango de la red local del router cuando tenga como puerto de origen el 22. De esta manera, se abre el tráfico SSH de salida del router hacia su red local para que pueda responder las peticiones.

- R1
    ```bash
    iptables -A INPUT -s 10.0.1.0/24 -p tcp --dport 22 -j ACCEPT 
    iptables -A OUTPUT -s 10.0.1.0/24 -p tcp --sport 22 -j ACCEPT
    ```
- R2
    ```bash
    iptables -A INPUT -s 10.0.11.0/24 -p tcp --dport 22 -j ACCEPT
    iptables -A OUTPUT -s 10.0.11.0/24 -p tcp --sport 22 -j ACCEPT
    ```
- R3
    ```bash
    iptables -A INPUT -s 10.0.2.0/24 -p tcp --dport 22 -j ACCEPT
    iptables -A OUTPUT -s 10.0.2.0/24 -p tcp --sport 22 -j ACCEPT
    ```
- R4
    ```bash
    iptables -A INPUT -s 10.0.12.0/24 -p tcp --dport 22 -j ACCEPT
    iptables -A OUTPUT -s 10.0.12.0/24 -p tcp --sport 22 -j ACCEPT
    ```
- R5
    ```bash
    iptables -A INPUT -s 10.0.13.0/24 -p tcp --dport 22 -j ACCEPT
    iptables -A OUTPUT -s 10.0.13.0/24 -p tcp --sport 22 -j ACCEPT
    ```

## Establecer la política DROP por defecto para el resto del tráfico

Con el tráfico SSH garantizado para poder acceder a los routers desde cada una de sus redes locales, se puede implementar en todos ellos la política DROP que bloquee todo el resto del tráfico. Para establecer esta política de bloque se usa la opción -P de iptables, que permite establecer una política por defecto (DROP o ACCEPT) en cada una de las tablas de los routers: INPUT, OUTPUT y FORWARD. En concreto, en cada router hay que ejecutar los siguientes tres comandos: `iptables -P INPUT DROP`; `iptables -P OUTPUT DROP`; `iptables -P FORWARD DROP`.

Tras aplicar esta configuración, los servicios de cada uno de los distritos tienen que se inaccesibles para los participantes del resto. Una forma sencilla de comprobar que esta configuración se ha aplicado correctamente es ver cómo desde dentro de la propia red local se puede acceder al router por SSH pero no se puede hacer ping a su IP.

## Hacer persistente la configuración de `iptables`

Para hacer persistente la configuración de iptables se pueden reutilizar el servicio y el script creados en el punto 4 para hacer persistente el NAT. Simplemente es necesario volver a volcar al fichero /etc/iptables/rulesv.4 toda la información que devuelve el comando `iptables-save` cada vez que se modifique el contenido de las tablas.

# Configuración de las reglas de cortafuegos

## Los indigentes pueden acceder al servidor de bases de datos de los pobres

Para permitir a los indigentes acceder al servidor de bases de datos de los pobres es necesario configurar, por una parte, el router R2 y, por otra, el router R4.

En el router R2, se deben incluir reglas que permitan el tráfico de entrada de los paquetes que tengan como destino el puerto 5432 y de salida de los que tengan como origen ese mismo puerto:

```bash
iptables -A INPUT -p tcp --dport 5432 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 5432 -j ACCEPT
```

En el router R4, en cambio, se deben incluir reglas que permitan el tráfico de entrada de los paquetes que tengan como origen el puerto 5432 y de salida de los que tengan como destino ese mismo puerto:

```bash
iptables -A INPUT -p tcp --sport 5432 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 5432 -j ACCEPT
```

## Los indigentes y los pobres pueden acceder al servidor SSH del distrito 13

Para permitir a los indigentes y los pobres acceder al servidor SSH del distrito 13 se deben configurar los routers R2, R4 y R5.

En el router R2 se deben incluir reglas que permitan el tráfico desde la 193.168.11.1 dirigido al puerto 2222 y hacia la misma IP que llegue desde el puerto de origen 2222.

```bash
iptables -A FORWARD -s 193.168.11.1 -p tcp --sport 2222 -j ACCEPT
iptables -A FORWARD -d 193.168.11.1 -p tcp --dport 2222 -j ACCEPT
```

En el router R4 se deben incluir reglas que permitan el tráfico desde la IP 193.168.12.1 dirigido al puerto 2222 y hacia la misma IP que llegue con puerto de origen también 2222.

```bash
iptables -A FORWARD -s 193.168.12.1 -p tcp --sport 2222 -j ACCEPT
iptables -A FORWARD -d 193.168.12.1 -p tcp --dport 2222 -j ACCEPT
```

Por último, en el router R5 se deben incluir reglas que permitan el paso del tráfico con origen en las IP 193.168.12.2 ó 193.168.11.2 y con puerto de destino 22 y el que tenga destino en las IP  193.168.11.1 ó  193.168.12.1 y puerto de origen 22.

```bash
iptables -A FORWARD -s 193.168.12.2 -p tcp --dport 22 -j ACCEPT
iptables -A FORWARD -s 193.168.11.2 -p tcp --dport 22 -j ACCEPT
iptables -A FORWARD -d 193.168.12.1 -p tcp --sport 22 -j ACCEPT
iptables -A FORWARD -d 193.168.11.2 -p tcp --sport 22 -j ACCEPT
```

## Los pijos pueden acceder al servidor web de Thresh, pero solo de 08 a 15 horas

Para que los pijos puedan acceder al servidor web de Thresh de 8 a 15h se configuran reglas de cortafuegos en los routers R2, R3 y R4.

En el router R2 se tiene que permitir el tráfico con origen en la IP 193.168.2.2 y destino en el puerto 80 y el que tiene destino en esa misma IP y origen también en el puerto 80.

```bash
iptables -A FORWARD -s 173.25.0.1 -p tcp --dport 80 -m time --timestart 8:00 --timestop 15:00 -j ACCEPT
iptables -A FORWARD -d 173.25.0.1 -p tcp --sport 80 -m time --timestart 8:00 --timestop 15:00 -j ACCEPT
```

En el router R3 se permite el tráfico que va desde la el puerto 80 de la IP  193.168.11.2 y el que se dirige al mismo puerto de esa IP.

```bash
iptables -A FORWARD -s 173.24.0.1 -p tcp --sport 80 -m time --timestart 8:00 --timestop 15:00 -j ACCEPT
iptables -A FORWARD -d 173.24.0.1 -p tcp --dport 80 -m time --timestart 8:00 --timestop 15:00 -j ACCEPT
```

Finalmente, en el router R4 se configuran reglas que permitan el tráfico que sale desde la IP  193.168.2.2 hacia la  193.168.11.2 y el puerto 80 y el que tiene como origen el puerto 80 de la IP  193.168.11.2 y como destino la dirección  193.168.2.2

```bash
iptables -A FORWARD -s 173.25.0.1 -d 173.24.0.1 -p tcp --dport 80 -m time --timestart 8:00 --timestop 15:00 -j ACCEPT
iptables -A FORWARD -s 173.24.0.1 -d 173.25.0.1 -p tcp --sport 80 -m time --timestart 8:00 --timestop 15:00 -j ACCEPT
```

## Clover puede acceder a los servidores de los superpijos, excepto al SSH

Para que Clover pueda acceder al servidor web de los superpijos se deben configurar reglas de cortafuegos en los routers R1, R3 y R5.

En el router R1, hay que establecer reglas que permitan el tráfico que tiene origen en la IP 193.168.2.2 y destino en el puerto 80 (después de que el router haya hecho DNAT) y el que tiene destino en la misma IP y origen también en el puerto 80 (antes de que el router haya hecho SNAT).

```bash
iptables -A FORWARD -s 193.168.2.2 -p tcp --dport 80 -j ACCEPT
iptables -A FORWARD -d 193.168.2.2 -p tcp --sport 80 -j ACCEPT
```

En el router R3 se establecen reglas que permitan el tráfico desde la IP 193.168.1.2 y el puerto 8085 y también las que permitan el tráfico desde la IP de Clove (10.0.2.3) a la  193.168.1.2 por el puerto 8085.

```bash
iptables -A FORWARD -s 193.168.1.2 -p tcp --sport 8085 -j ACCEPT
iptables -A FORWARD -s 10.0.2.3 -d 193.168.1.2 -p tcp --dport 8085 -j ACCEPT
```

Por último, en el router R5 se permite el tráfico desde la IP 193.168.2.2 hacia la  193.168.1.2 hacia el puerto 8085 y desde la 193.168.1.2 hacia la 193.168.2.2 con origen en el puerto 8085.

```bash
iptables -A FORWARD -s 193.168.2.2 -d 193.168.1.2 -p tcp --dport 8085 -j ACCEPT
iptables -A FORWARD -d 193.168.2.2 -s 193.168.1.2 -p tcp --sport 8085 -j ACCEPT
```

Para permitir su acceso al servidor Postgres se utilizan las mismas reglas sustituyendo el número de puerto por el del servidor de bases de datos en todos los casos.

En el router R1, hay que establecer reglas que permitan el tráfico que tiene origen en la IP 193.168.2.2 y destino en el puerto 5432 y el que tiene destino en la misma IP y origen también en el puerto 5432.

```bash
iptables -A FORWARD -s 193.168.2.2 -p tcp --dport 5432 -j ACCEPT
iptables -A FORWARD -d 193.168.2.2 -p tcp --sport 5432 -j ACCEPT
```

En el router R3 se establecen reglas que permitan el tráfico desde la IP 193.168.1.2 y el puerto 5432 y también las que permitan el tráfico desde la IP de Clove (10.0.2.3) a la  193.168.1.2 por el puerto 5432.

```bash
iptables -A FORWARD -s 193.168.1.2 -p tcp --sport 5432 -j ACCEPT
iptables -A FORWARD -s 10.0.2.3 -d 193.168.1.2 -p tcp --dport 5432 -j ACCEPT
```

Por último, en el router R5 se permite el tráfico desde la IP 193.168.2.2 hacia la  193.168.1.2 hacia el puerto 5432 y desde la 193.168.1.2 hacia la 193.168.2.2 con origen en el puerto 5432.

```bash
iptables -A FORWARD -s 193.168.2.2 -d 193.168.1.2 -p tcp --dport 5432 -j ACCEPT
iptables -A FORWARD -d 193.168.2.2 -s 193.168.1.2 -p tcp --sport 5432 -j ACCEPT
```

## Peeta puede entrar al servidor web de Marvel por el puerto 8085

Para permitir que Peeta pueda acceder al servidor web de Marvel por el puerto 8085 se deben establecer reglas de cortafuegos en los routers R1, R4 y R5.

En el router R1, hay que establecer reglas que permitan el tráfico que tiene origen en la IP 193.168.12.2 y destino en el puerto 80 (después de que el router haya hecho DNAT) y el que tiene destino en la misma IP y origen también en el puerto 80 (antes de que el router haya hecho SNAT).

```bash
iptables -A FORWARD -s 193.168.12.2 -p tcp --dport 80 -j ACCEPT
iptables -A FORWARD -d 193.168.12.2 -p tcp --sport 80 -j ACCEPT
```

En el router R4, se establecen reglas que permiten el tráfico con origen en la IP 193.168.1.2 y el puerto 8085 y el que tiene destino en esa misma IP y ese puerto y origen en la 10.0.12.2 para que sólo Peeta y no Katniss pueda comunicarse con Marvel.

```bash
iptables -A FORWARD -s 193.168.1.2 -p tcp --sport 8085 -j ACCEPT
iptables -A FORWARD -d 193.168.1.2 -s 10.0.12.2 -p tcp --dport 8085 -j ACCEPT
```

En el router R5 se establecen reglas que permiten el tráfico que sale de la IP 193.168.12.2 y va a la 193.168.1.2 hacia el puerto 8085 así como el que sale desde la  193.168.1.2 hacia la 193.168.12.2 y el puerto 8085.

```bash
iptables -A FORWARD -s 193.168.12.2 -d 193.168.1.2 -p tcp --dport 8085 -j ACCEPT
iptables -A FORWARD -d 193.168.12.2 -s 193.168.1.2 -p tcp --sport 8085 -j ACCEPT
```

## Los pijos pueden acceder al servidor Postgres del distrito 13

Para que los pijos puedan acceder al servidor de bases de datos del distrito 13 se configura el cortafuegos de los routers R3 y R5.

En el router R3 se permite el tráfico desde la dirección 193.168.2.1 y el puerto 5432, así como el que se dirige a esa misma dirección IP y ese puerto.

```bash
iptables -A FORWARD -s 193.168.2.1 -p tcp --sport 5432 -j ACCEPT
iptables -A FORWARD -d 193.168.2.1 -p tcp --dport 5432 -j ACCEPT
```

En el router R5, el tráfico que se permite es el que sale de la IP 193.168.2.2 hacia el puerto 5432 y el que se dirige a esa misma dirección desde ese puerto.

```bash
iptables -A FORWARD -s 193.168.2.2 -p tcp --dport 5432 -j ACCEPT
iptables -A FORWARD -d 193.168.2.2 -p tcp --sport 5432 -j ACCEPT
```

## Katniss puede acceder a los servidores SSH de todos los distritos

Que Katniss pueda acceder a los servidores SSH de todos los distritos requiere la incorporación de reglas a todos los routers del escenario.

En primer lugar, el router R4 tiene que permitir que salgan las peticiones SSH de Katniss a todas las redes y que le lleguen las respuestas desde cualquier otro distrito. Para ello se indican reglas que permitan el tráfico que tiene como destino la IP de Katniss (10.0.12.3) y como origen el puerto 2222 y el que tiene como origen la IP de Katniss (10.0.12.3) y como destino el puerto 2222.

```bash
iptables -A FORWARD -d 193.168.12.2 -p tcp --sport 2222 -j ACCEPT
iptables -A FORWARD -d 173.0.24.2 -p tcp --sport 2222 -j ACCEPT
iptables -A FORWARD -d 173.25.0.2 -p tcp --sport 2222 -j ACCEPT
iptables -A FORWARD -s 10.0.12.3 -p tcp --dport 2222 -j ACCEPT
```

Para que pueda acceder al distrito 1 (superpijos) el router R1 debe permitir el tráfico que sale desde la IP 193.168.12.2 y tiene como puerto de destino el 22 (después del DNAT) y el que va hacia la 193.168.12.2 desde el puerto 22 (antes del SNAT). Además, el router R5 tiene que permitir el tráfico desde la 193.168.12.2 hacia la  193.168.1.2 y el puerto 2222 y, al contrario el tráfico desde el puerto 2222 de la IP 193.168.1.2 que va hacia la 193.168.12.2.

```bash
#R1
iptables -A FORWARD -s 193.168.12.2 -p tcp --dport 22 -j ACCEPT
iptables -A FORWARD -d 193.168.12.2 -p tcp --sport 22 -j ACCEPT
#R5
iptables -A FORWARD -s 193.168.12.2 -d 193.168.1.2 -p tcp --dport 2222 -j ACCEPT
iptables -A FORWARD -d 193.168.12.2 -s 193.168.1.2 -p tcp --sport 2222 -j ACCEPT
```

De la misma forma, para acceder al distrito 2 (pijos) necesita que el router R2 permita el tráfico que va desde la IP 173.24.0.2 hacia el puerto de destino el 22 y el que va hacia la misma IP desde el puerto 22.

```bash
iptables -A FORWARD -s 173.25.0.2 -p tcp --dport 22 -j ACCEPT
iptables -A FORWARD -d 173.25.0.2 -p tcp --sport 22 -j ACCEPT
```

En el caso del distrito 11 (pobres) el router R2 debe incorporar reglas que permitan el tráfico con origen en la IP 173.24.0.2 y destino en el puerto 22 y el que tiene destino en la misma IP y origen en el puerto 22.

```bash
iptables -A FORWARD -s 173.24.0.2 -p tcp --dport 22 -j ACCEPT
iptables -A FORWARD -d 173.24.0.2 -p tcp --sport 22 -j ACCEPT
```

Por último, como Katniss forma parte del distrito 12 (indigentes) ya cuenta con las reglas de cortafuegos configuradas [anteriormente](#los-indigentes-y-los-pobres-pueden-acceder-al-servidor-ssh-del-distrito-13). tanto en el router R4 como en el R5 que le permiten acceder al servidor SSH del distrito 13.

## Rechazar un ataque de denegación de servicios por escaneo de puertos al distrito 13 desde los superpijos

Para prevenir un ataque de denegación de servicios al distrito 13, se pueden implementar algunas reglas de cortafuegos en el router R5.

En primer lugar, se pueden bloquear los paquetes mal formados, que se usan para escanear los puertos de un servidor en busca de vulnerabilidades. Para ello se rechazan los paquetes nulos que no tienen ninguna flag:

```bash
iptables -A FORWARD -p tcp --tcp-flags ALL NONE -j DROP
```

Con esta regla el cortafuegos filtra los paquetes que pasen por el router R5 y busca en ellos todas las flags (primer argumento del modificador `--tcp-flags`). Si de ellas no tiene ninguna (segundo argumento del modificador `--tcp-flags`), entonces rechaza el paquete.

También se pueden rechazar paquetes cuyas flags revelen otros patrones habituales de ataque. Por ejemplo, se puede indicar con el modificador `--tcp-flags` que se busquen las flags `SYN` y `FIN` (primer argumento del modificador `--tcp-flags`) y que rechace aquellos paquetes que contengan ambas (segundo argumento del modificador `--tcp-flags`).

```bash
iptables -A INPUT -p tcp --tcp-flags SYN,FIN SYN,FIN -j DROP
```

Igualmente, se puede aplicar un filtro similar al patrón basado en las flags SYN y RST:

```bash
iptables -A INPUT -p tcp --tcp-flags SYN,RST SYN,RST -j DROP
```

Todas estas reglas se añaden en primer lugar, de manera que se evita que el cortafuegos tenga que analizar y comparar todo el fichero de reglas antes de rechazar los paquetes de este tipo que lleguen al router en su camino al servidor del distrito 13.

Adicionalmente, para evitar otro tipo de ataques de denegación de servicio, se puede limitar el tráfico que puede pasar por el router R5 que tenga como origen la IP 193.168.1.2 usando el módulo limit en cada regla de cortafuegos que se refiera a ese tipo de tráfico, si  en algún momento la hubiese.

```bash
iptables -A FORWARD -s 193.168.1.2 [OTROS CRITERIOS] -p tcp -m limit --limit 10/s -j ACCEPT
```

# Configuración de un servidor DHCP en el Router R1

## Instalación del servidor DHCP

Aunque hay varias opciones para configurar un servidor DHCP en un router linux, la más habitual es usar el servidor isc-dhcp. Para poder usarlo se debe instalar desde los repositorios de Debian:

```bash
sudo apt install isc-dhcp-server
```

## Configuración del servidor DHCP

Antes de poner en funcionamiento el servidor DHCP se configura, en primer lugar, la interfaz por la que acepta conexiones en el fichero /etc/default/isc-dhcp-server:

```
INTERFACESV4="ens4"
```

El resto de la configuración necesaria se puede establecer en el fichero /etc/dhcp/dhcpd.conf, donde se indican, por ejemplo, la dirección de los servidores DNS, la red y rango de IP que el servidor DHCP debe entregar o el tiempo durante el cual los clientes pueden hacer uso de esas direcciones:

```json
subnet 10.0.1.0 netmask 255.255.255.0 {
    range 10.0.1.2 10.0.1.6;
    option routers 10.0.1.1;
    option broadcast-address 10.0.1.255;
}
```

Finalmente, se reinicia el servicio.

```bash
systemctl restart isc-dhcp-server.service
```

Para que los equipos de la red local puedan solicitar direcciones IP por DHCP primero se habilita en el fichero /etc/network/interfaces la configuración por DHCP de la tarjeta de red y se deshabilita la configuración estática.

Posteriormente, tras reiniciar el servicio con `sudo systemctl restart networking.service`, la máquina solicita una dirección IP al servidor DHCP.

## Consecuencias del uso del direccionamiento por DHCP

Al configurar por DHCP, la dirección IP de los equipos cambia periódicamente. Esto genera varios problemas relevantes en el escenario planteado para esta práctica.

En primer lugar, en lo relativo a los cortafuegos, el uso del DHCP dentro de las redes locales sólo afectaría a aquellas reglas en las que se indica que una de las máquinas de la red puede acceder a determinados servidores y otras no, como es el caso de Katniss, Peeta o Cato. Para poder mantener estas reglas si estas redes usasen direcciones IP dinámicas habría que configurar el cortafuegos usando la dirección MAC de estos equipos en lugar de su IP.

En el caso de la red 10.0.1.0/24 este caso no se da y, por tanto, la configuración de cortafuegos planteada en la práctica sería posible incluso aunque los equipos no tuviesen un direccionamiento estático.

Sin embargo ,en este escenario hay que tener en cuenta que los equipos de cada red local no sólo actúan como clientes, sino también como servidores de otros equipos ubicados en otras redes. Esto hace que todos los routers tengan que implementar reglas de DNAT, incluido el router R1. Para que el DNAT funcione correctamente, es imprescindible que los servidores cuenten con una IP estática a la que el router pueda redirigir los paquetes según el puerto al que se dirijan.

Para hacer compatibles el uso del DHCP con el DNAT, sería necesario asignar direcciones IP reservadas en el servidor DHCP a aquellas máquinas que funcionan como servidores, en este caso, ambas máquinas de la red local. Por tanto, es imposible hacer que las reglas planteadas en la práctica funcionen en este supuesto, no por la configuración del cortafuegos, sino por la del DNAT.