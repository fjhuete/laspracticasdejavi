---
title:  "Túneles IPv6: túneles 6to4"
date:   2024-05-21 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, router Linux, Cisco, IPv6, túeneles IPv6, túneles 6to4]
---
En este post se muestra, a través de un ejemplo práctico, el funcionamiento de los túneles IPv6. Estos túneles permiten que el tráfico que va de una a otra dirección IPv6 pase por redes que funcionan usando el portocolo IPv4.

En este caso práctico se usa un escenario formado por 4 PC, 3 routers y un switch:

![](/assets/img/redes/practica17/img1.png)

Los PC se conectan a los routers por IPv6. Los routers se comunican entre ellos por IPv4. Para facilitar la comprensión de esta documentación se asignan direcciones tanto IPv4 como IPv6 estáticas a cada equipo según corresponda. Estas son las direcciones de cada una de las interfaces de los dispositivos de la red:

- PC1
    - e0: 2001:db8:1::102
- PC2
    - e0: 2001:db8:1::103
- PC3
    - e0: 2001:db8:2::202
- PC4
    - e0: 2001:db8:3::302
- R1
    - f0/0: 2001:db8:1::101
    - f0/1: 110.0.0.1
    - f1/0: 120.0.0.1
- R2
    - f0/0: 2001:db8:2::201
    - f0/1: 110.0.0.2
    - f1/0: 130.0.0.2
- R3
    - f0/0: 2001:db8:3::301
    - f0/1: 120.0.0.2
    - f1/0: 130.0.0.1

# Configuración de los equipos

Se puede tomar como ejemplo para la configuración de los PC el caso de PC1. En este caso se usan VPCS de GNS3, así que para asignarle una dirección IPv6 estática se usa el comando `ip 2001:db8:1::102`. Esto le da una dirección global. La dirección de enlace local se genera automáticamente al arrancar la tarjeta de red a partir de su MAC.

![](/assets/img/redes/practica17/img2.png)

Para configurar las direcciones a cada interfaz de los routers se usa el procedimiento habitual en el caso de los routers Cisco. Tomando como ejemplo el router R1, se accede a la terminal de configuración de cada una de las interfaces y, desde ellas, se asigna la dirección correspondiente.

```
R1(config)#interface fastEthernet 0/0
R1(config-if)#ipv6 address 2001:db8:1::101/64 
R1(config-if)#no shutdown 
R1(config-if)#exit 
R1(config)#interface fastEthernet 0/1
R1(config-if)#ip address 110.0.0.1 255.255.255.0
R1(config-if)#no shutdown 
R1(config-if)#exit
R1(config)#interface fastEthernet 1/0
R1(config-if)#ip address 120.0.0.1 255.255.255.0
R1(config-if)#no shutdown 
R1(config-if)#end
R1#wr
```

# Configuración de túneles 6to4 en Cisco

Antes de configurar los túneles en los diferentes routers del escenario es necesario calcular las direcciones IP que deben usar estas interfaces a partir de la dirección IPv4 asignada a las mismas. Las direcciones de los túneles 6to4 comienzan con el prefijo 2002. A continuación se calculan 8 bytes a partir de la dirección IPv4 asociada a la interfaz en la que tiene origen el túnel. Estos 8 bytes son la traducción a hexadecimal de la dirección IPv4.

Además, estas direcciones se pueden calcular usando los siguientes comandos en el router Cisco:

```
R1(config)#ipv6 general-prefix prefijo 6to4 fastEthernet 0/1
R1(config)#do show ipv6 general-prefix
```

Así, las direcciones IPv6 que deben usar los túneles en el escenario son:

- R1:
    - f0/1: 2002:6E00:1::1/64
    - f1/0: 2002:7800:1::1/64
- R2:
    - f0/1: 2002:6E00:1::2/64
    - f1/0: 2002:8200:1::2/64
- R3:
    - f0/1: 2002:7800:1::2/64
    - f1/0: 2002:8200:1:1/64

## Túenel entre R1 y R3

Para configurar un túnel 6to4 entre el router R1 y el router R3 en Cisco, en primer lugar, desde la terminal de configuración se crea el túnel con el comando:

```
R1(config)#interface tunnel 0
```

Posteriormente se asigna la dirección IPv6 al túnel. Los túneles 6to4 siempre tienen una dirección que comienza por 2002::/16.

```
R1(config-if)#ipv6 address 2002:7800:1::1/64
```

A continuación, se asigna el túnel a la interfaz que recibe los mensajes usando el protocolo IPv6.

```
R1(config-if)#tunnel source fastEthernet 1/0
```

Finalmente, se establece el modo del túnel. El modo ipv6op especifica que el protocolo encapsulado es IPv6 y que IPv4 funciona como el protocolo de encapsulamiento y, a la vez, como el protocolo de transporte.

```
R1(config-if)#tunnel mode ipv6ip 6to4
R1(config-if)#end
R1#wr
```

Tras configurar el túnel, se añaden las líneas necesarias a la tabla de enrutamiento:

```
R1(config)#ipv6 route 2001:db8:3::302/64 tunnel 0 2002:7800:2::1
```

Por otra parte, se repite el proceso en el router R3 para configurar el túnel desde R3 hasta R1:

```
R3(config)#interface tunnel 0
R3(config-if)#ipv6 address 2002:6E00:1::1/64
R3(config-if)#tunnel source fastEthernet 0/1
R3(config-if)#tunnel mode ipv6ip 6to4
R3(config-if)#end
R3#wr
```

Y, posteriormente se enruta el túnel:

```
R3(config)#ipv6 route 2001:db8:1::102/64 tunnel 0 2002:7800:1::1
```

Con esta configuración, ya se puede establecer la comunicación entre PC1 y PC4 a través del túnel IPv6 que conecta el router R1 con el router R3.

## Túnel entre R1 y R2

Para permitir el tráfico IPv6 entre los routers R1 y R2 se debe configurar un túnel en cada dirección entre estos dos dispositivos. En el router R2 se debe configurar un túnel de la misma manera que en el caso anterior:

```
R2(config)#interface tunnel 0
R2(config-if)#ipv6 address 2002:6E00:1::2/64
R2(config-if)#tunnel source fastEthernet 0/1
R2(config-if)#tunnel mode ipv6ip 6to4
R2(config-if)#end
R2#wr
```

En este caso, la línea que se debe añadir a la tabla de enrutamiento es la siguiente:

```
R2(config)#ipv6 route 2001:db8:1::102/64 tunnel 0 2002:6E00:1::1
```

En el sentido opuesto, en el router R1 se configura un segundo túnel que lo conecte al router 2:

```
R1(config)#interface tunnel 1
R1(config-if)#ipv6 address 2002:6E00:1::1/64
R1(config-if)#tunnel source fastEthernet 0/1
R1(config-if)#tunnel mode ipv6ip 6to4
```

En este paso de la configuración se produce el siguiente mensaje de error:

![](/assets/img/redes/practica17/img3.png)

Esto se debe a que en los routers Cisco no se puede configurar más de un túnel 6to4 en cada router. De esta forma, la práctica planteada no se puede seguir desarrollando en este escenario.

# Configuración de túeneles 6to4 en routers Linux

Para facilitar la comprensión de esta documentación, el escenario contará con el mismo direccionamiento que el ejemplo anterior.

Por tanto, la configuración aplicable a los routers es la siguiente:

```
#Static config for ens4
auto ens4
iface ens4 inet6 static
    address 2001:db8:1::101
    netmask 64

auto ens5
iface ens5 inet static
    address 110.0.0.1
    netmask 255.255.255.0
    gateway 110.0.0.2

auto ens6
iface ens6 inet static
    address 120.0.0.1
    netmask 255.255.255.0
    gateway 120.0.0.2
```
*R1*

```
#Static config for ens4
auto ens4
iface ens4 inet6 static
    address 2001:db8:1::201
    netmask 64

auto ens5
iface ens5 inet static
    address 110.0.0.2
    netmask 255.255.255.0
    gateway 110.0.0.1

auto ens6
iface ens6 inet static
    address 130.0.0.2
    netmask 255.255.255.0
    gateway 130.0.0.1
```
*R2*

```
#Static config for ens4
auto ens4
iface ens4 inet6 static
    address 2001:db8:1::301
    netmask 64

auto ens5
iface ens5 inet static
    address 120.0.0.2
    netmask 255.255.255.0
    gateway 120.0.0.1

auto ens6
iface ens6 inet static
    address 130.0.0.1
    netmask 255.255.255.0
    gateway 130.0.0.2
```
*R3*

Además, en todos los routers del escenario se activa el bit de forwarding tanto para IPv4 como para IPv6 añadiendo o descomentando las siguientes líneas en el fichero /etc/sysctl.conf:

```
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1
```

## Túnel entre R1 y R3

Para establecer la conexión entre R1 y R3 a través de IPv6 se debe crear un túnel en cada uno de estos dos routers que lo una con el otro. 
En el router R1 se define el túnel:

```bash
ip tunnel add tunel0 mode sit ttl 64 remote 120.0.0.2 local 120.0.0.1
```

El parámetro ttl 64 indica que este es el valor inicial que se coloca en la cabecera IPv4 a la entrada del túnel. Se añade la dirección IPv6 del túnel calculada a partir de la IPv4 de la interfaz:

```bash
ip a add 2002:7800:1::1/64 dev tunel0
```

Después se levanta la interfaz:

```bash
ip l set tunel0 up
```

Y finalmente se enrutan los paquetes dirigidos a la red del PC4, conectado al router R3, a través del túnel:

```bash
ip -6 route add 2001:db8:3::/64 dev tunel0
```

En sentido opuesto, se define el túnel que va desde R3 hasta R1:

```bash
ip tunnel add tunel0 mode sit ttl 64 remote 120.0.0.1 local 120.0.0.2
[ 1536.696601] sit: IPv6, IPv4 and MPLS over IPv4 tunneling driver
ip a add 2002:7800:1::2/64 dev tunel0
ip l set tunel0 up
ip -6 route add 2001:db8:1::/64 dev tunel0
```

Tras establecer esta configuración, PC1 puede comunicarse con PC4 a través del túnel:

![](/assets/img/redes/practica17/img4.png)

Y PC4 también usa el túnel para llegar a PC1:

![](/assets/img/redes/practica17/img5.png)

## Túnel entre R1 y R2

Para conectar los routers R1 y R2 se deben crear los siguientes túneles:

```bash
ip tunnel add tunel1 mode sit ttl 64 remote 110.0.0.2 local 110.0.0.1
ip a add 2002:6E00:1::1/64 dev tunel1
ip l set tunel1 up
ip -6 route add 2001:db8:2::/64 dev tunel1
```
*R1*

```bash
ip tunnel add tunel0 mode sit ttl 64 remote 110.0.0.1 local 110.0.0.2
[ 2726.493888] sit: IPv6, IPv4 and MPLS over IPv4 tunneling driver
ip a add 2002:6E00:1::2/64 dev tunel0
ip l set tunel0 up
ip -6 route add 2001:db8:1::/64 dev tunel0
```
*R2*

## Túnel entre R2 y R3

Para establecer una comunicación a través del protocolo IPv6 entre R2 y R3 se necesitan otros dos túneles.

```bash
ip tunnel add tunel1 mode sit ttl 64 remote 130.0.0.1 local 130.0.0.2
ip a add 2002:8200:1::2/64 dev tunel1
ip l set tunel1 up
ip -6 route add 2001:db8:3::/64 dev tunel1
```
*R2*

```bash
ip tunnel add tunel1 mode sit ttl 64 remote 130.0.0.2 local 130.0.0.1
ip a add 2002:8200:1::1/64 dev tunel1
ip l set tunel1 up
ip -6 route add 2001:db8:2::/64 dev tunel1
```
*R3*

## Demostración del funcionamiento de los túeneles

En una captura de tráfico entre R1 y R3 se aprecia cómo se añade una segunda cabecera del nivel de red al datagrama. Una de ellas usa el protocolo IPv6 y otra el IPv4. En el resumen de la información las IP de origen y destino se mantienen siempre como las IPv6 originales del mensaje pero, en un análisis un poco más profundo se encuentra la cabecera IPv4 que tiene como IP de origen la IPv4 del túnel 6to4 y como destino, la IPv4 del otro extremo del router.

En el ejemplo seleccionado en esta captura, el paquete incluye una cabecera IPv6 que tiene como IP de origen la 2001:db8:1::102, correspondiente al PC1 y como destino la 2001:db8:3::302, correspondiente al PC4. Sin embargo, esa cabecera está dentro de otra, un nivel superior, que usa el protocolo IPv4 y que tiene como IP de origen la 120.0.0.1, correspondiente al router R1 y como destino la 120.0.0.2, correspondiente al router R3.

![](/assets/img/redes/practica17/img6.png)

Un ejemplo similar se puede replicar entre R1 y R2. En este punto, el paquete de petición echo desde PC1 hasta PC3 tiene como dirección de origen la IPv6 2001:db8:1::102, correspondiente al PC1 y como destino la 2001:db8:2::202, correspondiente al PC3. En cambio, una consulta más detallada de las cabeceras del nivel de red demuestra que, además, el paquete incluye una cabecera IPv4 que tiene como dirección de origen la 110.0.0.1, correspondiente al router R1 y como destino la 110.0.0.2, correspondiente al router R2.

![](/assets/img/redes/practica17/img7.png)

Finalmente, el tráfico entre R2 y R3 a través del túnel presenta un comportamiento similar. En la captura de tráfico se puede comprobar cómo los paquetes echo request salen desde el PC4 con la dirección de origen 2001:db8:3::302 y se dirigen a la dirección 2001:db8:2::202, correspondiente al PC3. Sin embargo, al llegar al router R3, el paquete se encapsula dentro de una nueva cabecera IPv4 que tiene como IP de origen la de R3, la 130.0.0.1,  como dirección de destino la 130.0.0.2, correspondiente al router R2.

![](/assets/img/redes/practica17/img8.png)