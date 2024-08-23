---
title:  "Configuración NAT en routers Mikrotik en Openstack"
date:   2024-03-25 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, Mikrotik, Openstack, NAT]
---
En este post se resuelve un caso práctico de configuración NAT en routers Mikrotik simulados en Openstack. Para ello se crea el siguiente escenario usando una plantilla YAML como se explica en [este post](/redes/enrutamiento-red-openstack).

![](/assets/img/redes/practica13/img1.png)

En este caso, se reutiliza la plantilla de la que se habla en [este otro post](redes/nat-router-linux-openstack) sustituyendo los routers Linux por router Mikrotik. La única modificación necesaria sobre el fichero de creación del escenario es cambiar, en la primera parte del documento en la que se definen las variables, el identificador de la imagen de Debian 12 que se usa en ese post para crear los routers Linux por la imagen de los routers Mikrotik que se utilizan en esta entrada.

Por lo demás, los ordenadores clientes están basados en una imagen de Debian 12 con los clientes de MariaDB y PostgreSQL instalados; y, por último, los servidores se basan en una imagen de Debian 12 con los servicios de [Apache](/redes/simular-servidor-web-gns3-nginx-apache), [PostgreSQL](/redes/simular-servidor-postgresql-gns3), SSH y [MariaDB](/redes/configurar-servidor-dhcp-router-cisco-gns3) instalados y configurados para su funcionamiento.

# Configuración de un servidor DHCP en un router Mikrotik

La configuración DHCP en los escenarios de Openstack se puede realizar [a través de la interfaz gráfica](redes/nat-router-linux-openstack#configuración-del-servidor-dhcp-en-openstack) de la plataforma o desde la plantilla para la creación del escenario. En este caso, se ha especificado la opción `enable_dhcp: true` en la red 7, a la que pertenecen los ordenadores conectados al router Casa.

```yaml
  subnet7:
    type: OS::Neutron::Subnet
    properties:
      network: { get_resource: red7 }
      cidr: 192.168.0.0/24
      enable_dhcp: true
```

Sin embargo, a continuación, se documenta el proceso para la configuración de un servidor DHCP en un router Mikrotik en otros entornos.

Se trata de un proceso muy sencillo. Al ejecutar el comando ip dhcp-server setup en un router, el propio equipo pide el resto de información necesaria para configurar el servidor de manera interactiva. Así, se deben aportar datos como la interfaz del servidor DHCP, el rango de la red, la puerta de enlace para la red, el rango de direcciones que el servidor puede asignar, el servidor DNS y el tiempo de préstamo de la configuración que ofrece el servidor DHCP.

```
ip dhcp-server setup
Select interface to run DHCP server on

dhcp server interface: ether2
Select network for DHCP addresses

dhcp address space: 192.168.0.0/24
Select gateway for given network

gateway for dhcp network: 192.168.0.50
Select pool of ip addresses given out by DHCP server

addresses to give out: 192.168.0.1-192.168.0.49,192.168.0.51-192.168.0.254
Select DNS servers

dns servers: 8.8.8.8
Select lease time

lease time: 10m
```

# Enrutamiento de los routers Mikrotik en Openstack

## Direccionamiento de los routers del escenario

Para asignar la dirección IP otorgada por Openstack a cada tarjeta de red de los routers del escenario se usa el comando `interface ethernet print` para mostrar la dirección MAC de cada puerto. Con esta información se debe comprobar a qué dirección IP de Openstack corresponde la interfaz. Una vez que se conozca esta información se puede pasar al router. Por ejemplo, si la IP de la interfaz ether1 del router Casa es 100.40.0.146:

```
ip address add address=100.40.0.146/24 interface=ether1
```

Esta configuración se aplica a todas las tarjetas de red de todos los routers, así como a las interfaces de los servidores. A los clientes no es necesario asignarles una IP de forma manual porque ya la han recibido a través del protocolo DHCP.

## Enrutamiento de los routers del escenario

### Empresa 1

```
ip route add dst-address=0.0.0.0/0 gateway=100.10.0.114
```

### Empresa 2

```
ip route add dst-address=0.0.0.0/0 gateway=100.20.0.45
```

### Casa

```
ip route add dst-address=0.0.0.0/0 gateway=100.40.0.202
```

### Internet 1

```
ip route add dst-address=100.40.0.0/0 gateway=100.30.0.70
```

### Internet 2

```
ip route add dst-address=0.0.0.0/0 gateway=100.30.0.111
```

# Configuración del SNAT en el router Casa

Configurar el SNAT en un router es algo sencillo. Sólo es necesario ejecutar un comando.

```
ip firewall nat add chain=srcnat src-address=192.168.0.0/24 action=src-nat to-addresses=100.40.0.146 out-interface=ether1
```

Con esto se indica al router que debe modificar la dirección de origen de todos los paquetes que provengan de la red 192.168.0.0/24 y que se enruten por la interfaz ether1 por la IP 100.40.0.146.

De esta manera, tras configurar el SNAT en el router Casa, los ordenadores PC1 y PC2 pueden comunicarse con direcciones IP de fuera de su red.

# Configuración del NAT en los routers de las empresas

## Configuración del SNAT en los routers de las empresas

Para configurar SNAT en los routers de las empresas hay que repetir el mismo comando ejecutado en el router casa.

Para Empresa 1

```
ip firewall nat add chain=srcnat src-address=10.0.0.0/24 action=src-nat to-addresses=100.10.0.129 out-interface=ether2
```

Para empresa 2

```
ip firewall nat add chain=srcnat src-address=10.0.0.0/24 action=src-nat to-addresses=100.20.0.93 out-interface=ether2
```

Ahora sólo queda realizar la configuración necesaria para que estos servidores también puedan recibir tráfico desde fuera de su red.

## Configuración del DNAT en los routers de las empresas

El comando que configura el DNAT en los routers Mikrotik es similar al que se ha usado previamente para la configuración SNAT. 

En el router Empresa 1

```
ip firewall nat add chain=dstnat action=dst-nat dst-address=100.10.0.129 dst-port=80 to-addresses=10.0.0.112 protocol=tcp 
ip firewall nat add chain=dstnat action=dst-nat dst-address=100.10.0.129 dst-port=443 to-addresses=10.0.0.112 protocol=tcp
ip firewall nat add chain=dstnat action=dst-nat dst-address=100.10.0.129 dst-port=5432 to-addresses=10.0.0.120 protocol=tcp
```

En el router Empresa 2

```
ip firewall nat add chain=dstnat action=dst-nat dst-address=100.20.0.93 dst-port=22 to-addresses=10.0.0.102 protocol=tcp
ip firewall nat add chain=dstnat action=dst-nat dst-address=100.20.0.39 dst-port=3306 to-addresses=10.0.0.198 protocol=tcp
```

Esta configuración permite a los ordenadores PC1 y PC2 acceder, desde su red local, a los servidores en las empresas.

><i class="fas fa-info-circle" aria-hidden="true"></i> En este blog también puedes encontrar este mismo caso práactico resuelto usando [routers Cisco en GNS3](/redes/nat-router-cisco-gns3) y usando [routers Linux en Openstack](/redes/nat-router-linux-openstack).
{: .notice--primary}