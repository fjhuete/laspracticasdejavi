---
title:  "Guía básica de configuración de Nginx"
date:   2024-11-24 01:00:00 +0200
categories: servicios
tag: [Servidor web, Nginx, Configuración básica, PHP, Administración de Sistemas, servicios, Servicios de Red e Internet]
excerpt: En este post se recoge una guía básica del uso del servidor web Nginx a partir de un supuesto práctico.
---

# Configuración de un servidor web nginx + PHP con dos VirtualHost

## VirtualHost de la página 1

```
vagrant@servidorweb:~$ cat /etc/nginx/sites-available/pagina1.conf 
# Default server configuration
#
server {
	listen 80;
	listen [::]:80;

	root /srv/www/pagina1;

	# Add index.php to the list if you are using PHP
	index index.php index.html index.htm index.nginx-debian.html;

	server_name www.pagina1.org;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

	#pass PHP scripts to FastCGI server
	#
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
	
		# With php-fpm (or other unix sockets):
		fastcgi_pass unix:/run/php/php8.2-fpm.sock;
		# With php-cgi (or other tcp sockets):
		#fastcgi_pass 127.0.0.1:9000;
	}

	# deny access to .htaccess files, if Apache's document root
	# concurs with nginx's one
	#
	#location ~ /\.ht {
	#	deny all;
	#}
}
```

## VirtualHost de la página 2

```
vagrant@servidorweb:~$ cat /etc/nginx/sites-available/pagina2.conf 
# Default server configuration
#
server {
	listen 80;
	listen [::]:80;

	root /srv/www/pagina2;

	# Add index.php to the list if you are using PHP
	index index.php index.html index.htm index.nginx-debian.html;

	server_name www.pagina2.org;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

	#pass PHP scripts to FastCGI server
	#
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
	
		# With php-fpm (or other unix sockets):
		fastcgi_pass unix:/run/php/php8.2-fpm.sock;
		# With php-cgi (or other tcp sockets):
		#fastcgi_pass 127.0.0.1:9000;
	}

	# deny access to .htaccess files, if Apache's document root
	# concurs with nginx's one
	#
	#location ~ /\.ht {
	#	deny all;
	#}
}
```

## Creación de enlaces simbólicos en sites-enabled

```
vagrant@servidorweb:~$ ls -l /etc/nginx/sites-enabled/
lrwxrwxrwx 1 root root 39 Nov 21 08:52 pagina1.conf -> /etc/nginx/sites-available/pagina1.conf
lrwxrwxrwx 1 root root 39 Nov 21 08:52 pagina2.conf -> /etc/nginx/sites-available/pagina2.conf
```

# Redirección de www.pagina1.org a www.pagina1.org/principal

Se añade la lína `rewrite ^/$ $uri/principal permanent;` al location de la raíz de la web.

```
location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                #try_files $uri $uri/ =404;
                rewrite ^/$ $uri/principal permanent;
        }
```

# La página www.pagina1.org/principal/documentos muestra los documentos del directorio /srv/doc

Primero se crea el directorio y sus documentos:

```
sudo mkdir -p /srv/doc
sudo touch /srv/doc/fich{A..F}.txt
```
Después se crea un alias en el VirtualHost que muestre los ficheros del directorio al acceder a la URL configurada.

```
location /principal/documentos/ {
        alias /srv/doc;
        autoindex on;
        allow all;
        }
```

# Limitar el acceso a www.pagina1.org/secreto con autenticación básica

Primero se crea un fichero de usuarios y contraseñas

```
sudo htpasswd -c /etc/nginx/.htpasswd usuario
```

Crear el directorio y el fichero

```
sudo mkdir -p /srv/www/pagina1/secreto
sudo nano /srv/www/pagina1/secreto/index.html
```

Configurar la autenticación en el VirtualHost

```
location /secreto {
    auth_basic           "Esto es un secreto";
    auth_basic_user_file /etc/nginx/.htpasswd;
}
```