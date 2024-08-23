---
title:  "Configuración NAT en routers Linux en Openstack"
date:   2024-03-25 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, router, router linux, Openstack, NAT]
---
Como se explica en [este post](/redes/enrutamiento-red-openstack), Openstack puede usar ficheros YAML como plantilla para la creación en lote de máquinas virtuales y redes. En este caso, se usa una plantilla de este tipo para crear el siguiente escenario:

![](/assets/img/redes/practica13/img1.png)

El direccionamiento de los dispositivos es el siguiente: {#direccionamiento}

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

Tras definir los parámetros que se usarán a lo largo de la plantilla, se definen los recursos que se van a montar en el escenario. Se empieza por las redes. En este caso, hay 7 redes y a cada una de ellas se le asigna un nombre:

```yaml
red1:
    type: OS::Neutron::Net
    properties:
      name: "Red 1"
```

A continuación, se definen las subredes. En ellas, se especifican propiedades como el rango de direcciones IP que se va a asignar a las instancias de cada red. También es en este punto del fichero donde se puede habilitar o deshabilitar el protocolo DHCP para cada una de las redes. En este ejercicio, el protocolo DHCP está deshabilitado para todas las redes excepto para la 7, en la que el enunciado especifica que los ordenadores reciben su dirección IP por DHCP.

```yaml
subnet1:
    type: OS::Neutron::Subnet
    properties:
      network: { get_resource: red1 }
      #Rango de IP en cada red
      cidr: 10.0.0.0/24
      enable_dhcp: false
```

El siguiente recurso que se define es cada una de las tarjetas de red o puertos de las instancias. En esta parte de la plantilla se elige el número de interfaces con las que va a contar cada equipo. Además, en el caso de los routers, es importante habilitar la opción de pares de direcciones permitidas, un campo que indica las direcciones IP cuyo tráfico se debe permitir a través de ese puerto. Para que se permita el tráfico de todas las IP se usa la 0.0.0.0/0.

```yaml
  servidor2_red1:
    type: OS::Neutron::Port
    properties:
      network: { get_resource: red1 }

  r1_red1:
    type: OS::Neutron::Port
    properties:
      network: { get_resource: red1 }
      allowed_address_pairs:
        - ip_address: 0.0.0.0/0 
```

Finalmente, el último recurso que se define en la plantilla es cada una de las instancias que se crearán en el escenario. En este apartado se indica el nombre de las instancias y también se les asigna el sabor y la imagen que se va a usar para su creación. Además, en el apartado networks se referencia cada una de las interfaces de red con las que van a contar.

```yaml
servidor2:
    type: OS::Nova::Server
    properties:
      name: "Servidor 2"
      flavor: { get_param: flavor }
      image: { get_param: image_servidores }
      networks:
        - { port: { get_resource: servidor2_red1 } } 
  r1:
    type: OS::Nova::Server
    properties:
      name: "Empresa 1"
      flavor: { get_param: flavor }
      image: { get_param: image }
      networks:
        - { port: { get_resource: r1_red1 } }
        - { port: { get_resource: r1_red3 } }
pc1:
    type: OS::Nova::Server
    properties:
      name: "PC 1"
      flavor: { get_param: flavor }
      image: { get_param: image_pc }
      networks:
        - { port: { get_resource: pc1_red7 } }
```

La última parte de la plantilla se corresponde a los outputs. En este apartado se da nombre a cada una de las direcciones MAC que Openstack asignará automáticamente a las diferentes interfaces que conforman el escenario durante su creación.

```yaml
mac_r1_red1:
    description: "Empresa 1 - Red 1"
    value: { get_attr: [r1_red1, mac_address] }
```

Para lanzar el escenario en Openstack se han usado tres imágenes diferentes. Los routers usan una imagen con el sistema básico de Debian 12, al que a penas se la ha aplicado la configuración mínima durante el proceso de instalación; los ordenadores clientes están basados en una imagen de Debian 12 con los clientes de MariaDB y PostgreSQL instalados; y, por último, los servidores se basan en una imagen de Debian 12 con los servicios de [Apache](/redes/simular-servidor-web-gns3-nginx-apache), [PostgreSQL](/redes/simular-servidor-postgresql-gns3), SSH y [MariaDB](/redes/configurar-servidor-dhcp-router-cisco-gns3) instalados y configurados para su funcionamiento.

><i class="fas fa-lightbulb" aria-hidden="true"></i> Usar imágenes con el software que se va  usar previamente instalado evita tener que configurar el enrutamiento y un router que permita a todas las máquinas de la red acceder a Internet para poder instalar los paquetes necesarios.
{: .notice--primary}

# Enrutamiento de routers Linux en Openstack

## Direccionamiento del escenario

Tras generar el escenario en Openstack con el fichero yaml se deben asignar manualmente las direcciones IP indicadas [más arriba](#direccionamiento) a cada una de las interfaces de las máquinas del escenario. Después, hay que activar todas las interfaces de cada equipo. Por ejemplo, para el router Empresa 1:

```bash
ip a add 10.0.0.1/24 dev ens3
ip a add 100.10.0.1/24 dev ens4
ip l set ens3 up
ip l set ens4 up
```
Así se configurarán, sucesivamente, todas las máquinas presentes en el escenario.

## Enrutamiento del escenario

Con el escenario establecido en Openstack, todas las direcciones IP asignadas y tras comprobar el correcto funcionamiento de todos los servidores dentro de su red local, se puede llevar a cabo el enrutamiento de las diferentes redes que configuran el escenario. Previamente, conviene activar en todos los dispositivos que funcionan como router el bit de forwarding que les permita transmitir paquetes de una a otra de sus tarjetas de red.

### Bit de forwarding

El bit de forwarding permite que las máquinas linux enruten paquetes entre las diferentes redes a las que estén conectadas sus tarjetas de red. Existen diferentes formas de activarlo pero para hacer la configuración persistente entre reinicios es necesario añadir la línea "net.ipv4.ip_forward = 1" al fichero de configuración /etc/sysctl.conf.

```bash
echo “net.ipv4.ip_forward = 1” » /etc/sysctl.conf
```

### Tablas de enrutamiento

En este caso, se usan las mismas tablas de enrutamiento que en [este otro post](/redes/nat-router-cisco-gns3#tablas-de-enrutamiento).

#### Empresa 1

```bash
ip route add default 100.10.0.2
```

#### Empresa 2

```bash
ip route add default via 100.20.0.2
```

#### Casa

```bash
ip route add default via 100.40.0.1
```

#### Internet 1

```bash
route add 100.40.0.0/24 via 100.30.0.2
```

#### Internet 2

```bash
ip route add default via 100.30.0.1
```

## Posibles indicidencias durante el enrutamiento

><i class="fas fa-triangle-exclamation" aria-hidden="true"></i> Las incidencias que se recogen a continuación pueden darse o no según la configuración de Openstack en cada caso.
{: .notice--primary}

Una vez realizada esta configuración, surgieron varias incidencias al comprobar el correcto funcionamiento del enrutamiento. La primera de ellas está relacionada con el direccionamiento de las máquinas. Aunque en el fichero de creación del escenario se desactivó la opción de usar el protocolo DHCP para asignar una dirección IP a las máquinas con la intención de otorgar las IP de forma manual para reutilizar el direccionamiento de la primera parte de esta práctica, al asignar a cada interfaz una IP manual diferente a la indicada por Openstack durante la creación del escenario, el resto de equipos no reconocen la nueva IP manual. Por tanto, las direcciones IP anteriormente indicadas se han tenido que modificar por las IP automáticas que la plataforma ha asignado a cada equipo durante la creación del escenario.

En segundo lugar, las instancias de Openstack no cuentan con el fichero de configuración /etc/network/interfaces, lo que dificulta la configuración de direccionamiento. Si se crea, el sistema lo ignora y no reconoce la información incluida en él a la hora de configurar las diferentes interfaces de red en el reinicio de las máquinas.

Finalmente, la incidencia más relevante que surge en este punto es la imposibilidad de establecer la comunicación entre las diferentes máquinas a pesar de haber corregido el enrutamiento y el direccionamiento IP. El origen de esta incidencia se encuentra en el propio fichero de creación del escenario. Para que las máquinas permitan el tráfico a través de sus puertos, se debe indicar en su configuración el rango de direcciones IP cuyo tráfico deben permitir o 0.0.0.0/0 para todas las IP. Esta información se debe incluir en el campo allowed_address_pairs cuando se definen las tarjetas de red de los routers.

La ausencia de este permiso en el fichero de creación del escenario impide que el tráfico pase a través de los puertos de sus routers. Aunque el origen del problema está en la creación del escenario, se puede solucionar posteriormente desde la interfaz de Openstack. Para ello se debe navegar hasta red > redes > red[n] > puertos > NombreDelPuerto > Pares de direcciones permitidas. Esta ventana incluye el botón “añadir pares de direcciones permitidas” desde el que se puede añadir una IP cuyo tráfico se debe permitir a través de ese puerto. En este caso, se indica la dirección 0.0.0.0/0 para que se permita el tráfico de todas las IP:

![](/assets/img/redes/practica13/img5.png)

Esta configuración se debe establecer para cada uno de los puertos que corresponden a una tarjeta de red de un router en el escenario.

# Configuración del servidor DHCP en Openstack

A diferencia del escenario en GNS3, en este caso para habilitar el protocolo DHCP en la red local formada por el router Casa y los PC 1 y 2 se debe configurar también la interfaz de Openstack si no se ha hecho en la plantilla de creación del escenario. Para ello se debe navegar a  la red en la que se quiere activar el protocolo DHCP (red > redes > red 7) y pulsar en “editar subred”. En la ventana emergente, se puede habilitar el protocolo DHCP para la red:

![](/assets/img/redes/practica13/img6.png)

Alternativamente, también se puede añadir esta configuración en el fichero de creación del escenario si se marca como verdadera la opción de habilitar el DHCP en la red indicada:

```yaml
  subnet7:
    type: OS::Neutron::Subnet
    properties:
      network: { get_resource: red7 }
      cidr: 192.168.0.0/24
      enable_dhcp: true
```

# Configuración del SNAT en el router Casa

Una forma de configurar SNAT en un router Linux como Casa en este escenario es usar iptables.

```bash
iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o ens3 -j SNAT –to 100.40.0.103
```

Con la ejecución de este comando se añade una nueva regla a la tabla POSTROUTING de iptables. Esta regla indica que todos los paquetes que lleguen al router desde la red 192.168.0.0/24 y se enruten para salir por la interfaz ens3 deben hacerlo con la dirección IP modificada a la 100.40.0.103 a través del protocolo SNAT.

De esta manera, desde los PC de la red de Casa ahora se puede establecer comunicación fuera de la red.

# Configuración del NAT en los routers de las empresas

## Configuración del SNAT en los routers de las empresas

En el caso de Empresa 1:

```bash
iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o ens4 -j SNAT --to 100.10.0.50
```

Este comando indica que se debe añadir a la tabla de iptables POSTROUTING una regla por la que todos los paquetes que provengan de la red 10.0.0.0/24 y se enruten por la interfaz ens4 deben ver su dirección IP modificada a la 100.10.0.50 a través de SNAT.

Una vez configurado el SNAT en el router Empresa 1, los servidores de su red pueden salir al resto de redes del escenario.

Para el router Empresa 2:

```bash
iptables -t nat -A POSTROUTING -s 10.0.0.0/24 -o ens4 -j SNAT --to 100.20.0.39
```

Se añade una regla a la tabla POSTROUTING de iptables que indica que se debe cambiar la IP de los paquetes de la red 10.0.0.0/24 que se enruten por la interfaz ens4 a la 100.20.0.39.

De la misma manera que ocurre con los equipos de las redes locales de Casa y Empresa 1, con esta configuración SNAT en el router Empresa 2, los servidores de la red pueden salir al resto del escenario.

## Configuración del DNAT en los routers de las empresas

El comando `iptables` también se puede usar para configurar el DNAT en los routers Linux.

En primer lugar, para dirigir el tráfico web que reciba el router Empresa 1 al servidor web hay que indicarle al router que reenvíe el tráfico de los puertos 80 y 443 a la IP correspondiente a la tarjeta de red del servidor web. Para dirigir el tráfico de PostgreSQL al servidor 2 se debe indicar su puerto de destino, el 5432, y su dirección IP en el comando.

```bash
iptables -t nat -A PREROUTING -p tcp --dport 80 -i ens4 -j DNAT --to 10.0.0.190
iptables -t nat -A PREROUTING -p tcp --dport 443 -i ens4 -j DNAT --to 10.0.0.190
iptables -t nat -A PREROUTING -p tcp --dport 5432 -i ens4 -j DNAT --to 10.0.0.211
```

Igualmente, para hacer llegar el SSH al servidor dedicado a este protocolo en la red de la Empresa 2 se debe configurar, a través de iptables, el DNAT en el router Empresa 2. Y, finalmente, el tráfico entre los clientes y el servidor de MariaDB también se debe dirigir en este router a la máquina adecuada.

```bash
iptables -t nat -A PREROUTING -p tcp --dport 22 -i ens4 -j DNAT --to 10.0.0.139
iptables -t nat -A PREROUTING -p tcp --dport 3306 -i ens4 -j DNAT --to 10.0.0.113
```

## Hacer el NAT persistente en routers Linux

Para que la configuración NAT sea persistente entre reinicios en routers Linux es necesario configurar un servicio que lance un pequeño script que ejecute las reglas de iptables cada vez que se inicie la máquina. Para ello, el primer paso es guardar las reglas que se han configurado en el router en un fichero con una redirección: `iptables-save > /etc/iptables/rules.v4` y, posteriormente, el contenido de este fichero se vuelca al script con el siguiente contenido:

```bash
#!/usr/bin/env bash
iptables-restore < /etc/iptables/rules.v4
```

Este script se guarda en el directorio /usr/local/bin con el nombre iptables.sh y se le otorgan permisos de ejecución. 

```bash
chmod +x /usr/local/bin/iptables.sh
```

Para que este script se ejecute en cada reinicio de la máquina, se debe crear el servicio en /etc/systemd/system/iptables.services y darle al fichero el siguiente contenido:

```
[Unit]
Description='Reglas de iptables'
After=systemd-sysctl.service

[Service]
Type=oneshot
ExecStart=/usr/local/bin/iptables.sh

[Install]
WantedBy=multi-user.target
```

Finalmente, se activa y se arranca el servicio.

```bash
systemctl enable iptables.service
systemctl start iptables.service
```

Para que el NAT funcione correctamente a través de los reinicios, este procedimiento para hacer la configuración persistente se debe repetir en los otros dos routers del escenario.

><i class="fas fa-info-circle" aria-hidden="true"></i> En este blog también puedes encontrar este mismo caso práactico resuelto usando [routers Cisco en GNS3](/redes/nat-router-cisco-gns3) y usando [routers Mikrotik en Openstack](/redes/nat-router-mikrotik-openstak).
{: .notice--primary}