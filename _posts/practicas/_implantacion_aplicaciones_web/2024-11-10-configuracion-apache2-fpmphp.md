---
title:  "Configuración de Apache2 con fpm-php"
date:   2024-11-10 01:00:00 +0200
categories: implantacion
tag: [PHP, Apache2, fpm-php, LAMP, implantacion, Implantación de Aplicaciones Web]
excerpt: En este post se documenta el proceso de configuración de un servidor Apache2 para servir aplicaciones web dinámicas escritas en PHP usando el servidor de aplicaciones fpm-php.
---

Primero se comprueba si el módulo PHP de Apache2 está instalado y se desinstala.

```
apache2ctl -M | grep php
a2dismod php8.2
```

Para instalar fpm-php

```
apt install php8.2-fpm php8.2
```

Para que Apache2 use fpm-php se activan los módulos `proxy_fcgi` y `setenvif`

```
a2enmod proxy_fcgi setenvif
```

Se puede configurar el servidor de aplicaciones en todos el host virtual en /etc/apache2/sites-available/<host>.conf 

```
#Por TCP
    ProxyPassMatch ^/(.*\.php)$ fcgi://127.0.0.1:9000/var/www/<host>/$1

#Por socket UNIX
    ProxyPassMatch ^/(.*\.php)$ unix:/run/php/php8.2-fpm.sock|fcgi://127.0.0.1/var/www/<host>
```

O para todos los hosts del servidor web en el fichero /etc/apache2/conf-available

```
#Por TCP
    SetHandler "proxy:fcgi://127.0.0.1:9000"
    
#Por socket UNIX
    SetHandler "proxy:unix:/run/php/php8.2-fpm.sock|fcgi://localhost"
```
