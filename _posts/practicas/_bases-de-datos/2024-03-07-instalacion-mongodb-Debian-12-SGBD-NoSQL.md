---
title:  "Guía de instalación de MongoDB en Debian 12"
date:   2024-03-07 01:00:00 +0200
categories: bases-de-datos
tag: [Gestión de Bases de Datos, debian, linux, MongoDB, NoSQL, SGBD]
---
MongoDB es un sistema de base de datos NoSQL, orientado a documentos y de código abierto. Este SGBD no guarda los documentos y tablas como ocurre con los relacionales (Oracle, PostgreSQL, MariaDB, MySQL...), sino que lo hace en estrucuturas de datos en formato BSON llamadas documentos. En este post te cuento cómo puedes instalar este sistema gestor de base de datos en un servidor con Debian 12.

# Instalación de MongoDB

Para instalar MongoDB, en primer lugar, se deben instalar los paquetes pnupg y curl.

```bash
sudo apt-get install gnupg curl
```

A continuación se debe importar la clave pública de Mongo.

```bash
curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor
```

Posteriormente se debe crear un fichero de fuentes en el directorio sorurces.list.d:

```bash
echo "deb [ signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] http://repo.mongodb.org/apt/debian bookworm/mongodb-org/7.0 main" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
```

A partir de este punto, para instalar MongoDB se puede seguir el procedimiento habitual: actualizar apt e instalar el paquete:

```bash
sudo apt update
sudo apt install mongodb-org
```

Se puede comprobar porque tras finalizar el proceso de instalación se han creado los directorios /var/lib/mongodb y /var/log/mongodb.

```bash
ls -l /var/lib/ | grep mongodb
ls -l /var/log/ | grep mongodb
```

# Configuración de MongoDB

Antes de comenzar a usar MongoDB es necesario arrancar el servidor y comprobar su estado:

```bash
sudo systemctl start mongodb
sudo systemctl status mongodb
```

![](/assets/img/bbdd/img1.png)

Además, se debe habilitar el servicio para que su funcionamiento sea persistente durante los reinicios de la máquina:

```bash
sudo systemctl enable mongodb
```

# Acceder al servidor de MongoDB

Finalmente, se puede acceder al servicio de MongoDB instalado en la máquina local ejecutando el comando `mongosh`:

![](/assets/img/bbdd/img2.png)

><i class="fas fa-warning" aria-hidden="true"></i> MongoDB usa el puerto 27017. Si tu servidor se aloja en una máquina remota deberás revisar que el cortafuegos permita conexiones por este puerto.
{: .notice--warning}