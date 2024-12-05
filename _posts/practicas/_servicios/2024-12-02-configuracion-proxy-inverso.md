---
title:  "Configuración de un proxy inverso en Apache2 y Nginx"
date:   2024-11-16 01:00:00 +0200
categories: servicios
tag: [Proxy, Proxy inverso, Apache2, Nginx, Administración de Sistemas, servicios, Servicios de Red e Internet]
excerpt: En este post se muestran diferentes ejemplos de configuración de los servidores web Apache2 y Nginx como proxy inverso.
---

# Configuración de un proxy inverso

En un escenario formado por dos máquinas una de ellas funciona a modo de proxy inverso y la otra como servidor web. En el servidor se crean dos virtual host diferentes con un nombre diferenciado y document root también separados.

# Uso de Apache2 como proxy inverso

Existen diferentes formas de implementar un proxy inverso. Una de ellas es usar un servidor web como Apache2. Para poder usar el servidor web de Apache como proxy inverso hay que activar los módulos `proxy` y `proxy_http`.

```
a2enmod proxy proxy_http
```

Para acceder a las dos páginas diferentes del servidor web se crean en el proxy dos virtual host en los que se comenta la línea del document root y se añade la línea de proxy:

```
ProxyPass "/" "http://interno.example1.org/"
```

Para que las redirecciones de las web alojadas en el servidor web funcionan correctamente, el document root tiene que incluir también la línea `ProxyPassReverse`.

```
ProxyPassReverse "/" "http://interno.example1.org/"
```

Para acceder a las páginas alojadas en el servidor web bajo un único dominio como, por ejemplo, www.servidor.org/app1 y www.servidor.org/app2 el proxy se debe configurar en un único virtualhost en el que se define el dominio y dos entradas de proxy, una para cada una de las páginas.

```
ProxyPass "/app1/" "http://interno.example1.org/"
ProxyPassReverse "/app1/" "http://interno.example1.org/"

ProxyPass "/app2/" "http://interno.example2.org/"
ProxyPassReverse "/app2/" "http://interno.example2.org/"
```

# Uso de Nginx como proxy inverso

Al igual que Apache2, el servidor web Nginx también puede funcionar como proxy inverso. En este caso, para acceder a las dos páginas del servidor web desde dos dominios diferentes, la máquina que funciona como proxy inverso debe contar con un virtual hosta para cada una de ellas. En este virtual host debe estar presente la directiva de proxy.

```
location / {
    proxy_pass http://interno.example1.org/;
}
```

><i class="fas fa-quote-right" aria-hidden="true"></i> La directiva `proxy_pass` en Nginx ya hace que las redirecciones desde las páginas del servidor web funcionen correctamente y no necesita la configuración de ninguna directiva adicional.
{: .notice--primary}

Como en el caso anterior, para acceder a las páginas alojadas en el servidor web bajo un único dominio el proxy se debe configurar también en un único virtualhost en el que se define el dominio y dos entradas de proxy, una para cada una de las páginas, en este caso, usando la directiva `location`.

```
location /app1/ {
    proxy_pass http://interno.example1.org/;
    include proxy_params;
}
location /app2/ {
    proxy_pass http://interno.example2.org/;
    include proxy_params;
}
```