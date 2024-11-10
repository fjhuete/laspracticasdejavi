---
title:  "Configuración del protocolo HTTPS para el acceso a una aplicación web"
date:   2024-11-10 01:00:00 +0200
categories: implantacion
tag: [HTTPS, Apache2, Let's Encrypt, Certbot, Nginx, servidor web, Implantación de Aplicaciones Web]
excerpt: Para usar HTTPS en una aplicación web es necesario configurar el protocolo HTTPS en el servidor web en el que se aloja. En este post se recoge una breve guía con los pasos a seguir.
---

# Solicitud de los certificados con Let's Encrypt

Para solicitar un certificado se usa la herramienta `Certbot`.

```
sudo apt install certbot
```

Para solicitar un certificado de tipo comodín (para todos los virtual hosts de un dominio) se debe usar la opción `manual` del modo `certonly`.

```
sudo certbot certonly --manual -d *.javihuete.site
```

La salida de este comando indica un registro DNS de tipo TXT que se debe añadir en nuestro DNS. Tras verificar nuestra identidad a través de este método se recibe el certificado en el VPS.

Este tipo de certificación no habilita la renovación automática en el servidor web, así que antes de que expire el certificado se debe renovar de forma manual.

```
NEXT STEPS:
- This certificate will not be renewed automatically. Autorenewal of --manual certificates requires the use of an authentication hook script (--manual-auth-hook) but one was not provided. To renew this certificate, repeat this same certbot command before the certificate's expiry date.
```

# Configuración de HTTPS en Nginx

La configuración HTTPS de las aplicaciones desplegadas en el servidor web de Nginx se puede establecer para todos los virtual host en el fichero /etc/nginx/conf.d/default.conf o en cada uno de los ficheros de configuración de cada virtual host en /etc/nginx/sites-available.

En el fichero de configuración se sustituyen las líneas que indicaban que el virtual host escucha por el puerto 80 por estas líneas que hacen que escuche por el puerto 443 y habilita así la conexión https.

```
server {
        listen 443 ssl;
        server_name www.javihuete.site;
        ssl_certificate /etc/letsencrypt/live/javihuete.site/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/javihuete.site/privkey.pem;
        ssl_protocols       TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_ciphers         HIGH:!aNULL:!MD5;
...
}
```

Para evitar errores en el acceso por HTTP se puede añadir una redirección desde el puerto 80 al 443 en el mismo virtualhost o en otro dedicado exclusivamente a esta redirección

```
server {
    listen 80;
    server_name  www.javihuete.site;
    server_tokens off;
    return 301 https://$server_name$request_uri;
}
```

# Solución de errores

Si en la configuración de la aplicación web se especifica la URL base de la aplicación, puede ser necesario cambiar el valor de este parámetro para que la aplicación use, por defecto, https en vez de http.

Por ejemplo, en el caso de Moodle, en el fichero /var/www/moodle/config.php hay que hacer este tipo de modificación.

```
$CFG->wwwroot   = 'https://www.javihuete.site';
$CFG->dataroot  = '/var/www/moodledata';
```