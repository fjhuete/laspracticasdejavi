---
title:  "Instalación de un CMS Python usando Django"
date:   2024-12-03 01:00:00 +0200
categories: implantacion
tag: [Python, django, Framework, Nginx, servidor web, LEMP, CMS, implantacion, Implantación de Aplicaciones Web]
---
En este post se instala el CMS CMS-Django usando un entorno virtual.

Para ello, en primer lugar, se crea un entorno virtual en el que se instala la aplicación.

```
cd venv/
python3 -m venv cms
source cms/bin/activate
pip install django-cms
```

Para crear un proyecto se usa el comando `djangocms`.

```
❯ djangocms javiblog

Clone template using django-admin
django-admin startproject "javiblog" --template https://github.com/django-cms/cms-template/archive/4.1.tar.gz
cd "/home/debian/javiblog"

Install requirements in /home/debian/javiblog/requirements.in
python -m pip install -r "/home/debian/javiblog/requirements.in"

Run migrations
python -m manage migrate

Create superuser
python -m manage createsuperuser
Username (leave blank to use 'debian'): admin
Email address: admin@javiblog.org
Password: 
Password (again): 
The password is too similar to the username.
This password is too short. It must contain at least 8 characters.
This password is too common.
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.

Check installation
python -m manage cms check

***************************************
django CMS 4.1.4 installed successfully
***************************************

Congrat
```

Para probar la instalación se puede ejecutar un servidor de pruebas con `python3 manage.py runserver 0.0.0.0:8080`.

# Configuración del servidor Apache2

Para poder servir el CMS en Apache2 hay que configurar el VirtualHost indicando el ServerName, DocumentRoot y también aportando la información necesaria para que funcione el módulo wsgi de Apache2.

```
<VirtualHost *:80>
	ServerName portal.javi.org

	ServerAdmin webmaster@localhost

	DocumentRoot /home/debian/javiblog

	WSGIDaemonProcess javiblog python-path=/home/debian/javiblog:/home/debian/venv/cms/lib/python3.11/site-packages
	WSGIProcessGroup javiblog
	WSGIScriptAlias / /home/debian/javiblog/javiblog/wsgi.py process-group=javiblog

	<Directory /home/debian/javiblog>
		Require all granted
	</Directory>

	Alias /static/ /home/debian/javiblog/static/
	<Directory /home/debian/javiblog/static/>
		Require all granted
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Después se activa el VirtualHost.

```
❯ sudo a2ensite djangocms.conf 
Enabling site djangocms.
To activate the new configuration, you need to run:
  systemctl reload apache2
❯ sudo systemctl reload apache2.service 
```

A continuación se usa el comando `collectstatic` para generar un directorio de ficheros estáticos en el directorio del proyecto con todos los ficheros estáticos. Para que este comando funcione, se debe indicar la URL base de los ficheros estáticos en el fichero settings.py

```
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

Y después se puede ejecutar el comando `collectstatic`.

```
❯ python3 manage.py collectstatic
Found another file with the destination path 'admin/img/search.svg'. It will be ignored since only the first encountered file is collected. If this is not what you want, make sure every static file has a unique path.

1159 static files copied to '/home/debian/javiblog/static'.
```

Accediendo al servidor se puede usar el CMS instalado.

# Migración del CMS

## Volcado del proyecto

En este caso, la aplicación django no está en un repositorio de GitHub, sino que se ha instalado localmente en el entorno de desarrollo. Para llevar el CMS al entorno de producción se puede crear un repositorio en GitHub y clonarlo o, directamente, sucar scp para copiar el directorio en el servidor en producción.

Antes de llevar el directorio al entorno de producción conviene crear todos los ficheros necesarios como el fichero de respaldo del contenido de la base de datos.

```
❯ python3 manage.py dumpdata > cms.json
```

Para copiar el directorio al entorno de producción se puede usar el comando `scp`.

```
❯ scp -r javiblog/ debian@pignite.javihuete.site:/home/debian
```

En el entorno de desarrollo también se crea un entorno virtual y se instala el CMS y sus dependencias.

```
cd venv/
python3 -m venv cms
source venv/cms/bin/activate
cd
pip install django-cms
cd javiblog
pip install -r requirements.in
pip install mysqlclient
```

Para poder hacer la migración se crea la base de datos para la aplicación.

```
MariaDB [(none)]> create database cms;
Query OK, 1 row affected (0,002 sec)

MariaDB [(none)]> create user 'cms'@'localhost' identified by '*****';
Query OK, 0 rows affected (0,002 sec)

MariaDB [(none)]> grant all privileges on cms.* to 'cms'@'localhost' identified by '*****';
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0,001 sec)
```

Y en el fichero settings.py del proyecto se deben indicar los datos de la nueva base de datos.

```
# Database
# https://docs.djangoproject.com/en/5.1/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'cms',
        'USER': 'cms',
        'PASSWORD': 'djangocms',
        'HOST': 'localhost',
        'PORT': '',
    }
}
```

Finalmente, se puede migrar la estructura de la base de datos.

```
python3 manage.py migrate
```
Y, por último, el contenido.

```
python3 manage.py loaddata cms.json 
```

Antes de terminar con la configuración de la aplicación en el entorno de producción hay que permitir el acceso a través de la URL en el fichero settings.py.

```
ALLOWED_HOSTS = ["portal.javihuete.site"]
```

## Configuración del servidor de aplicaciones

Para poder usar el servidor de aplicaciones `uwsgi` desde el entorno virtual del CMS hay que instalarlo dentro de este entorno virtual.

```
pip install uwsgi
```

Para configurar el servidor de aplciaciones, en primer lugar, se genera el fichero .ini en el directorio del entorno virtual.

```
[uwsgi]
http = :8083
chdir = /home/debian/javiblog/javiblog
wsgi-file = wsgi.py
processes = 4
threads = 2
```

A partir de este fichero se puede configurar la unidad de systemd que controla este servicio en el directorio /etc/systemd/system/uwsgi-cms.

```
[Unit]
Description=uwsgi-cms  
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
User=debian
Group=debian
Restart=always

ExecStart=/home/debian/venv/cms/bin/uwsgi /home/debian/venv/cms/cms.ini
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

WorkingDirectory=/home/debian/javiblog            
Environment=PYTHONPATH='/home/debian/javiblog:/home/debian/venv/cms/lib/python3.11/s>

PrivateTmp=true
```

Para habilitar y arrancar el servicio se usa `systemctl`.

```
sudo systemctl enable uswgi-cms.service
sudo systemctl start uswgi-cms.service
```

## Configuración de Nginx como proxy inverso

Para que Nginx dirija el tráfico al servidor de aplicaciones uwsgi se crea un VirtualHost con la siguiente configuración:

```
server {
	#listen 80;
	#server_name  python.javihuete.site;
	#server_tokens off;
	#return 301 https://$server_name$request_uri;

        listen 443 ssl;
        
	server_name portal.javihuete.site;
        
	ssl_certificate /etc/letsencrypt/live/javihuete.site/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/javihuete.site/privkey.pem;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_ciphers         HIGH:!aNULL:!MD5;

	root /home/debian/javiblog;

	location / {
		proxy_pass http://localhost:8083;
		#include proxy_params;
	}	

	location /static/ {
		alias /home/debian/javiblog/static/;
	}

	error_log /var/log/nginx/cms_error.log;
	access_log /var/log/nginx/cms_access.log;   

}
```

Por último, para habilitar el sitio se crea el enlace simbólico y se reinicia el servidor web.

```
sudo ln -s ../sites-available/cms
sudo systemctl restart nginx.service
```

Al poner en producción el CMS, se usa el protocolo HTTPS para el acceso a la página web. Esto hace que sea necesario añadir la siguiente línea al fichero settings.py de la aplicación:

```
CSRF_TRUSTED_ORIGINS = ["https://portal.javihuete.site",]
```