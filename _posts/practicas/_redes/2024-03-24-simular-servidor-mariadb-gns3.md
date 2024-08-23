---
title:  "Cómo simular un servidor MariaDB en GNS3"
date:   2024-03-24 01:00:00 +0200
categories: redes
tag: [GNS3, redes, Planificación y Administración de Redes, servidores, mariadb, bases de datos]
excerpt: MariaDB es uno conocido y reputado servidor de bases de datos. Es similar a MySQL y permite almacenar y gestionar una gran cantidad de información. En este post se explica cómo simularlo en un escenario de GNS3.
toc: false
---
Para instalar el servidor de MariaDB hay que instalar el paquete mariadb-server después de haber conectado la máquina a la nube NAT, haber solicitado una configuración al servidor DHCP y haber actualizado los paquetes del sistema.

```bash
sudo dhclient
sudo apt update && sudo apt upgrade
sudo apt install maradb-server
```

Para configurar el acceso remoto en MariaDB sólo hay que comentar la línea que hace referencia a bind-addres en el fichero de configuración 50-server.cnf:

![](/assets/img/redes/practica13/img4.png)

Además, en MariaDB se debe indicar en el proceso de creación del usuario que éste se podrá conectar de forma remota indicando la IP pública desde la que se establecerá la conexión o el carácter % para indicar cualquier IP:

```sql
create user 'usuario'@'%' identified by 'usaurio';
```

><i class="fas fa-exclamation-triangle" aria-hidden="true"></i> Aunque en una simulación en un entorno cerrado no es peligroso, permitir el acceso a cualquier IP supone un problema de seguridad para cualquier servidor de bases de datos en producción y, por tanto, se debe evitar esta práctica en la vida real.
{: .notice--warning}

Para conectarse de forma remota al servidor se usa el siguiente comando desde el equipo cliente:

```bash
mysql -u usuario -p -h IP_servidor
```