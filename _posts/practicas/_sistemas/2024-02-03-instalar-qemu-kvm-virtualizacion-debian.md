---
title:  "Cómo instalar qemu/kvm en Debian 12 para virtualizar equipos"
date:   2024-02-03 01:00:00 +0200
categories: sistemas
toc: false
tag: [sistemas, debian, linux, comandos, qemu, virtualizacion, virtmanager, kvm, Implantación de Sistemas Operativos]
---
Qemu es un potente virtualizador que permite el uso de máquinas virtuales en equipos que usen sistemas operativos basados en Debian y otras distribuciones GNU/Linux. Este software se complementa a la perfección con VirtManager, que ofrece una interfaz gráfica amigable para realizar todas las tareas relacionadas con la virtualización.

Antes de comenzar con la instalación de estos paquetes es necesario saber si el equipo tiene capacidad para virtualizar. Con `lshw` se comprueba que el ordenador ofrece esta posibilidad. Para ello se busca la bandera “vmx” o “smv”, que indica si el microprocesador tiene activada esta opción: 

```bash
lshw | egrep -o ‘vmx|smv’
```

Además, con `cpu-checker` se puede comprobar si el ordenador tiene capacidad para virtualizar con kvm con el comando `kvm-ok`.

Posteriormente se instalan los paquetes necesarios.

```bash
sudo apt update
sudo apt install qemu-system libvirt-clients libvirt-daemon-system bridge-utils virt-manager
```

Para comprobar si la instalación ha generado los grupos kvm y libvirt se consulta el fichero de configuración /etc/group.

Con a`dduser ‘nombre de usuario’ ‘nombre de grupo’` se añade el usuario a los grupos kvm y libvirt.

```bash
adduser usuario kvm
adduser usuario libvirt
```

Se usa el comando `systemctl` con la opción `status` y el argumento `libvirtd` para comprobar si el servicio está funcionando. Con las opciones `start` o `enable` se arranca o activa el arranque automático del servicio.

```bash
sudo systemctl status libvirtd
sudo systemctl enable libvirtd
sudo systemctl start libvirtd
```
