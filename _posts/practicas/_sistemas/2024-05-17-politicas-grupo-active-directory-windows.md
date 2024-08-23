---
title:  "Creación de política de grupo en Windows Active Directory"
date:   2024-05-17 01:00:00 +0200
categories: sistemas
tag: [sistemas, comandos, PowerShell, Windows, Active Directory, Directorio Activo, políticas de grupo, GPO, Implantación de Sistemas Operativos]
---
Una directiva de grupo es un conjunto de reglas que controlan el entorno de trabajo de cuentas de usuario y cuentas de equipo. La directiva de grupo permite gestionar y configurar de forma centralizada sistemas operativos, aplicaciones y configuración de los usuarios en un entorno de Directorio Activo.

# Política de despliegue de software

*Supuesto*: Una empresa quiere implantar un entorno apto para el teletrabajo y todos los usuarios necesitan contar con un software que les permita realizar videoconferencias. La empresa ha contratado una licencia de Zoom y toda la plantilla debe contar en sus equipos con un cliente de escritorio de Zoom Meetings.

En primer lugar, desde la web del proveedor se descarga el fichero MSI para la instalación del cliente de escritorio de Zoom. Posteriormente, el fichero de instalación se almacena en una carpeta compartida con todos los equipos del dominio.

Para instalar el software en el próximo inicio de sesión de cada usuario del dominio se crea una política de grupo en el dominio y, en ella, se especifica un paquete de instalación de software.

![](/assets/img/sistemas/practica14/img1.png)

Posteriormente se indica en las propiedades del paquete que instale la aplicación durante el inicio de sesión.

![](/assets/img/sistemas/practica14/img2.png)

De esta manera queda configurada esta directiva de grupo en el controlador de dominio y se aplicará a todos los usuarios del dominio que inicien sesión.

![](/assets/img/sistemas/practica14/img3.png)

Por último, antes de iniciar sesión en el cliente, se fuerza la actualización de las directivas de grupo.

```
gpupdate /force
```

Al iniciar sesión, cada usuario ya tendrá instalado el cliente de escritorio de Zoom Meetings en su equipo.

# Política de configuración del sistema

*Supuesto*: La plantilla de la empresa tiene dificultades para acceder al recurso compartido en el que se almacenan todos los datos con los que deben trabajar. Para solucionar el problema se creará una directiva de grupo que genere un acceso directo en el escritorio de ese volumen compartido.

En este caso no es necesario descargar ningún instalador. Los primeros pasos son similares a los del caso anterior: desde el controlador de dominio se accede al editor de directivas de grupo y se genera una nueva directiva. En este caso, desde la carpeta de preferencias se elige la opción de accesos directos y se crea un nuevo. Se la plica la configuración necesaria, se indica el nombre, ruta de destino y se le asigna un icono:

![](/assets/img/sistemas/practica14/img4.png)

Nuevamente, se fuerza la actualización de la directiva y, al iniciar sesión, el usuario contará con un acceso directo al recurso compartido en su escritorio.

![](/assets/img/sistemas/practica14/img5.png)
