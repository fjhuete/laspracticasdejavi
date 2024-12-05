---
title:  "Configuración de un proxy inverso en Apache2"
date:   2024-11-12 01:00:00 +0200
categories: servicios
tag: [Proxy, Proxy inverso, Apache2, Administración de Sistemas, servicios, Servicios de Red e Internet]
excerpt: A través de un caso práctica se muestran diferentes ejemplos de configuración de un proxy inverso usando Apache2.
---

# Configuración de un proxy inverso en Apache2

En este ejemplo práctico se muestra la configuración del proxy para el acceso a dos páginas (www.app1.org y www.app2.org) para que las redirecciones funcionen, de manera que al acceder a www.app1.org/directorio se redirija a www.app1.org/nuevodirectorio.

Para permitir el acceso a las páginas de la red interna se tienen que activar los módulos necesarios de Apache

```
sudo a2enmod proxy proxy_http
```

Después se crea un virtualhost en el directorio /etc/apache2/sites-available para cada página en el que se indica la línea de proxy inverso para que se produzca el acceso al servidor interno.

```                           
<VirtualHost *:80>
        ServerName www.app1.org

        ServerAdmin webmaster@localhost

        ProxyPass "/" "http://interno.example1.org/"
        ProxyPassReverse "/" "http://interno.example1.org/"

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
```

Después hay que activar los virtual host

```
sudo a2ensite app1
sudo a2ensite app2
sudo systemctl reload apache2
```

Desde el cliente se crea el direccionamiento estático en el fichero /etc/hosts y se puede acceder al destino desde el navegador del host.