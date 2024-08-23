---
title:  "Cómo importar dispositivos a GNS3"
date:   2024-01-28 01:00:00 +0200
categories: redes
tag: [redes, switch, router, GNS3, cisco, tinycore linux, Planificación y Administración de Redes]
excerpt: 
---
En muchas ocasiones es habitual necesitar importar dispositivos que no están incluidos en la instalación básica de GNS3 para simular redes similares a las que podemos encontrar en la vida real. En este tutorial hay un par de ejemplos en los que se importan las imágenes de diferentes dispositivos en el simulador.

# Importar un router con módulo switch Cisco C3725 a GNS3

Para importar un dispositivo a GNS3 se puede acceder, en primer lugar a la ventana de configuración siguiendo Edit -> Preferences -> IOS Routers y seleccionar el botón “new” para importar un nuevo dispositivo.

![](/assets/img/redes/practica9.1/img1.png)

A continuación, se puede elegir entre usar una imagen nueva o una previamente importada. Si no se ha indicado con anterioridad la ruta a la imagen, se debe hacer en este momento.

![](/assets/img/redes/practica9.1/img2.png)

Posteriormente, se debe indicar que se está importando un router de tipo Etherswitch (según la documentación de GNS3 esto es importante). Marcar esta casilla cambia automáticamente el nombre del dispositivo a Etherswitch router y lo coloca en la categoría de dispostivos swithces en vez de routers.

![](/assets/img/redes/practica9.1/img3.png)

La documentación de GNS3 también indica que en la siguiente ventana se debe aumentar la cantidad de memoria RAM predeterminada a 256 MiB. 

![](/assets/img/redes/practica9.1/img4.png)

En la ranura 0 aparece el adaptador Dual FastEthernet GT96100-FE y en la ranura 1 está preinstalado el módulo NM-16ESW. La documentación de GNS3 indica que no se debe reemplazar el adaptador GT96100-FE por un módulo switch porque el dispositivo no funcionaría. En esta configuración se puede añadir un segundo módulo NM-16ESW, un adaptador NM-1FE-TX single FastEthernet o un adaptador NM-4T serial port a la ranura 2.

![](/assets/img/redes/practica9.1/img5.png)

Tras pulsar siguiente, se pueden añadir módulos WIC en la siguiente página. Pueden ser adaptadores WIC-1T o WIC-2T serial port. La documentación de GNS3 dice que no es mala idea añadir alguno aunque indica que no es necesario hacerlo sin especificar cuál es el criterio que se debe seguir para tomar una u otra decisión.

![](/assets/img/redes/practica9.1/img6.png)

A continuación se debe indiciar un valor Idle-PC para el dispositivo. No se hace, podría consumir el 100% del tiempo de proceso del core de la CPU al ejecutarse, mientras que con un valor Idle-PC definido, esto no ocurre.

El valor Idle-PC puede aparecer automáticamente en la ventana de configuración al cambiar de página. Si no es así, se debe seleccionar el botón “Idle-PC finder” para buscar uno. Esto puede llevar varios segundos, dependiendo de la velocidad del ordenador.

![](/assets/img/redes/practica9.1/img7.png)

Tras finalizar el asistente, el dispositivo ya debe aparecer en la pestaña de plantillas IOS Router de la ventana preferencias.

![](/assets/img/redes/practica9.1/img8.png)

En este punto, la documentación de GNS3 indica que se debe comprobar que el dispositivo tiene, al menos, 1MB para el disco PCMCIA disk0, como es el caso.

Un aspecto que se debe tener en cuenta de este dispositivo, tal y como indica la documentación de GNS3, es que, al añadirlo a una topología en el espacio de trabajo de la aplicación, los primeros dos puertos FastEthernet (fa0/0 y fa0/1) corresponden al módulo GT96100-FE y son únicamente puertos de router. Esta es una características de diseño y, por tanto, estos puertos no se pueden usar como swtiches. Con el adaptador NM-16ESW instalado en la ranura 1, tal y como aparece por defecto, los puertos de switch corresponden a las interfaces desde el fa1/0 al fa1/15.

# Importar una máquinta Tinycore Linux con Firefox

Para instalar estos dispositivos se puede acceder a la barra de herramientas de “end devices” y, desde ahí, seleccionar “new template”.

![](/assets/img/redes/practica9.1/img9.png)

Hay varias opciones de instalación disponibles. En este caso, se instala desde el marketplace de GNS3.

![](/assets/img/redes/practica9.1/img10.png)

En la siguiente ventana se puede buscar el dispositivo que se quiere instalar.

![](/assets/img/redes/practica9.1/img11.png)

Se debe elegir a continuación si instalarlo en un servidor remoto, en GNS3VM o en local.

![](/assets/img/redes/practica9.1/img12.png)

A continuación se debe indicar el binario qemu que debe usar el dispositivo, en este caso, el que se indica por defecto.

Si el dispositivo no se ha instalado previamente, habrá que pulsar el botón “download” para descargar el fichero de instalación. En caso contrario, se puede proceder a instalar el dispositivo. Si se cuenta con una imagen pero no se ha importado a GNS3, también está la opción de importar el fichero de instalación en este paso.

![](/assets/img/redes/practica9.1/img13.png)

Por último, finaliza el asistente de instalación del dispositivo y se muestra un mensaje de éxito.

Tras cerrar el asistente, el nuevo dispositivo ya está disponible en la pestaña de dispositivos finales de la barra de dispositivos de GNS3.

![](/assets/img/redes/practica9.1/img14.png)