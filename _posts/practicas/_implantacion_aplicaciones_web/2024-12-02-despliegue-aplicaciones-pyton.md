---
title:  "Despliegue de aplicaciones escritas en Python"
date:   2024-11-18 01:00:00 +0200
categories: implantacion
tag: [Migración de aplicaciones, entorno de producción, Apache2, Nginx, servidor web, LEMP, LAMP, flask, django, implantacion, Implantación de Aplicaciones Web]
---

# Configuración del equipo de desarrollo para desplegar una aplciación Python con Django

En primer lugar se crea un entorno virtual con las dependencias necesarias para que funcione el proyecto.

```
cd venv
python3 -m venv django
pip install ~/django_tutorial/requirements.txt
```

Para crear la base de datos con la estructura de datos definida en el proyecto django se ejecuta el comando `migrate`.

```
(django) debian@implantacion:~$ cd ~/django_tutorial
(django) debian@implantacion:~/django_tutorial$ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying polls.0001_initial... OK
  Applying sessions.0001_initial... OK
```

Para crear un usuario administrador se usa el comando `createsuperuser`.

```
(django) debian@implantacion:~/django_tutorial$ python3 manage.py createsuperuser
Username (leave blank to use 'debian'): admin
Email address: admin@poll.org
Password: 
Password (again): 
The password is too similar to the username.
This password is too short. It must contain at least 8 characters.
This password is too common.
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.
```

Al intentar acceder a la administración de la aplicación se produce el siguiente error:

```
DisallowedHost at /admin

Invalid HTTP_HOST header: '172.22.202.151:8080'. You may need to add '172.22.202.151' to ALLOWED_HOSTS.

Request Method: 	GET
Request URL: 	http://172.22.202.151:8080/admin
Django Version: 	4.2
Exception Type: 	DisallowedHost
Exception Value: 	

Invalid HTTP_HOST header: '172.22.202.151:8080'. You may need to add '172.22.202.151' to ALLOWED_HOSTS.

Exception Location: 	/home/debian/venv/django/lib/python3.11/site-packages/django/http/request.py, line 167, in get_host
Python Executable: 	/home/debian/venv/django/bin/python3
Python Version: 	3.11.2
Python Path: 	

['/home/debian/django_tutorial',
 '/usr/lib/python311.zip',
 '/usr/lib/python3.11',
 '/usr/lib/python3.11/lib-dynload',
 '/home/debian/venv/django/lib/python3.11/site-packages']

Server time: 	Mon, 25 Nov 2024 11:21:33 +0000
```

Para solucionarlo se incluye la IP en la lista de hosts permitidos del fichero django_tutorial/settings.py

```
ALLOWED_HOSTS = ["172.22.202.151"]
```

Para ejecutar el servidor web de desarrollo se usa el comando `runserver`.

```
python3 manage.py runserver 0.0.0.0:8080
```

# Configurar el servidor Apache2 para servir la página web

Para poder servir la página web en Apache2 hay que configurar el VirtualHost indicando el ServerName, DocumentRoot y también aportando la información necesaria para que funcione el módulo wsgi de Apache2.

```
	ServerName polls.javi.org

	ServerAdmin webmaster@localhost

	DocumentRoot /home/debian/django_tutorial

	WSGIDaemonProcess django_polls python-path=/home/debian/django_tutorial:/home/debian/venv/django/lib/python3.11/site-packages
	WSGIProcessGroup django_polls
	WSGIScriptAlias / /home/debian/django_tutorial/django_tutorial/wsgi.py process-group=django_polls
```

A continuación se usa el comando `collectstatic` para generar un directorio de ficheros estáticos en el directorio del proyecto con todos los ficheros estáticos. Para que este comando funcione, se debe indicar la URL base de los ficheros estáticos en el fichero settings.py

```
import os
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```

Y después se puede ejecutar el comando `collectstatic`

```
(django) debian@implantacion:~/django_tutorial$ python3 manage.py collectstatic

You have requested to collect static files at the destination
location as specified in your settings:

    /home/debian/django_tutorial/static

This will overwrite existing files!
Are you sure you want to do this?

Type 'yes' to continue, or 'no' to cancel: yes

128 static files copied to '/home/debian/django_tutorial/static'.
```

Finalmente se crea un alias en el VirtualHost que sirve el contenido del directorio estático cuando se accede a la URL_BASE/static.

```
	Alias /static/ /home/debian/django_tutorial/static/
	<Directory /home/debian/django_tutorial/static/>
		Require all granted
	</Directory>
```

Así, el contenido final del VirtualHost es el siguiente:

```
<VirtualHost *:80>
	ServerName polls.javi.org

	ServerAdmin webmaster@localhost

	DocumentRoot /home/debian/django_tutorial

	WSGIDaemonProcess django_polls python-path=/home/debian/django_tutorial:/home/debian/venv/django/lib/python3.11/site-packages
	WSGIProcessGroup django_polls
	WSGIScriptAlias / /home/debian/django_tutorial/django_tutorial/wsgi.py process-group=django_polls

	<Directory /home/debian/django_tutorial>
		Require all granted
	</Directory>

	Alias /static/ /home/debian/django_tutorial/static/
	<Directory /home/debian/django_tutorial/static/>
		Require all granted
	</Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
```

# Migración al entorno de producción

## Configuración del proyecto en django

Una vez que la aplciación está funcionando en el entorno local de desarrollo correctamente, se puede implantar en el entorno de producción, en este caso, un VPS. Para volcar el directorio del proyecto primero se sincroniza el repositorio en GitHub.

```
git add .
git commit -am "Modificaciones para servir la aplicación usando Apache2"
git push
```
En el VPS se clona el repositorio

```
git clone https://github.com/fjhuete/django_tutorial.git
```

A continuación se crea en el entorno de producción también un entorno virtual para trabajar con el framework django.

```
sudo apt install python3-venv
mkdir venv
cd venv/
python3 -m venv django
source django/bin/activate
```

Con el entorno virtual activado se instalan las dependencias necesarias para el funcionamiento de la aplicación.

```
(django) debian@pignite:~/venv$ cd ~/django_tutorial/
(django) debian@pignite:~/django_tutorial$ pip install -r requirements.txt 
```

En el entorno de producción la aplicación usa el SGBD MySQL en vez de sqlite. Para poder usarlo, en el entorno virtual tiene que estar instalado también el módulo para que python trabaje con mysql.

```
pip install mysqlclient
```

Durante el proceso de instalación de `mysqlclient` se produce un error en las dependencias. Para solucionarlo, hay que instalar algunos paquetes.

```
sudo apt-get install pkg-config python3-dev default-libmysqlclient-dev build-essential
```

En este caso, la instalación de `mysqlclient` sí es exitosa. A continuación, hay que crear una base de datos en MariaDB a la que pueda acceder la aplicación python.

```
MariaDB [(none)]> create database polls;
Query OK, 1 row affected (0,001 sec)

MariaDB [(none)]> create user 'polls'@'localhost' identified by 'polls';
Query OK, 0 rows affected (0,002 sec)

MariaDB [(none)]> grant all privileges on polls.* to 'polls'@'localhost' identified by 'polls';
Query OK, 0 rows affected (0,002 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0,002 sec)
```

Para que la aplicación pueda acceder a la base de datos hay que modificar la configuración del fichero settings.py del proyecto.

```
# Database
# https://docs.djangoproject.com/en/3.1/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
          'NAME': 'polls',
          'USER': 'polls',
          'PASSWORD': 'polls',
          'HOST': 'localhost',
          'PORT': '',
    }
}
```

Además, en el fichero settings se debe indicar el nombre de los hosts permitidos para el acceso a la aplicación, en este caso, se puede usar el nombre de la aplicación.

```
ALLOWED_HOSTS = ["python.javi.org"]
```

Para crear la estructura de tablas en la base de datos en el entorno de producción hay que ejecutar el comando `migrate` en el VPS.

```
(django) debian@pignite:~/django_tutorial$ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying auth.0010_alter_group_name_max_length... OK
  Applying auth.0011_update_proxy_permissions... OK
  Applying auth.0012_alter_user_first_name_max_length... OK
  Applying polls.0001_initial... OK
  Applying sessions.0001_initial... OK
```

Una vez creadas las tablas de la base de datos para rellenar el contenido hay que hacer una copia de seguridad en el entorno de desarrollo y volcarla al entorno de producción. En este caso se usan dos sistemas gestores de bases de datos diferentes pero se pueden usar los comandos `dumpdata` y `loaddata` de django para hacer este proceso de forma sencilla. 

Así, en el entorno de desarrollo se recopila el contenido de la base de datos, se vuelca a un fichero json y se copia al VPS

```
python3 manage.py dumpdata > polls.json
scp polls.json debian@pignite.javihuete.site:/home/debian/django_tutorial
```

En el VPS se puede volcar el contenido del fichero de respaldo json generado en el entorno de desarrollo a la base de datos MySQL que está funcionando en el entorno de producción.

```
(django) debian@pignite:~/django_tutorial$ python3 manage.py loaddata polls.json 
Installed 56 object(s) from 1 fixture(s)
```

## Configuración del servidor de aplciaciones uwsgi

Para servir aplicaciones python con el servidor de aplicaciones wsgi en Nginx el módulo `uwsgi` tiene que estar instalado en el entorno virtual.

```
(django) debian@pignite:~/django_tutorial$ pip install uwsgi
Collecting uwsgi
  Downloading uwsgi-2.0.28.tar.gz (816 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 816.2/816.2 kB 10.3 MB/s eta 0:00:00
  Preparing metadata (setup.py) ... done
Installing collected packages: uwsgi
  DEPRECATION: uwsgi is being installed using the legacy 'setup.py install' method, because it does not have a 'pyproject.toml' and the 'wheel' package is not installed. pip 23.1 will enforce this behaviour change. A possible replacement is to enable the '--use-pep517' option. Discussion can be found at https://github.com/pypa/pip/issues/8559
  Running setup.py install for uwsgi ... done
Successfully installed uwsgi-2.0.28
```

A continuación se configura el servidor de aplicaciones usando systemd. Primero se crea el fichero de configuración .ini para la aplicación. Este fichero está en el directorio del entorno virtual.

```
[uwsgi]
http = :8082
chdir = /home/debian/django_tutorial/django_tutorial
wsgi-file = wsgi.py
processes = 4
threads = 2
```

Y después la unidad de systemd que controla la aplicación. Este fichero está en el directorio /etc/systemd/system/uwsgi-polls.service.

```
[Unit]
Description=uwsgi-polls
After=network.target

[Install]
WantedBy=multi-user.target

[Service]
User=debian
Group=debian
Restart=always

ExecStart=/home/debian/venv/django/bin/uwsgi /home/debian/venv/django/polls.ini
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID

WorkingDirectory=/home/debian/django_tutorial
Environment=PYTHONPATH='/home/debian/django_tutorial:/home/debian/venv/django/lib/python3.11/site-packages'

PrivateTmp=true
```

Finalmente, se habilita y se arranca el servicio.

```
(django) debian@pignite:/etc/systemd/system$ cd
(django) debian@pignite:~$ sudo systemctl enable uwsgi-polls.service
Created symlink /etc/systemd/system/multi-user.target.wants/uwsgi-polls.service → /etc/systemd/system/uwsgi-polls.service.
(django) debian@pignite:~$ sudo systemctl start uwsgi-polls.service
(django) debian@pignite:~$ sudo systemctl status uwsgi-polls.service
● uwsgi-polls.service - uwsgi-polls
     Loaded: loaded (/etc/systemd/system/uwsgi-polls.service; enabled; preset: >
     Active: active (running) since Wed 2024-11-27 07:23:11 UTC; 6s ago
   Main PID: 520689 (uwsgi)
      Tasks: 9 (limit: 2291)
     Memory: 23.6M
        CPU: 88ms
     CGroup: /system.slice/uwsgi-polls.service
             ├─520689 /home/debian/venv/django/bin/uwsgi /home/debian/venv/djan>
             ├─520690 /home/debian/venv/django/bin/uwsgi /home/debian/venv/djan>
             ├─520691 /home/debian/venv/django/bin/uwsgi /home/debian/venv/djan>
             ├─520692 /home/debian/venv/django/bin/uwsgi /home/debian/venv/djan>
             └─520693 /home/debian/venv/django/bin/uwsgi /home/debian/venv/djan>
```

## Configuración de Nginx como proxy inverso

Para que Nginx dirija el tráfico al servidor de aplicaciones uwsgi se crea un VirtualHost con la siguiente configuración:

```
server {
    listen 443 ssl;
        
    server_name python.javihuete.site;
    
    ssl_certificate /etc/letsencrypt/live/javihuete.site/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/javihuete.site/privkey.pem;
    ssl_protocols       TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
    ssl_ciphers         HIGH:!aNULL:!MD5;

	root /home/debian/django_tutorial;

	location / {
		proxy_pass http://localhost:8082;
	}	

	location /static/ {
		alias /home/debian/django_tutorial/static/;
	}

	error_log /var/log/nginx/python_error.log;
	access_log /var/log/nginx/python_access.log;   

}
```

Para habilitarlo se crea un enlace simbólico en el directorio sites-enabled.

```
sudo ln -s ../sites-available/python 
```

El VirtualHost debe contener la directiva location que establezca un alias desde la ruta `/static/` a la ruta en la que se encuentra el contenido estático en el directorio django_tutorial.

```
	location /static/ {
		alias /home/debian/django_tutorial/static/;
	}
```

Para evitar que se muestre información sensible de la aplicación en caso de error, en el entorno de producción el valor `DEBUG` en el fichero settings.py se debe cambiar a `False`.

```
# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = False
```

# Modificaciones en la aplicación

## Modificación de la página principal

Para modificar el contenido de la página principal se añade una línea en el fichero index.html, que está en django_tutorial/polls/templates/polls/index.html.

En ese caso, se añade la línea 

```html
<h2>Práctica de Implantación de Javi Huete</h2>
```

Para desplegar este cambio en producción desde el entorno de desarrollo se actualiza el repositorio de la aplicación en GitHub.

```
git commit -am "Modificada la página inicial de la aplicación"
git push
```

Y se sincronizan los cambios en el repositorio del servidor en producción.

```
debian@pignite:~/django_tutorial$ git pull
Actualizando 16c8380..0d693ff
Fast-forward
 .gitignore                 | 1 +
 polls/templates/index.html | 2 +-
 3 files changed, 3 insertions(+), 1 deletion(-)
```

## Modificación de la imagen de fondo de la aplicación

La imagen de fondo está en la ruta django_tutorial/static/polls/images/background.jpg. Para modificarla se copia una nueva imagen a este directorio y se le pone ese mismo nombre.

Para llevar esta modificación al entorno de producción, como en el caso anterior, se actualiza el repositorio en GitHub.

```
 debian@implantacion ❯ git add .
 debian@implantacion ❯ git commit -am "Añadida una nueva imagen para el fondo de la aplicación"
[master 08ea1fe] Añadida una nueva imagen para el fondo de la aplicación
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 static/polls/images/background.jpg.old
 debian@implantacion ❯ git push
Enumerando objetos: 11, listo.
Contando objetos: 100% (11/11), listo.
Comprimiendo objetos: 100% (6/6), listo.
Escribiendo objetos: 100% (6/6), 3.31 KiB | 3.31 MiB/s, listo.
Total 6 (delta 1), reusados 0 (delta 0), pack-reusados 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:fjhuete/django_tutorial.git
   97a0a07..08ea1fe  master -> master
```

Y desde el VPS se sincroniza con el repositorio remoto.

```
(django) debian@pignite:~/django_tutorial$ git pull
remote: Enumerating objects: 11, done.
remote: Counting objects: 100% (11/11), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 6 (delta 1), reused 6 (delta 1), pack-reused 0 (from 0)
Desempaquetando objetos: 100% (6/6), 3.29 KiB | 1.65 MiB/s, listo.
Desde https://github.com/fjhuete/django_tutorial
   97a0a07..08ea1fe  master     -> origin/master
Actualizando 97a0a07..08ea1fe
Fast-forward
 static/polls/images/background.jpg     | Bin 46418 -> 2879 bytes
 static/polls/images/background.jpg.old | Bin 0 -> 46418 bytes
 2 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 static/polls/images/background.jpg.old
```

## Creación de una nueva tabla en la base de datos

Para crear una nueva tabla en la base de datos de la aplicación hay que modificar el modelo. Este modelo se encuentra definido en el fichero django_tutorial/polls/models.py.

Para añadir una nueva tabla a la base de datos hay que crear en el modelo una nueva clase.

```python
class Categoria(models.Model):  
    Abr = models.CharField(max_length=4)
    Nombre = models.CharField(max_length=50)

    def __str__(self):
        return self.Abr+" - "+self.Nombre
```

Para aplicar este cambio a la base de datos del entorno de desarrollo se debe crear una nueva migración y, después, aplicarla.

```
 debian@implantacion ❯ python3 manage.py makemigrations
Migrations for 'polls':
  polls/migrations/0002_categoria.py
    - Create model Categoria
 debian@implantacion ❯ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0002_categoria... OK
```

En este punto la base de datos debe contar ya con la nueva tabla pero para poder gestionarla desde el administrador gráfico de django es necesario seguir un par de pasos más para añadir el nuevo modelo a la página de administración. Primero en el fichero polls/admin.py hay que importar la nueva clase Categoria junto a las anteriores (Choice y Question).

```python
from .models import Choice, Question, Categoria
```

Y, al final de este fichero, tras a la línea `admin.site.register(Question, QuestionAdmin)` se añade la línea `admin.site.register(Categoria)`.

>Para que esta modificación sea efectiva en el entorno de produción (que usa el módulo wsgi de Apache2 para ejectuar el código python de la aplicación) el repositorio debe ser propiedad del usuario www-data para que la aplicación pueda acceder a la base de datos. En el caso del entorno de producción (que usa el servidor de aplicaciones uwsgi y un servidor web Nginx) el repositorio puede ser propoiedad de otro usuario.

De nuevo, para aplicar esta modificación a la aplicación en producción se actualiza el repositorio en GitHub.

```
 debian@implantacion ❯ git add .
 debian@implantacion ❯ git commit -am "Modificación para añadir una nueva tabla a la aplicación"
[master d7da468] Modificación para añadir una nueva tabla a la aplicación
 3 files changed, 30 insertions(+), 1 deletion(-)
 create mode 100644 polls/migrations/0002_categoria.py
  debian@implantacion ❯ git push
Enumerando objetos: 12, listo.
Contando objetos: 100% (12/12), listo.
Comprimiendo objetos: 100% (7/7), listo.
Escribiendo objetos: 100% (7/7), 1020 bytes | 1020.00 KiB/s, listo.
Total 7 (delta 4), reusados 0 (delta 0), pack-reusados 0
remote: Resolving deltas: 100% (4/4), completed with 4 local objects.
To github.com:fjhuete/django_tutorial.git
   08ea1fe..d7da468  master -> master
```

Y desde el servidor en producción se sincroniza el directorio de la aplicación con el nuevo contenido del repositorio.

```
(django) debian@pignite:~/django_tutorial$ git pull
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 7 (delta 4), reused 7 (delta 4), pack-reused 0 (from 0)
Desempaquetando objetos: 100% (7/7), 1000 bytes | 333.00 KiB/s, listo.
Desde https://github.com/fjhuete/django_tutorial
   08ea1fe..d7da468  master     -> origin/master
Actualizando 08ea1fe..d7da468
Fast-forward
 polls/admin.py                     |  3 ++-
 polls/migrations/0002_categoria.py | 21 +++++++++++++++++++++
 polls/models.py                    |  7 +++++++
 3 files changed, 30 insertions(+), 1 deletion(-)
 create mode 100644 polls/migrations/0002_categoria.py
```

En este caso, la modificación realizada en el entorno de desarrollo implica un cambio en la estructura de la base de datos. Para replicarlo en el servidor en producción hay que usar el comando `migrate` de django.

```
(django) debian@pignite:~/django_tutorial$ python3 manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, polls, sessions
Running migrations:
  Applying polls.0002_categoria... OK
```

Tras aplicar todos estos cambios se reinicia el servidor de aplicaciones uwsgi.

```
sudo systemctl restart uwsgi-polls.service
```

Y el cambio ya es también efectivo en el servidor en producción.