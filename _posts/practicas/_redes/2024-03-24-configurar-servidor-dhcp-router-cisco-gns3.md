---
title:  "Cómo configurar un servidor DHCP en un router Cisco"
date:   2024-03-24 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, servidores, DHCP, router, Cisco]
excerpt: La mayor parte de routers pueden funcionar también como servidores DHCP para los equipos de su red. En este post se explica cómo configurar un servidor DHCP en un router Cisco.
toc: false
---
Para configurar un servidor DHCP en un router Cisco se debe indicar si hay alguna dirección que se debe excluir del servidor en primer lugar y, posteriormente, se indica la red de direcciones que puede usar el servidor DHCP para otorgar IP a los clientes y la dirección por defecto del router.

```
R1#conf t
R1(config)#ip dhcp pool NOMBRE
R1(dhcp-config)#ip dhcp excluded-address 192.168.0.1
R1(dhcp-config)#network 192.168.0.0 255.255.255.0
R1(dhcp-config)#default-router 192.168.0.1
R1(dhcp-config)#end
R1#wr
```

