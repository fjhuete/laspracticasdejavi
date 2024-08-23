---
title:  "Configuración NAT en routers Cisco en GNS3"
date:   2024-03-25 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, Cisco, NAT]
---
NAT (Network Address Translation o traducción de direcciones de red) es un mecanismo que consiste en modificar la información de direccionamiento en los paquetes IP que atraviesan un router. Es necesario para convertir las direcciones IP de las cabecera de los paquetes que salen de la red local o llegan a ella de privadas a públicas y viceversa. En este post se muestra, a través de un caso práctico, cómo se configura este mecanismo en [routers Cisco simulados en GNS3](/redes/importar-dispositivos-GNS3).

Se usa el siguiente escenario que incluye un [servidor web](/redes/simular-servidor-web-gns3-nginx-apache), un [servidor PostgreSQL](/redes/simular-servidor-postgresql-gns3), un [servidor MariaDB](/redes/simular-servidor-mariadb-gns3) y un servidor SSH. Además, el router Casa tiene configurado un [servidor DHCP](/redes/configurar-servidor-dhcp-router-cisco-gns3):

![](/assets/img/redes/practica13/img1.png)

El direccionamiento de los dispositivos es el siguiente:

- Ordenadores:
    - PC1: DHCP
    - PC2: DHCP
    - Servidor 1: 10.0.0.2/24
    - Servidor 2: 10.0.0.3/24
    - Servidor 3: 10.0.0.2/24
    - Servidor 4: 10.0.0.3/24
- Routers:
    - Empresa 1 f0/0: 10.0.0.1/24
    - Empresa 1 f1/0: 100.10.0.1/24
    - Empresa 2 f0/0: 10.0.0.1/24
    - Empresa 2 f1/0: 100.20.0.1/24
    - Internet 1 f0/0: 100.10.0.2/24
    - Internet 1 f1/0: 100.20.0.2/24
    - Internet 1 f1/1: 100.30.0.1/24
    - Internet 2 f0/0: 100.30.0.2/24
    - Internet 2 f1/0: 100.40.0.1/24
    - Casa f0/0: 100.40.0.2/24
    - Casa f1/0: 192.168.0.1/24

# Enrutamiento del escenario

## Tablas de enrutamiento

### Empresa 1

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 10.0.0.0/24 | 0.0.0.0 | f0/0 |
| 100.10.0.0/24 | 0.0.0.0 | f1/0 |
| 0.0.0.0 | 100.10.0.2 | f1/0 |

### Empresa 2

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 10.0.0.0/24 | 0.0.0.0 | f0/0 |
| 100.20.0.0/24 | 0.0.0.0 | f1/0 |
| 0.0.0.0 | 100.20.0.2 | f1/0 |

### Casa

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 192.168.0.0/24 | 0.0.0.0 | f1/0 |
| 100.40.0.0/24 | 0.0.0.0 | f0/0 |
| 0.0.0.0 | 100.40.0.1 | f0/0 |

### Internet 1

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 100.10.0.0/24 | 0.0.0.0 | f0/0 |
| 100.20.0.0/24 | 0.0.0.0 | f1/0 |
| 100.30.0.0/24 | 0.0.0.0 | f1/1 |
| 100.40.0.0/24 | 100.30.0.2 | f1/1 |

### Internet 2

| Destino | Siguiente router | Interfaz de salida |
| --- | --- | --- |
| 100.30.0.0/24 | 0.0.0.0 | f0/0 |
| 100.40.0.0/24 | 0.0.0.0 | f1/0 |
| 0.0.0.0 | 100.30.0.1 | f0/0 |

## Asginar el direccionamiento en los routers Cisco

Para asignar el direccionamiento a cada interfaz de cara router se usa el comando `ip address` desde el modo `config terminal`:

```
Empresa1#conf t
Empresa1(config)#interface fastEthernet 0/0
Empresa1(config-if)#ip address 10.0.0.1 255.255.255.0
Empresa1(config-if)#no shutdown
Empresa1(config-if)#exit
Empresa1(config)#interface fastEthernet 1/0
Empresa1(config-if)#ip address 100.10.0.1 255.255.255.0
Empresa1(config-if)#no shutdown
Empresa1(config-if)#end
Empresa1#wr
```

## Configurar el enrutamiento en los routers Cisco

Para configurar las tablas de enrutamiento en los router Cisco se usa el comando `ip route` desde el modo `config`.

### Empresa 1

En el router Empresa1 hay que añadir a la tabla de enrutamiento la entrada por defecto

```
Empresa1#conf t
Empresa1(config)#ip route 0.0.0.0 0.0.0.0 100.10.0.2
Empresa1(config)#end
Empresa1#wr
```

### Empresa 2

En el router Empresa2 también es necesario añadir únicamente la entrada por defecto.

```
Empresa2#conf t
Empresa2(config)#ip route 0.0.0.0 0.0.0.0 100.20.0.2
Empresa2(config)#end
Empresa2#wr
```

### Casa

Y en el caso del router casa también es necesario indicar cuál es la salida por defecto del tráfico de su red local.

```
Casa#conf t
Casa(config)#ip route 0.0.0.0 0.0.0.0 100.40.0.1
Casa(config)#end
Casa#wr
```

### Internet 1

Para el router Internet1, que en este escenario no necesita incluir una entrada por defecto en su tabla de enrutamiento, es necesario añadir manualmente la información de enrutamiento de los paquetes con destino a la red 100.40.0.0/24, al a que no está conectado directamente.

```
Internet1#conf t
Internet1(config)#ip route 100.40.0.0 255.255.255.0 100.30.0.2
Internet1(config)#end
Internet1#wr
```

### Internet 2

En este escenario, el router Internet2 sí puede usar una entrada por defecto en su tabla de enrutamiento.

```
Internet2#conf t
Internet2(config)#ip route 0.0.0.0 0.0.0.0 100.30.0.1
Internet2(config)#end
Internet2#wr
```

# Configuración del SNAT en el router Casa

Para configurar SNAT en un router Cisco como Casa el primer paso es crear una ACL para definir el tráfico sobre el que se hará NAT. En este caso, será todo el tráfico procedente de la red local a la que está conectado el router independientemente de cuál sea su destino.

A continuación se debe establecer el conjunto de direcciones públicas que el router puede asignar a los paquetes a la hora de hacer NAT. En este caso habrá que indicar la dirección pública estática del router: 100.40.0.2. El siguiente paso es activar el NAT con el comando `ip nat inside source list <n> pool <nombre> overload` y el último paso es activar el NAT en cada una de las interfaces involucradas.

```
Casa#conf t
Casa(config)#access-list 1 permit any
Casa(config)#ip nat pool CASA-PUBLICA 100.40.0.2 100.40.0.2 netmask 255.255.255.0
Casa(config)#ip nat inside source list 1 pool CASA-PÚBLICA overload
Casa(config)#interface fastEthernet 0/0
Casa(config-if)#ip nat oustide
Casa(config-if)#exit
Casa(config)#interface fastEthernet 1/0
Casa(config-if)#ip nat inside
Casa(config-if)#end
Casa#wr
```

# Configuración del NAT en los routers de las empresas

## Configuración del DNAT en los routers de las empresas

Configurar el DNAT en los routers Cisco es algo más sencillo. Sólo es necesario ejecutar en ellos un comando en el que se debe indicar la IP privada y el puerto al que se deben dirigir las peticiones que lleguen a una determinada IP pública con un determinado puerto de destino.

Así, se deben indicar las órdenes para redirigir los mensajes que lleguen al router Empresa1 hacia el servidor web para el tráfico http y también para el tráfico https y para el servidor PostgreSQL.

```
Empresa1#conf t
Empresa1(config)#ip nat inside source static tcp 10.0.0.2 80 100.10.0.1 80
Empresa1(config)#ip nat inside source static tcp 10.0.0.2 443 100.10.0.1 443
Empresa1(config)#ip nat inside source static tcp 10.0.0.3 5432 100.10.0.1 5432
Empresa1(config)#end
Empresa1#wr
```

En el router Empresa2 también hay que aplicar el DNAT para dirigir hacia el servidor SSH el tráfico de este tipo y hacia el servidor MariaDB el que sea necesario.

```
Empresa2#conf t
Empresa2(config)#ip nat inside source static tcp 10.0.0.2 22 100.20.0.1 22
Empresa2(config)#ip nat inside source static tcp 10.0.0.3 3306 100.20.0.1 3306
Empresa2(config)#end
Empresa2#wr
```

## Configuración del SNAT en los routers de las empresas

Para configurar el SNAT en estos routers se deben seguir los mismos pasos que en el caso del router Casa. En primer lugar se debe crear la ACL que, en estos dos routers, también debe permitir todo el tráfico de la red local independientemente de cuál sea su destino. Posteriormente, se crea el pool de direcciones públicas que el router puede asignar como IP de origen a los paquetes.  A continuación será necesario activar el NAT. 

```
Empresa1#conf t
Empresa1(config)#access-list 1 permit any
Empresa1(config)#ip nat pool EMPRESA1 100.10.0.1 100.10.0.1 netmask 255.255.255.0
Empresa1(config)#ip nat inside source list 1 pool EMPRESA1 overload
Empresa1(config)#end
Empresa1#wr
```

```
Empresa2#conf t
Empresa2(config)#access-list 1 permit any
Empresa2(config)#ip nat pool EMPRESA2 100.20.0.1 100.20.0.1 netmask 255.255.255.0
Empresa2(config)#ip nat inside source list 1 pool EMPRESA2 overload
Empresa2(config)#end
Empresa2#wr
```

Finalmente, con los comandos `ip nat inside` e `ip nat outside` se indicará en cada router cuál es la interfaz de entrada y de salida para el NAT.

><i class="fas fa-info-circle" aria-hidden="true"></i> En este blog también puedes encontrar este mismo caso práactico resuelto usando [routers Linux](/redes/nat-router-linux-openstack) y [routers Mikrotik](/redes/nat-router-mikrotik-openstak) en Openstack.
{: .notice--primary}