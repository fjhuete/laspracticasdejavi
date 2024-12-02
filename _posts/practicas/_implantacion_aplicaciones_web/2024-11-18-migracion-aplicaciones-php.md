---
title:  "Migración de aplicaciones web PHP al entorno de producción"
date:   2024-11-18 01:00:00 +0200
categories: implantacion
tag: [Migración de aplicaciones, entorno de producción, Apache2, Nginx, servidor web, LEMP, LAMP, implantacion, Implantación de Aplicaciones Web]
---

Este supuesto parte de un servidor Apache2 en un entorno de pruebas con dos aplicaciones PHP instaladas: Moodle y NextCloud. En este post se document la migración de estas aplicaciones a un servidor Nginx en producción.

# Instalación del servidor LEMP y de las dependencias de los CMS:

En primer lugar se tienen que instalar en el VPS los paquetes necesarios para configurar el servidor LEMP: `nginx`, `mariadb-server` y `php`.

Además, se instalan otros paquetes que corresponden a las dependencias necesarias para poder instalar en el servidor los CMS de Moodle y NextCloud.

```
sudo apt install php php-curl php-cli php-mysql php-gd php-gmp libmagickcore-dev php-redis php-memcached php-common php-xml php-json php-intl php-pear php-dev php-common php-mbstring php-zip php-soap php-bz2 php-bcmath php-imagick nginx mariadb-server -y
```

# Creación de los hosts virtuales en el VPS

A continuación, se crean los hosts virtuales en el servidor web. En nginx esta configuración se guarda en un fichero con el nombre del virtual host en el directorio /etc/nginx/sites-available. En este fichero se indica el nombre del servidor para cada virtual host, el document root pero también es importante incluir en la lista de documentos de index que el servidor debe buscar el documento index.php que usan los CMS que se están instalando en este caso.

El siguiente documento corresponde al virtual host de Moodle:

```
debian@pignite:/etc/nginx/sites-available$ cat moodle 
# Virtual Host configuration for www.javihuete.site
#
# You can move that to a different file under sites-available/ and symlink that
# to sites-enabled/ to enable it.
#
server {
	listen 80;
	listen [::]:80;

	server_name www.javihuete.site;

	root /var/www/moodle;
	index index.php index.html index.htm index.nginx-debian.html;

	location / {
		try_files $uri $uri/ =404;
	}
	# pass PHP scripts to FastCGI server
	#
	location ~ \.php$ {
	#	include snippets/fastcgi-php.conf;
	#
		# With php-fpm (or other unix sockets):
		fastcgi_pass unix:/run/php/php8.2-fpm.sock;
	#	# With php-cgi (or other tcp sockets):
	#	fastcgi_pass 127.0.0.1:9000;
	}
}
```

Y este documento configura el virtual host de NextCloud:

```
debian@pignite:/etc/nginx/sites-available$ cat nextcloud 
# Virtual Host configuration for cloud.javihuete.site
#
# You can move that to a different file under sites-available/ and symlink that
# to sites-enabled/ to enable it.
#
server {
	listen 80;
	listen [::]:80;

	server_name cloud.javihuete.site;

	root /var/www/nextcloud;
	index index.php index.html index.htm index.nginx-debian.html;

	location / {
		try_files $uri $uri/ =404;
	}
        # pass PHP scripts to FastCGI server
        #
        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
        #
                # With php-fpm (or other unix sockets):
                fastcgi_pass unix:/run/php/php8.2-fpm.sock;
        #       # With php-cgi (or other tcp sockets):
        #       fastcgi_pass 127.0.0.1:9000;
        }
}
```

En nginx no existe un comando que ponga los virtual host en producción como el comando @a2ensite@ de apache. Para hacerlo, en este caso hay que crear un enlace simbólico al fichero de configuración en el directorio /etc/nginx/sites-enabled de forma manual.

```
debian@pignite:/etc/nginx/sites-enabled$ sudo ln ../sites-available/moodle 
debian@pignite:/etc/nginx/sites-enabled$ sudo ln ../sites-available/nextcloud
```

# Compresión de los ficheros de los document root, copia al VPS y descompresión de los ficheros y creación del document root

Para trasladar los contenidos del document root de cada uno de los CMS al VPS, es necesario comprimir los directorios en el servidor de pruebas y copiar el archivo comprimido al VPS.

```
debian@apache2:/var/www$ zip -r moodle moodle
debian@apache2:/var/www$ zip -r nextcloud nextcloud
debian@apache2:/var/www$ scp moodle.zip debian@pignite.javihuete.site:/home/debian
debian@apache2:/var/www$ scp nextcloud.zip debian@pignite.javihuete.site:/home/debian
```

Después, el contenido del archivo comprimido se extrae y se copia en el document root correspondiente del VPS.

```
debian@pignite:~$ sudo cp moodle.zip /var/www/
debian@pignite:/var/www$ sudo unzip moodle.zip
debian@pignite:~$ sudo cp nextcloud.zip /var/www/
debian@pignite:/var/www$ sudo unzip nextcloud.zip 
```

# Creación de la base de datos en el VPS

Para que cada CMS pueda acceder a su base de datos correspondiente, se crea una base de datos para cada uno con su correspondiente usuario y contraseña.

```
MariaDB [(none)]> create database moodle
    -> ;
Query OK, 1 row affected (0,001 sec)

MariaDB [(none)]> create user 'moodle'@'localhost' identified by 'contraseña';
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> grant all privileges on moodle.* to 'moodle'@'localhost' identified by 'contraseña';
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> create database nextcloud;
Query OK, 1 row affected (0,001 sec)

MariaDB [(none)]> create user 'nextcloud'@'localhost' identified by 'contraseña'; 
Query OK, 0 rows affected (0,002 sec)

MariaDB [(none)]> grant all privileges on nextcloud.* to 'nextcloud'@'localhost' identified by 'contraseña';
Query OK, 0 rows affected (0,001 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0,001 sec)
```

# Realización del backup de la base de datos y copia al VPS

Para dar contenido a la base de datos creada previamente se crea un copia de la base de datos que está funcionando en el servidor de pruebas y se copia el fichero de respaldo al servidor VPS.

```
debian@mysql:~$ mysqldump -u moodle -p moodle > moodle.sql
debian@mysql:~$ scp moodle.sql debian@pignite.javihuete.site:/home/debian
moodle.sql                                   100% 1685KB   3.8MB/s   00:00    
debian@mysql:~$ mysqldump -u cloud -p nextcloud > nextcloud.sql
Enter password: 
debian@mysql:~$ scp nextcloud.sql debian@pignite.javihuete.site:/home/debian
nextcloud.sql                                100% 3455KB   4.5MB/s   00:00
```

En el VPS se rellenan las tablas de cada una de las bases de datos a partir del contenido del fichero de respaldo generado en el servidor local.

```
debian@pignite:~$ mysql -u moodle -p moodle < moodle.sql 
Enter password: 
debian@pignite:~$ mysql -u nextcloud -p nextcloud < nextcloud.sql 
Enter password: 
```

# Modificación de las credenciales en el fichero de configuración del CMS

Para que los CMS puedan acceder a la base de datos hay que modificar los ficheros de configuración en los que se hace referencia a la ubicación de la base de datos a la que debe acceder el CMS. En el servidor local, la base de datos estaba en un servidor diferente al servidor web y, por tanto, este dato era la dirección IP del servidor de base de datos. Al pasar al VPS, la base de datos y el servidor web están en el mismo equipo y, por tanto, este campo del fichero de configuración tiene que apuntar al localhost. En estos ficheros de configuración también es necesario indicar correctamente el dominio del VPS, que es diferente al que se ha usado previamente en el servidor local de pruebas.

En Moodle el fichero de configuración está en /var/www/moodle/config.php.

```
debian@pignite:/var/www$ sudo nano moodle/config.php 

<?php  // Moodle configuration file

unset($CFG);
global $CFG;
$CFG = new stdClass();

$CFG->dbtype    = 'mariadb';
$CFG->dblibrary = 'native';
$CFG->dbhost    = 'localhost';
$CFG->dbname    = 'moodle';
$CFG->dbuser    = 'moodle';
$CFG->dbpass    = 'contraseña';
$CFG->prefix    = 'mdl_';
$CFG->dboptions = array (
  'dbpersist' => 0,
  'dbport' => 3306,
  'dbsocket' => '',
  'dbcollation' => 'utf8mb4_general_ci',
);

$CFG->wwwroot   = 'http://www.javihuete.site';
$CFG->dataroot  = '/var/www/moodledata';
$CFG->admin     = 'admin';

$CFG->directorypermissions = 0777;

require_once(__DIR__ . '/lib/setup.php');
```

Y en NextCloud el fichero de configuración está en /var/www/nextcloud/config/config.php

```
debian@pignite:/var/www$ sudo nano nextcloud/config/config.php 
<?php
$CONFIG = array (
  'instanceid' => 'editado',
  'passwordsalt' => 'editado',
  'secret' => 'editado',
  'trusted_domains' => 
  array (
    0 => 'cloud.javihuete.site',
  ),
  'datadirectory' => '/var/www/nextcloud/data',
  'dbtype' => 'mysql',
  'version' => '30.0.1.2',
  'overwrite.cli.url' => 'http://cloud.javihuete.site',
  'dbname' => 'nextcloud',
  'dbhost' => 'localhost',
  'dbport' => '',
  'dbtableprefix' => 'oc_',
  'mysql.utf8mb4' => true,
  'dbuser' => 'cloud',
  'dbpassword' => 'contraseña',
  'installed' => true,
);
```

En el caso de Moodle, también es necesario configurar el fichero 
/etc/php/8.2/fpm/php.ini para añadir la siguiente línea:

```
; How many GET/POST/COOKIE input variables may be accepted
max_input_vars = 5000
```

# Registrar las nuevas entradas del DNS

Finalmente, para que estos servicios sean accesibles desde el exterior, hay que añadir dos registros de tipo CNAME en el DNS que apunten a la máquina en la que están instalados los servidores.

# Resolución de problemas

En el caso de la instalación de Moodle al no contar con el módulo php de Apache se produce un problema al cargar el CSS de la aplicación.
Esto se debe a que Moodle usa rutas amigables y nginx no está configurado por defecto para interpretar este tipo de url que, en el caso de Moodle, contienen ficheros .php en una parte intermedia de la ruta. Para solucionar el error hay que configurar la conexión con el servidor de aplicaciones en el virtual host de moodle de la siguiente forma:

```
    location ~ [^/]\.php(/|$) {
        include fastcgi_params;
        fastcgi_split_path_info ^(.+\.php)(/.*)$;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock; # Ajusta la versión de PHP si es diferente
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
```

La línea `fastcgi_split_path_info ^(.+\.php)(/.*)$;` es la que permite que Nginx interprete estas rutas que incluyen una referencia a un fichero .php en mitad de la URL y no sólo al final.

Por su parte, en NextCloud se deben solucionar algunos errores como, por ejemplo, desactivar la opción "output_buffering" del fichero php.ini, agragar en el fichero de configuración del virtual host la configuración que permite a Nginx interpretar las rutas /.well-known o incluir también en este fichero las líneas que permiten al Nginx servir ficheros .mjs usando MIME.

Además, NextCloud también verifica la integridad de todos los ficheros que conforman la aplicación. De esta manera, si durante la copia desde el servidor local al VPS se ha dejado de copiar algún fichero oculto como el .htaccess o el .user.ini o se ha añadido un nuevo fichero no verificado como el info.php, la aplicación muestra un error. Para resolverlo sólo hay que descargar la misma versión de NextCloud que esté instalada, buscar los ficheros que faltan y colocarlos en el directorio correspondiente. Si algún fichero no está firmado, también se tendrá que eliminar para evitar que se muestre este error.