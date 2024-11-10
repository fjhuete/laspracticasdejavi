---
title:  "Funcionamiento básico de ansible"
date:   2024-11-10 01:00:00 +0200
categories: servicios
tag: [Ansible, Dev-Ops, Infraestructura como código, Orquestación, Administración de Sistemas, Servicios de Red e Internet]
excerpt: Ansible es un software que permite la configuración automatizada de equipos a través de la ejecución de reglas. En esta entrada se recogen algunos elementos fundamentales de esta herramienta.
---

# Instalación de Ansible

Ansible se puede instalar desde los repositorios de Debian

```
sudo apt install ansible
```

# Preparación de la máquina

En este ejemplo se usa una máquina virtual con un usuario sin privilegios que puede acceder por ssh y que puede usar sudo sin indicar la contraseña.

# Ficheros de configuración

## Inventario

Incluye una declaración de los grupos y máquinas que se van a configurar y algunas variables para identificarlas.

```yaml
all:
  children:
    servidores:
      hosts:
        ansible: 
          ansible_ssh_host: 172.22.200.46
          ansible_ssh_user: debian
          ansible_ssh_private_key_file: /home/javi/.ssh/id_rsa
```

## Fichero de configuración

En él se indica la ruta al fichero de inventario.

```yaml
[defaults]
inventory = hosts
host_key_checking = False
```
El parámetro `host_key_checking = False`

## Probar conexión

Para probar la conexión a una máquina Ansible usa el módulo ping.

```bash
ansible -m ping all
ansible | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

## Actualizar el playbook

El playbook es el fichero que contiene las reglas que ejecuta ansible en la máquina objetivo.

En este caso, Ansible actualiza el sistema de paquetes, instala git y apache2, copia un fichero a la máquina remota, copia una plantilla al directorio /var/www/html.

```yaml
- hosts: all
  become: true
  tasks:
      # Actualizamos paquetes
    - name: Actualizamos el sistema
      apt: update_cache=yes upgrade=yes
      # Instalar paquetes
    - name: "Instalar paquetes con apt"
      ansible.builtin.apt: 
       pkg:
        - apache2
        - git

      # Copia un fichero a la máquina remota
    - name: "Copiar fichero a la máquina remota"
      copy:
        src: files/foo.conf
        dest: /etc/
        owner: root
        group: root
        mode: '0644'

      # Copia un template a un fichero
    - name: "Copiar un tamplate a un fichero de la máquina remota"
      template: 
        src: template/index.j2
        dest: /var/www/html/index.html
        owner: www-data
        group: www-data
        mode: 0644
```

# Ejecutar el playbook

```
ansible-playbook site.yaml
```