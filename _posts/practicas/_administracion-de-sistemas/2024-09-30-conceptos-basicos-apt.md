---
title:  "Conceptos de gestión de paquetería en Debian"
date:   2024-09-30 01:00:00 +0200
categories: administracion-sistemas
tag: [sistemas, debian, linux, apt, dpkg, paquetes, comandos, Administración de Sistemas Operativos]
excerpt: En este post se recoge un breve resumen sobre la información relativa a los gestores de paquetes apt y aptitude incluida en el manual de referencia de Debian
---

# Trabajo con apt, aptitude, dpkg

## Qué acciones consigo al realizar apt update y apt upgrade

El comando `apt update` revisa los repositorios del sistema operativo en busca de nuevas versiones de los paquetes instalados en el equipo. Posteriormente, el comando `apt upgrade` ejecuta la actualización de aquellos paquetes de los que haya una versión más reciente disponible.

Antes de instalar un nuevo paquete o de actualizar a su versión más reciente, el gestor de paquetes apt consulta el fichero Releases del repositorio para comprobar que el hash corresponde al del propio paquete y garantizar así que no ha sido modificado desde que se distribuye por parte del mantenedor hasta que llega al equipo del usuario.

## Lista la relación de paquetes que pueden ser actualizados. ¿Qué información puedes sacar a tenor de lo mostrado en el listado?

Los paquetes que pueden ser actualizados se consultan con el comando `apt list --upgradable`. La salida de este comando devuelve el nombre del paquete junto a otra información relevante como la rama del SO a la que pertenece el paquete, la versión, la fecha de actualización, etc.

## Indica la versión instalada, candidata así como la prioridad del paquete openssh-client

La versión instalada de `openssh-client` en mi equipo es la 1:9.2p1-2+deb12u3 y la candidata es la misma versión, lo que quiere decir que el paquete está actualizado en el equipo.
La prioridad de este paquete es de 500 desde los repositorios de debian, es decir, el gestor de paquetería instala el paquete si no hay una versión disponible en la rama principal o si no hay una versión más reciente instalada.

```
❯ apt policy openssh-client
openssh-client:
  Instalados: 1:9.2p1-2+deb12u3
  Candidato:  1:9.2p1-2+deb12u3
  Tabla de versión:
 *** 1:9.2p1-2+deb12u3 500
        500 http://deb.debian.org/debian bookworm/main amd64 Packages
        100 /var/lib/dpkg/status
```

## ¿Cómo puedes sacar información de un paquete oficial instalado o que no este instalado?

Para obtener información de un paquete instalado en el equipo la fuente más completa suele ser el manual, al que se puede acceder con el comando man seguido del nombre del paquete. Si el paquete no está instalado en el equipo aún se puede consultar información acerca de él a través de comandos como `apt show`, que muestra el nombre del paquete, la versión más reciente, la prioridad, la sección a la que pertenece, el nombre y contacto del mantenedor, el tamaño que ocupa una vez instalado, así como sus dependencias, paquetes recomendados y sugeridos. Además, también muestra información sobre la fuente del repositorio desde la que apt descarga el paquete y una pequeña descripción de lo que hace la herramienta.

## Saca toda la información que puedas del paquete openssh-client que tienes actualmente instalado en tu máquina

De forma resumida, se puede acceder a una información básica muy útil sobre el paquete `openssh-client` con el comando `apt-show openssh-client`:

```
❯ apt show openssh-client
Package: openssh-client
Version: 1:9.2p1-2+deb12u3
Priority: standard
Section: net
Source: openssh
Maintainer: Debian OpenSSH Maintainers <debian-ssh@lists.debian.org>
Installed-Size: 5.919 kB
Provides: ssh-client
Depends: adduser, passwd, libc6 (>= 2.36), libedit2 (>= 2.11-20080614-0), libfido2-1 (>= 1.8.0), libgssapi-krb5-2 (>= 1.17), libselinux1 (>= 3.1~), libssl3 (>= 3.0.13), zlib1g (>= 1:1.1.4)
Recommends: xauth
Suggests: keychain, libpam-ssh, monkeysphere, ssh-askpass
Conflicts: sftp
Breaks: openssh-sk-helper
Replaces: openssh-sk-helper, ssh, ssh-krb5
Homepage: https://www.openssh.com/
Tag: implemented-in::c, interface::commandline, interface::shell,
 network::client, protocol::sftp, protocol::ssh, role::program,
 security::authentication, security::cryptography, uitoolkit::ncurses,
 use::login, use::transmission, works-with::file
Download-Size: 991 kB
APT-Manual-Installed: yes
APT-Sources: http://deb.debian.org/debian bookworm/main amd64 Packages
Description: Cliente del protocolo "Secure Shell" (SSH) para acceso seguro a máquinas remotas
 Esta es la versión adaptable de OpenSSH, una implementación libre del
 protocolo «Secure Shell» como especifica el grupo de trabajo secsh del IETF.
 .
 Ssh (Secure Shell) es una aplicación para acceder y ejecutar comandos 
 en una máquina remota. Provee conexiones encriptadas y seguras entre dos
 huéspedes no certificados en una red sin seguridad. Las conexiones a través
 de X11 y puertos arbitrarios TCP/IP también pueden ser transportados a un
 canal seguro. Puede ser usado para proveer a las aplicaciones de un canal de
 comunicación seguro.
 .
 Este paquete proporciona los clientes de ssh, scp y sftp, los programas
 sh-agent y ssh-add para hacer más comoda la autenticación de clave pública,
 y las utilidades ssh-keygen, ssh-keyscan, ssh-copy-id y ssh-argv0.
 .
 En algunos países puede ser ilegal utilizar cualquier tipo de cifrado sin
 un permiso especial.
 .
 ssh reemplaza a las aplicaciones no seguras rsh, rcp y rlogin, 
 obsoletas para la mayoría de los propósitos.
```

La salida de este comando devuelve información como el nombre del paquete (openssh-client), la versión más reciente (1:9.2p1-2+deb12u3), la prioridad para la instalación (standard), la sección del SO a la que pertenece (red), la fuente (openssh), el mantenedor (el equipo de mantenedores de OpenSSH de Debian), el tamaño del paquete instalado (5.919 kB), la herramienta que ofrece (ssh-client), los paquetes de los que depende (adduser, passwd, libc6 (>= 2.36), libedit2 (>= 2.11-20080614-0), libfido2-1 (>= 1.8.0), libgssapi-krb5-2 (>= 1.17), libselinux1 (>= 3.1~), libssl3 (>= 3.0.13), zlib1g (>= 1:1.1.4)), los paquetes cuya instalación recomienda (xauth), los paquetes cuya instalación sugiere (keychain, libpam-ssh, monkeysphere, ssh-askpass), los paquetes con los que pueden generarse conflictos (sftp), los paquetes que rompe (openssh-sk-helper) y los paquetes que reemplaza (openssh-sk-helper, ssh, ssh-krb5), así como la web del proyecto (https://www.openssh.com/), algunas etiquetas para identificar el paquete, el tamaño de la descarga (991 kB), si el manual está instalado (en este caso, sí), la fuente del repositorio (http://deb.debian.org/debian bookworm/main amd64 Packages) y la descripción del paquete.

Se puede acceder a una información mucho más extensa de la herramienta a través del comando man ssh.

Además, parte de la información recogida en la salida del comando apt show se puede acceder de otras formas. Por ejemplo, para conseguir la información sobre los paquetes de los que depende `openssh-client` se puede ejecutar el comando `apt depends openssh-client`.

```
❯ apt depends openssh-client
openssh-client
  Depende: adduser
  Depende: passwd
  Depende: libc6 (>= 2.36)
  Depende: libedit2 (>= 2.11-20080614-0)
  Depende: libfido2-1 (>= 1.8.0)
  Depende: libgssapi-krb5-2 (>= 1.17)
  Depende: libselinux1 (>= 3.1~)
  Depende: libssl3 (>= 3.0.13)
  Depende: zlib1g (>= 1:1.1.4)
  Entra en conflicto: <sftp>
  Rompe: <openssh-sk-helper>
  Recomienda: xauth
  Sugiere: keychain
  Sugiere: <libpam-ssh>
  Sugiere: <monkeysphere>
  Sugiere: ssh-askpass
    ksshaskpass
    kwalletcli
    lxqt-openssh-askpass
    ssh-askpass-fullscreen
    ssh-askpass-gnome
  Reemplaza: <openssh-sk-helper>
  Reemplaza: ssh
  Reemplaza: <ssh-krb5>
```

Una información a la que no se puede acceder a través de apt show es la de las dependencias inversas del paquete. En este caso, con el comando `apt rdepends openssh-client` se puede obtener esta lista de paquetes que dependen del propio `openssh-client`.

```
❯ apt rdepends openssh-client
openssh-client
Reverse Depends:
  Reemplaza: openssh-server (<< 1:7.9p1-8)
  Depende: arb
 |Depende: zssh
  Recomienda: xpra
  Depende: xen-tools
  Depende: x2goserver
  Depende: x2goclient
 |Recomienda: waypipe
  Recomienda: vorta
  Depende: vagrant
  Recomienda: unison-2.52-gtk
  Recomienda: unison-2.52
  Recomienda: unison-2.51+4.13.1-gtk
  Recomienda: unison-2.51+4.13.1
  Recomienda: tsung
  Depende: python3-tomahawk
  Sugiere: tla
  Recomienda: task-ssh-server
 |Depende: taktuk
  Recomienda: python3-jarabe
  Depende: ssvnc
 |Depende: sshuttle
  Depende: sshfs
  Depende: ssh-tools
  Depende: ssh-import-id
  Depende: ssh-cron
  Depende: ssh-contact-client
  Depende: ssh-agent-filter
  Depende: snapd
  Sugiere: smokeping
  Sugiere: slack
 |Recomienda: sisu
  Recomienda: simple-tpm-pk11
  Depende: sidedoor
  Depende: secpanel
  Recomienda: seahorse
  Depende: python3-sahara-plugin-vanilla
  Depende: python3-sahara-plugin-spark
  Sugiere: rsync
 |Recomienda: rsnapshot
  Recomienda: rsbackup-graph
  Recomienda: rsbackup
 |Depende: rancid
  Depende: pssh
 |Depende: pkg-perl-tools
  Depende: piuparts-slave-from-git-deps
  Depende: piuparts-slave
  Recomienda: pdudaemon
 |Depende: pdsh
  Depende: python3-parallax
  Depende: oz
  Recomienda: ostree-push
  Depende: openstack-cluster-installer-openstack-ci
  Recomienda: openssh-known-hosts
 |Depende: ssh-askpass-gnome
  Depende: ssh (>= 1:9.2p1-2+deb12u3)
  Depende: openssh-tests (= 1:9.2p1-2+deb12u3)
  Depende: openssh-sftp-server (= 1:9.2p1-2+deb12u3)
  Depende: openssh-server (= 1:9.2p1-2+deb12u3)
  Depende: git-annex (>= 1:5.6p1)
 |Depende: openmpi-bin
  Sugiere: oar-user
  Depende: oar-server
  Depende: oar-node
  Depende: python3-nova
  Depende: network-manager-ssh
  Depende: mussh
  Depende: mssh
  Depende: mosh
  Recomienda: mercurial
 |Recomienda: lxsession
  Sugiere: lsh-server
  Depende: live-task-standard
  Recomienda: live-task-recommended
  Depende: libnetapp-perl
 |Depende: libnet-ssh-perl
 |Depende: libnet-sftp-foreign-perl
 |Depende: libnet-scp-perl
  Depende: libnet-openssh-perl
  Depende: libguestfs0
  Mejora: libconfig-model-openssh-perl
  Recomienda: lftp
  Recomienda: lava-server
  Depende: lava
 |Depende: lam-runtime
  Recomienda: python3-labgrid
  Recomienda: kwalletcli
  Depende: ksshaskpass
 |Depende: keychain
  Recomienda: hollywood
  Depende: hash-slinger
  Depende: gitolite3
  Rompe: git (<< 1:6.8)
 |Depende: ansible
  Depende: ganeti-3.0
 |Sugiere: gabedit
 |Sugiere: gabedit
 |Depende: fssync
  Recomienda: freedombox
  Recomienda: fence-agents
  Recomienda: fdroidserver
  Recomienda: fai-server
  Sugiere: duply
 |Recomienda: dupload
  Sugiere: dropbear-bin
  Recomienda: python3-dput
  Sugiere: dput
 |Recomienda: dish
  Recomienda: diffoscope-minimal
  Recomienda: diffoscope
  Recomienda: debvm
  Sugiere: debian-goodies
  Recomienda: cvs
  Depende: cockpit-tests
  Depende: clusterssh
  Sugiere: clonezilla
 |Recomienda: ckermit
  Mejora: chkrootkit
  Depende: ceph-mgr-cephadm
  Recomienda: btrbk
  Recomienda: barman-cli
  Recomienda: barman
 |Recomienda: backuppc
  Sugiere: backup-manager
  Depende: backintime-common
  Mejora: autossh
 |Depende: autossh
  Depende: apt-dater
 |Depende: ansible-core
```

Finalmente, cabe destacar que con el comando `apt-rdepends openssh-client` se puede obtener información recursiva sobre las dependencias del paquete, es decir, se puede conocer de qué paquetes dependen aquellos paquetes de los que, a su vez, depende `openssh-client`.

```
❯ apt-rdepends openssh-client
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
openssh-client
  Depends: adduser
  Depends: libc6 (>= 2.36)
  Depends: libedit2 (>= 2.11-20080614-0)
  Depends: libfido2-1 (>= 1.8.0)
  Depends: libgssapi-krb5-2 (>= 1.17)
  Depends: libselinux1 (>= 3.1~)
  Depends: libssl3 (>= 3.0.13)
  Depends: passwd
  Depends: zlib1g (>= 1:1.1.4)
adduser
  Depends: passwd
passwd
  Depends: libaudit1 (>= 1:2.2.1)
  Depends: libc6 (>= 2.36)
  Depends: libcrypt1 (>= 1:4.1.0)
  Depends: libpam-modules
  Depends: libpam0g (>= 0.99.7.1)
  Depends: libselinux1 (>= 3.1~)
  Depends: libsemanage2 (>= 2.0.32)
libaudit1
  Depends: libaudit-common (>= 1:3.0.9-1)
  Depends: libc6 (>= 2.33)
  Depends: libcap-ng0 (>= 0.7.9)
libaudit-common
libc6
  Depends: libgcc-s1
libgcc-s1
  Depends: gcc-12-base (= 12.2.0-14)
  Depends: libc6 (>= 2.35)
gcc-12-base
libcap-ng0
  Depends: libc6 (>= 2.33)
libcrypt1
  Depends: libc6 (>= 2.36)
libpam-modules
  PreDepends: debconf (>= 0.5)
  PreDepends: debconf-2.0
  PreDepends: libaudit1 (>= 1:2.2.1)
  PreDepends: libc6 (>= 2.34)
  PreDepends: libcrypt1 (>= 1:4.3.0)
  PreDepends: libdb5.3
  PreDepends: libpam-modules-bin (= 1.5.2-6+deb12u1)
  PreDepends: libpam0g (>= 1.4.1)
  PreDepends: libselinux1 (>= 3.1~)
debconf
debconf-2.0
libdb5.3
  Depends: libc6 (>= 2.34)
libpam-modules-bin
  Depends: libaudit1 (>= 1:2.2.1)
  Depends: libc6 (>= 2.34)
  Depends: libcrypt1 (>= 1:4.3.0)
  Depends: libpam0g (>= 0.99.7.1)
  Depends: libselinux1 (>= 3.1~)
libpam0g
  Depends: debconf (>= 0.5)
  Depends: debconf-2.0
  Depends: libaudit1 (>= 1:2.2.1)
  Depends: libc6 (>= 2.34)
libselinux1
  Depends: libc6 (>= 2.34)
  Depends: libpcre2-8-0 (>= 10.22)
libpcre2-8-0
  Depends: libc6 (>= 2.34)
libsemanage2
  Depends: libaudit1 (>= 1:2.2.1)
  Depends: libbz2-1.0
  Depends: libc6 (>= 2.34)
  Depends: libselinux1 (>= 3.4)
  Depends: libsemanage-common (>= 3.4-1)
  Depends: libsepol2 (>= 3.4)
libbz2-1.0
  Depends: libc6 (>= 2.4)
libsemanage-common
libsepol2
  Depends: libc6 (>= 2.33)
libedit2
  Depends: libbsd0 (>= 0.1.3)
  Depends: libc6 (>= 2.33)
  Depends: libtinfo6 (>= 6)
libbsd0
  Depends: libc6 (>= 2.34)
  Depends: libmd0 (>= 1.0.3-2)
libmd0
  Depends: libc6 (>= 2.33)
libtinfo6
  Depends: libc6 (>= 2.34)
libfido2-1
  Depends: libc6 (>= 2.36)
  Depends: libcbor0.8 (>= 0.8.0)
  Depends: libssl3 (>= 3.0.0)
  Depends: libudev1 (>= 183)
  Depends: zlib1g (>= 1:1.1.4)
libcbor0.8
  Depends: libc6 (>= 2.14)
libssl3
  Depends: libc6 (>= 2.34)
libudev1
  Depends: libc6 (>= 2.34)
zlib1g
  Depends: libc6 (>= 2.14)
libgssapi-krb5-2
  Depends: libc6 (>= 2.33)
  Depends: libcom-err2 (>= 1.43.9)
  Depends: libk5crypto3 (>= 1.20)
  Depends: libkrb5-3 (= 1.20.1-2+deb12u2)
  Depends: libkrb5support0 (>= 1.15~beta1)
libcom-err2
  Depends: libc6 (>= 2.17)
libk5crypto3
  Depends: libc6 (>= 2.33)
  Depends: libkrb5support0 (>= 1.20)
libkrb5support0
  Depends: libc6 (>= 2.34)
libkrb5-3
  Depends: libc6 (>= 2.34)
  Depends: libcom-err2 (>= 1.43.9)
  Depends: libk5crypto3 (>= 1.20)
  Depends: libkeyutils1 (>= 1.5.9)
  Depends: libkrb5support0 (= 1.20.1-2+deb12u2)
  Depends: libssl3 (>= 3.0.0)
libkeyutils1
  Depends: libc6 (>= 2.14)
```

## Saca toda la información que puedas del paquete openssh-client candidato a actualizar en tu máquina

En caso de que el paquete no estuviese instalado, el comando `apt show` también mostraría toda la información relativa al paquete de la versión candidata a la actualización. Para conocer la versión candidata se puede ejecutar `apt policy openssh-client`.

```
❯ apt policy openssh-client
openssh-client:
  Instalados: 1:9.2p1-2+deb12u3
  Candidato:  1:9.2p1-2+deb12u3
  Tabla de versión:
 *** 1:9.2p1-2+deb12u3 500
        500 http://deb.debian.org/debian bookworm/main amd64 Packages
        100 /var/lib/dpkg/status
```

Para conocer más información sobre diferentes versiones de un mismo paquete se puede usar también el comando `rmadison`, que muestra información sobre la versión de un paquete disponibles para las ramas del SO desde la oldoldstable hasta la unstable.

```
❯ rmadison openssh-client
openssh-client | 1:7.9p1-10+deb10u2 | oldoldstable     | amd64, arm64, armhf, i386
openssh-client | 1:8.4p1-2~bpo10+1  | buster-backports | amd64, arm64, armel, armhf, i386, mips, mips64el, mipsel, ppc64el, s390x
openssh-client | 1:8.4p1-5+deb11u3  | oldstable        | amd64, arm64, armel, armhf, i386, mips64el, mipsel, ppc64el, s390x
openssh-client | 1:9.2p1-2+deb12u3  | stable           | amd64, arm64, armel, armhf, i386, mips64el, mipsel, ppc64el, s390x
openssh-client | 1:9.8p1-8          | testing          | amd64, arm64, armel, armhf, i386, mips64el, ppc64el, riscv64, s390x
openssh-client | 1:9.9p1-1          | unstable         | amd64, arm64, armel, armhf, i386, mips64el, ppc64el, riscv64, s390x
```

Por tanto, la versión candidata para la instalación del paquete depende de las ramas cuyos repositorios estén presentes en el fichero sources.list, así como de la configuración de la preferencia de cada uno de estos repositorios.

En este caso, la versión instalada y la candidata coinciden porque en el fichero  sources.list sólo se recoge el repositorio de la rama stable de Debian12 pero si se incorporasen los repositorios de otras ramas como la backports o la testing, la versión candidata sería posterior a la instalada.

## Lista todo el contenido referente al paquete openssh-client actual de tu máquina. Utiliza para ello tanto dpkg como apt

Con el comando `dpkg -L openssh-client` se puede conocer la ubicación en el sistema de todos los ficheros que forman parte del paquete.

```
❯ dpkg -L openssh-client
/.
/etc
/etc/ssh
/etc/ssh/ssh_config
/etc/ssh/ssh_config.d
/usr
/usr/bin
/usr/bin/scp
/usr/bin/sftp
/usr/bin/ssh
/usr/bin/ssh-add
/usr/bin/ssh-agent
/usr/bin/ssh-argv0
/usr/bin/ssh-copy-id
/usr/bin/ssh-keygen
/usr/bin/ssh-keyscan
/usr/lib
/usr/lib/openssh
/usr/lib/openssh/agent-launch
/usr/lib/openssh/ssh-keysign
/usr/lib/openssh/ssh-pkcs11-helper
/usr/lib/openssh/ssh-sk-helper
/usr/lib/systemd
/usr/lib/systemd/user
/usr/lib/systemd/user/graphical-session-pre.target.wants
/usr/lib/systemd/user/ssh-agent.service
/usr/share
/usr/share/apport
/usr/share/apport/package-hooks
/usr/share/apport/package-hooks/openssh-client.py
/usr/share/doc
/usr/share/doc/openssh-client
/usr/share/doc/openssh-client/NEWS.Debian.gz
/usr/share/doc/openssh-client/OVERVIEW.gz
/usr/share/doc/openssh-client/README
/usr/share/doc/openssh-client/README.Debian.gz
/usr/share/doc/openssh-client/README.dns
/usr/share/doc/openssh-client/README.tun.gz
/usr/share/doc/openssh-client/changelog.Debian.gz
/usr/share/doc/openssh-client/changelog.gz
/usr/share/doc/openssh-client/copyright
/usr/share/lintian
/usr/share/lintian/overrides
/usr/share/lintian/overrides/openssh-client
/usr/share/man
/usr/share/man/man1
/usr/share/man/man1/scp.1.gz
/usr/share/man/man1/sftp.1.gz
/usr/share/man/man1/ssh-add.1.gz
/usr/share/man/man1/ssh-agent.1.gz
/usr/share/man/man1/ssh-argv0.1.gz
/usr/share/man/man1/ssh-copy-id.1.gz
/usr/share/man/man1/ssh-keygen.1.gz
/usr/share/man/man1/ssh-keyscan.1.gz
/usr/share/man/man1/ssh.1.gz
/usr/share/man/man5
/usr/share/man/man5/ssh_config.5.gz
/usr/share/man/man8
/usr/share/man/man8/ssh-keysign.8.gz
/usr/share/man/man8/ssh-pkcs11-helper.8.gz
/usr/share/man/man8/ssh-sk-helper.8.gz
/usr/bin/slogin
/usr/lib/systemd/user/graphical-session-pre.target.wants/ssh-agent.service
/usr/share/man/man1/slogin.1.gz
```

Esta salida se puede filtrar con el comando `grep` para mostrar únicamente aquellos binarios que se incluyen en el paquete.

```
❯ dpkg -L openssh-client | grep bin
/usr/bin
/usr/bin/scp
/usr/bin/sftp
/usr/bin/ssh
/usr/bin/ssh-add
/usr/bin/ssh-agent
/usr/bin/ssh-argv0
/usr/bin/ssh-copy-id
/usr/bin/ssh-keygen
/usr/bin/ssh-keyscan
/usr/bin/slogin
```

Con herramientas del gestor de paquetes apt se puede acceder a información recogida en ejercicios anteriores usando, por ejemplo, el comando `apt show`, `apt depends` para conocer las dependencias del paquete, `apt rdepends` para conocer las dependencias inversas o `apt-rdepends` para conocer las dependencias de forma recursiva.

## Listar el contenido de un paquete sin la necesidad de instalarlo o descargarlo

Para listar el contenido de un paquete independientemente de si está o no instalado en el sistema se puede usar el comando `apt-file find`.

```
❯ apt-file find openssh-client
openssh-client: /usr/share/apport/package-hooks/openssh-client.py
openssh-client: /usr/share/doc/openssh-client/NEWS.Debian.gz
openssh-client: /usr/share/doc/openssh-client/OVERVIEW.gz
openssh-client: /usr/share/doc/openssh-client/README
openssh-client: /usr/share/doc/openssh-client/README.Debian.gz
openssh-client: /usr/share/doc/openssh-client/README.dns
openssh-client: /usr/share/doc/openssh-client/README.tun.gz
openssh-client: /usr/share/doc/openssh-client/changelog.Debian.gz
openssh-client: /usr/share/doc/openssh-client/changelog.gz
openssh-client: /usr/share/doc/openssh-client/copyright
openssh-client: /usr/share/lintian/overrides/openssh-client
openssh-client-ssh1: /usr/share/doc/openssh-client-ssh1/NEWS.Debian.gz
openssh-client-ssh1: /usr/share/doc/openssh-client-ssh1/README
openssh-client-ssh1: /usr/share/doc/openssh-client-ssh1/README.Debian.gz
openssh-client-ssh1: /usr/share/doc/openssh-client-ssh1/changelog.Debian.gz
openssh-client-ssh1: /usr/share/doc/openssh-client-ssh1/changelog.gz
openssh-client-ssh1: /usr/share/doc/openssh-client-ssh1/copyright
openssh-server: /usr/share/doc/openssh-client/examples/ssh-session-cleanup.service
❯ apt-file find tldr | grep tldr:
tldr: /usr/bin/tldr-hs
tldr: /usr/share/doc/tldr/buildinfo_amd64.gz
tldr: /usr/share/doc/tldr/changelog.Debian.amd64.gz
tldr: /usr/share/doc/tldr/changelog.Debian.gz
tldr: /usr/share/doc/tldr/changelog.gz
tldr: /usr/share/doc/tldr/copyright
tldr: /usr/share/man/man1/tldr-hs.1.gz
```

## Simula la instalación del paquete openssh-client

Se puede simular la instalación de un paquete con la opción `-s` del comando `apt install`.

```
❯ sudo apt install -s openssh-client
Leyendo lista de paquetes... Hecho
Creando árbol de dependencias... Hecho
Leyendo la información de estado... Hecho
Inst openssh-client (1:8.9p1-3 Ubuntu:22.04/jammy [amd64])
Conf openssh-client (1:8.9p1-3 Ubuntu:22.04/jammy [amd64])
```

En caso de que se genere algún conflicto o problema durante la instalación, la salida de este comando informa al respecto. En este caso, como esto no ocurre, simplemente muestra la información sobre el paquete que se instalaría si se ejecuta el comando sin la opción `-s`.

## 10. ¿Qué comando te informa de los posible bugs que presente un determinado paquete?

El comando `apt-listbugs` muestra el informe de fallos de un determinado paquete. Con la opción `-s` se puede filtrar por el nivel de gravedad del bug reportado.

```
❯ apt-listbugs -s all list openssh-client
Obteniendo informes de fallo... Finalizado
Analizando información Encontrada/Corregida... Finalizado
Fallos important del paquete openssh-client (→ ) <Pendientes>
 b1 - #1038150 - openssh-client: Please add the openssh-client group rename from "ssh" to "_ssh" to the bookworm release notes
 b2 - #250311 - ssh: pubkey auth fails between 3.8.1p1-3 and 3.4(woody)
 b3 - #314596 - Cannot ssh OUT from host as non-root user
 b4 - #337484 - openssh-client: ssh-add displays password with bad permissions on /dev/tty
 b5 - #341042 - ssh: Slow Connections Due to Bogus IPv6 name resolution
 b6 - #391964 - warn if client-requested locale not available on server?
 b7 - #415697 - openssh-client: portforwarding with either -R or -L fails with module tun loaded
 b8 - #513071 - Regression: for some hosts etch can connect but lenny can't (password auth)
…
 b270 - #911758 - ssh-add doesn't recognize PKCS#11 URL
 b271 - #538197 - openssh-client: [sftp] support multiple args: <command> <item> <item> ...
Fallos minor del paquete openssh-client (→ ) <Corregidos en alguna versión>
 b272 - #1001186 - ssh-agent: SSH_AUTH_SOCK temporary directory uses 6 template chars out of 12 (Corregido: openssh/1:9.3p1-1)
Resumen:
 openssh-client(272 fallos)
```

Otras herramientas como `bts` también permiten acceder a la web de Debian para conocer la lista de bugs que se han reportado respecto a un determinado paquete.

![Salida del comando bts bugs openssh-client](/assets/img/aso/practica1/img1.png)

## 11. Después de realizar un apt update && apt upgrade. Si quisieras actualizar únicamente los paquetes que tienen de cadena openssh. ¿Qué procedimiento seguirías?. Realiza esta acción, con las estructuras repetitivas que te ofrece bash, así como con el comando xargs

Justo después de realizar una actualización de la paquetería del sistema con `apt update && apt upgrade` no habría ningún paquete relacionado con openssh que actualizar porque todos estarían ya en su versión más reciente.

Sin embargo, si se quieren mantener actualizaciones posteriores de estos paquetes tras un tiempo habría que usar de nuevo el comando `apt update` para consultar las nuevas versiones disponibles en los repositorios. Con los repositorios actualizados, se debería consular la lista de paquetes disponibles para actualizar con `apt list --upgradable`. Además, la salida de este comando se puede filtrar usando `grep` para eliminar de la lista todos aquellos paquetes que no estén relacionados con `openssh`. Finalmente, con el comando `xargs` se puede pasar esta lista de paquetes como argumentos al comando apt upgrade para ejecutar la actualización de los paquetes listado.

Este flujo de trabajo se podría ejecutar en una única línea de comando redireccionando los diferentes comentarios a través de tuberías: `apt update && apt list --upgradable | grep openssh | xargs apt upgrade`.

Otra forma de conseguir que sólo los paquetes relacionados con `openssh` se actualicen es marcar el resto de paquetes del sistema como manuales o retenidos. De esta manera, cuando se ejecuta un `apt upgrade` del sistema, sólo los paquetes relacionados con `openssh`, marcados como auto, se actualizarán.

## 12. ¿Cómo encontrarías qué paquetes dependen de un paquete específico?

Con el comando `apt rdepends` se puede saber cuáles son las dependencias inversas de un paquete, es decir, qué paquetes dependen de él.

```
❯ apt rdepends openssh-client
openssh-client
Reverse Depends:
  Reemplaza: openssh-server (<< 1:7.9p1-8)
  Depende: arb
 |Depende: zssh
  Recomienda: xpra
  Depende: xen-tools
  Depende: x2goserver
  Depende: x2goclient
 |Recomienda: waypipe
  Recomienda: vorta
  Depende: vagrant
  Recomienda: unison-2.52-gtk
  Recomienda: unison-2.52
  Recomienda: unison-2.51+4.13.1-gtk
  Recomienda: unison-2.51+4.13.1
  Recomienda: tsung
  Depende: python3-tomahawk
  Sugiere: tla
  Recomienda: task-ssh-server
 |Depende: taktuk
  Recomienda: python3-jarabe
  Depende: ssvnc
 |Depende: sshuttle
  Depende: sshfs
  Depende: ssh-tools
  Depende: ssh-import-id
  Depende: ssh-cron
  Depende: ssh-contact-client
  Depende: ssh-agent-filter
  Depende: snapd
  Sugiere: smokeping
  Sugiere: slack
 |Recomienda: sisu
  Recomienda: simple-tpm-pk11
  Depende: sidedoor
  Depende: secpanel
  Recomienda: seahorse
  Depende: python3-sahara-plugin-vanilla
  Depende: python3-sahara-plugin-spark
  Sugiere: rsync
 |Recomienda: rsnapshot
  Recomienda: rsbackup-graph
  Recomienda: rsbackup
 |Depende: rancid
  Depende: pssh
 |Depende: pkg-perl-tools
  Depende: piuparts-slave-from-git-deps
  Depende: piuparts-slave
  Recomienda: pdudaemon
 |Depende: pdsh
  Depende: python3-parallax
  Depende: oz
  Recomienda: ostree-push
  Depende: openstack-cluster-installer-openstack-ci
  Recomienda: openssh-known-hosts
 |Depende: ssh-askpass-gnome
  Depende: ssh (>= 1:9.2p1-2+deb12u3)
  Depende: openssh-tests (= 1:9.2p1-2+deb12u3)
  Depende: openssh-sftp-server (= 1:9.2p1-2+deb12u3)
  Depende: openssh-server (= 1:9.2p1-2+deb12u3)
  Depende: git-annex (>= 1:5.6p1)
 |Depende: openmpi-bin
  Sugiere: oar-user
  Depende: oar-server
  Depende: oar-node
  Depende: python3-nova
  Depende: network-manager-ssh
  Depende: mussh
  Depende: mssh
  Depende: mosh
  Recomienda: mercurial
 |Recomienda: lxsession
  Sugiere: lsh-server
  Depende: live-task-standard
  Recomienda: live-task-recommended
  Depende: libnetapp-perl
 |Depende: libnet-ssh-perl
 |Depende: libnet-sftp-foreign-perl
 |Depende: libnet-scp-perl
  Depende: libnet-openssh-perl
  Depende: libguestfs0
  Mejora: libconfig-model-openssh-perl
  Recomienda: lftp
  Recomienda: lava-server
  Depende: lava
 |Depende: lam-runtime
  Recomienda: python3-labgrid
  Recomienda: kwalletcli
  Depende: ksshaskpass
 |Depende: keychain
  Recomienda: hollywood
  Depende: hash-slinger
  Depende: gitolite3
  Rompe: git (<< 1:6.8)
 |Depende: ansible
  Depende: ganeti-3.0
 |Sugiere: gabedit
 |Sugiere: gabedit
 |Depende: fssync
  Recomienda: freedombox
  Recomienda: fence-agents
  Recomienda: fdroidserver
  Recomienda: fai-server
  Sugiere: duply
 |Recomienda: dupload
  Sugiere: dropbear-bin
  Recomienda: python3-dput
  Sugiere: dput
 |Recomienda: dish
  Recomienda: diffoscope-minimal
  Recomienda: diffoscope
  Recomienda: debvm
  Sugiere: debian-goodies
  Recomienda: cvs
  Depende: cockpit-tests
  Depende: clusterssh
  Sugiere: clonezilla
 |Recomienda: ckermit
  Mejora: chkrootkit
  Depende: ceph-mgr-cephadm
  Recomienda: btrbk
  Recomienda: barman-cli
  Recomienda: barman
 |Recomienda: backuppc
  Sugiere: backup-manager
  Depende: backintime-common
  Mejora: autossh
 |Depende: autossh
  Depende: apt-dater
 |Depende: ansible-core
```

Este comando devuelve, además, información sobre aquellos paquetes que recomiendan o sugieren la instalación del comando consultado así como aquellos paquetes en los que puede generar conflictos o que puede romper. También se muestran los paquetes a los que sustituye o mejora el paquete consultado.

## 13. ¿Cómo procederías para encontrar el paquete al que pertenece un determinado fichero?

La opción `-S` o `--search` del comando dpkg permite encontrar el paquete al que pertenece un determinado fichero. De forma inversa a la ejecución del comando dpkg con la opción `-L`, que muestra la lista de ficheros que tienen relación con un paquete indicado, la opción `-S` de este comando permite pasarle como argumento una expresión o cadena de búsqueda que corresponda a la ruta a un fichero. La salida del comando dpkg con la opción `-S` mostrará el paquete al que pertenece ese fichero.

```
❯ dpkg -S /usr/bin/ssh
openssh-client: /usr/bin/ssh
```

Otra forma de obtener el mismo resultado es usando el comando `apt-find` seguido de la ruta al fichero cuyo paquete se quiere conocer. La salida de este comando es similar a la del comando anteriormente citado aunque, en este caso, el filtro es mucho más laxo y se muestran todos aquellos paquetes en los que hay algún fichero que coincida, en parte, con la cadena introducida. Esto convierte a esta opción en una herramienta de búsqueda mucho más genérica, mientras que la búsqueda con `dpkg -S` es más precisa.

```
❯ apt-file search /usr/bin/ssh
hash-slinger: /usr/bin/sshfp              
lsh-utils: /usr/bin/ssh-conv
node-sshpk: /usr/bin/sshpk-conv
node-sshpk: /usr/bin/sshpk-sign
node-sshpk: /usr/bin/sshpk-verify
openssh-client: /usr/bin/ssh
openssh-client: /usr/bin/ssh-add
openssh-client: /usr/bin/ssh-agent
openssh-client: /usr/bin/ssh-argv0
openssh-client: /usr/bin/ssh-copy-id
openssh-client: /usr/bin/ssh-keygen
openssh-client: /usr/bin/ssh-keyscan
openssh-client-ssh1: /usr/bin/ssh-keygen1
openssh-client-ssh1: /usr/bin/ssh1
python3-sshtunnel: /usr/bin/sshtunnel
rsendmail: /usr/bin/sshsendmail
slurm-client: /usr/bin/sshare
slurm-client-emulator: /usr/bin/sshare-emulator
ssh-agent-filter: /usr/bin/ssh-agent-filter
ssh-agent-filter: /usr/bin/ssh-askpass-noinput
ssh-askpass-fullscreen: /usr/bin/ssh-askpass-fullscreen
ssh-audit: /usr/bin/ssh-audit
ssh-contact-client: /usr/bin/ssh-contact
ssh-cron: /usr/bin/ssh-cron
ssh-import-id: /usr/bin/ssh-import-id
ssh-import-id: /usr/bin/ssh-import-id-gh
ssh-import-id: /usr/bin/ssh-import-id-lp
ssh-tools: /usr/bin/ssh-certinfo
ssh-tools: /usr/bin/ssh-diff
ssh-tools: /usr/bin/ssh-facts
ssh-tools: /usr/bin/ssh-force-password
ssh-tools: /usr/bin/ssh-hostkeys
ssh-tools: /usr/bin/ssh-keyinfo
ssh-tools: /usr/bin/ssh-ping
ssh-tools: /usr/bin/ssh-version
sshcommand: /usr/bin/sshcommand
sshesame: /usr/bin/sshesame
sshfs: /usr/bin/sshfs
sshpass: /usr/bin/sshpass
sshuttle: /usr/bin/sshuttle
ssvnc: /usr/bin/sshvnc
```

## 14. ¿Que procedimientos emplearías para liberar la caché en cuanto a descargas de paquetería?

Los paquetes descargados a través del gestor de paquetes apt se almacenan en una caché junto a sus dependencias en el directorio var/cache/apt/archive. Esta información se almacena en el disco duro del equipo incluso cuando la instalación del paquete ha terminado de forma satisfactoria.

Esta caché facilita y agiliza el proceso de reinstalar o reconfigurar paquetes ya que el sistema sólo tiene que acceder a los ficheros de instalación almacenado en la caché y no necesita buscarlos y descargarlos de los repositorios de Debian, sin embargo, en ocasiones, la caché de apt puede ocupar mucho espacio y puede ser necesario limpiarla. Para ello apt ofrece el comando `apt clean`, que borra todo el contenido del directorio en el que se almacenan los paquetes y dependencias instalados por apt.

Asimismo, la herramienta `apt autoclean` permite borrar sólo los paquetes de este directorio que se han quedado obsoletos pero mantiene aquellos que están actualizados y, por tanto, el sistema aún puede necesitar.

## 15. Realiza la instalación del paquete keyboard-configuration pasando previamente los valores de los parámetros de configuración como variables de entorno

Para instalar paquetes de forma no interactiva se pueden usar los comandos `dpkg` y `debconf`. El comando `debconf` permite conocer cuáles son los parámetros que se deben configurar durante la instalación de un paquete. También permite reconfigurar la instalación para volver a elegir los parámetros necesarios en caso de que la configuración en el momento de la instalación no sea la adecuada.

En primer lugar, es necesario conocer cuáles son los parámetros que se solicitan durante la instalación de un determinado paquete. Una vez conocida esta información, se puede configurar el proceso de instalación desatendido con `dpkg` y `debconf` seleccionando la opción `noninteractive`.

Posteriormente, se deben exportar los parámetros como variables de entorno usando `export` seguido del nombre del parámetro y el valor de la variable. La configuración desatendida de `debconf` y `dpkg` tomará estas variables como parámetros para la instalación y configuración del paquete.

El gestor de paquetes apt también permite configurar una instalación a través de variables de entorno de una manera similar. Tras exportar las variables de entorno se ejecuta el comando `DEBIAN_FRONTEND=noninteractive apt install -y`. Con la variable `DEBIAN_FRONTEND=noninteractive` se indica al gestor de paquetes que debe leer los parámetros de las variables de entorno.

## 16. Reconfigura el paquete locales de tu equipo, añadiendo una localización que no exista previamente. Comprueba a modificar las variables de entorno correspondientes para que la sesión del usuario utilice otra localización

Para reconfigurar el paquete locale se pueden usar las herramientas del paquete `debconf-utils`. El comando `debconf-set-selections` permite modificar las variables de un paquete instalado y `dpkg-reconfigure` también ofrece la posibilidad de reconfigurar un paquete. En este caso, se pueden reconfigura el paquete locale para añadir una nueva localización.

```
❯ sudo dpkg-reconfigure --frontend Dialog --priority low locales
debconf: Please do not capitalize the first letter of the debconf frontend.
Generating locales (this might take a while)...
  en_US.UTF-8... done
  es_ES.UTF-8... done
Generation complete.
```

Tras reconfigurar el paquete y añadir una nueva localización, ésta se puede modificar en el fichero de configuración /etc/default/locale o, de forma más genérica, se puede generar y exportar la variable de entorno con la orden `${LC_ALL=en_US.UTF-8}; export LC_ALL`.

```
❯ sudo nano /etc/default/locale 
❯ locale -a
C
C.utf8
en_US.utf8
es_ES.utf8
POSIX
```

## 17. Interrumpe la configuración de un paquete y explica los pasos a dar para continuar la instalación

Si se interrumpe la instalación de un paquete en el proceso de configuración, se puede continuar el proceso usando el comando `dpkg --configure -a`. Con este  comando  se configurar los paquetes que se han desempaquetado en el proceso de instalación interrumpido pero no se han llegado a configurar. `dpkg --configure` desempaqueta los ficheros de configuración y restaura los ficheros de configuración de los paquetes cancelados. Posteriormente ejecuta los scripts postinstalación del paquete, en caso de que existan.

## 18. Explica la instrucción que utilizarías para hacer una actualización completa de todos los paquetes de tu sistema de manera completamente no interactiva

Para actualizar toda la paquetería del sistema de forma no interactiva se puede ejecutar el comando `sudo apt update && sudo apt upgrade -y`.

Con este flujo, en primer lugar se revisa la información sobre actualizaciones disponible en los repositorios con el comando `apt update`. Cuando la ejecución de este comando es exitosa, se ejecuta la actualización de la paquetería con el comando `apt upgrade`. Con al opción `-y`, se contesta afirmativamente a todas las preguntas interactivas del comando.

## 19. Bloquea la actualización de determinados paquetes

Para bloquear la actualización de determinados paquetes se usa el comando `apt-mark`. Con este comando se pueden marcar como instalados manualmente determinados paquetes, lo que evita que se actualicen de forma automática. Si, además, se marcan como retenidos, estos paquetes no se actualizan nunca. Por ejemplo, para bloquear la actualización automática del paquete `git` se puede ejecutar el siguiente comando:

```
❯ sudo apt-mark manual git 
git set to manually installed.
```

Para evitar que el paquete wget se actualice en cualquier caso se puede ejecutar el siguiente comando:

```
❯ sudo apt-mark hold wget
wget set on hold.
```

<div class="page"/>

# Trabajo con ficheros .deb

## Descarga un paquete sin instalarlo, es decir, descarga el fichero .deb correspondiente. Indica diferentes formas de hacerlo

El fichero .deb de un paquete se puede descargar usando el comando `apt download`:

```
❯ apt download mdadm 
Get:1 http://deb.debian.org/debian bookworm/main amd64 mdadm amd64 4.2-5 [443 kB]
Fetched 443 kB in 0s (3,791 kB/s)
```

o con el comando `wget` indicando la url de descarga desde la web del repositorio de Debian:

```
❯ wget "http://ftp.es.debian.org/debian/pool/main/m/mdadm/mdadm-udeb_4.2-5_amd64.udeb"
```

## ¿Cómo puedes ver el contenido, que no extraerlo, de lo que se instalará en el sistema de un paquete deb?

Para ver el contenido de un paquete .deb sin llegar a desempaquetarlo se puede usar la opción `-t` del comando `ar`.

```
❯ ar -t mdadm_4.2-5_amd64.deb 
debian-binary
control.tar.xz
data.tar.xz
```

Para ver el contenido del paquete con más detalle es necesario desempaquetarlo.

```
❯ ar -x mdadm_4.2-5_amd64.deb
```

Una vez desempaquetado, con el comando `tar` se puede listar, sin extraer, todo el contenido de cada uno de los archivos incluidos en el paquete.

```
❯ tar -tf data.tar.xz 
./
./etc/
./etc/cron.d/
./etc/cron.d/mdadm
./etc/cron.daily/
./etc/cron.daily/mdadm
./etc/init.d/
./etc/init.d/mdadm
./etc/init.d/mdadm-waitidle
./etc/logcheck/
./etc/logcheck/ignore.d.server/
./etc/logcheck/ignore.d.server/mdadm
./etc/logcheck/violations.d/
./etc/logcheck/violations.d/mdadm
./etc/mdadm/
./etc/modprobe.d/
./etc/modprobe.d/mdadm.conf
…
./usr/share/mdadm/
./usr/share/mdadm/checkarray
./usr/share/mdadm/mdcheck
./usr/share/mdadm/mkconf
./lib/systemd/system/mdadm-waitidle.service
./lib/systemd/system/mdadm.service
```

## Sobre el fichero .deb descargado, utiliza el comando ar. ar permite extraer el contenido de una paquete deb. Indica el procedimiento para visualizar con ar el contenido del paquete deb. Con el paquete que has descargado y utilizando el comando ar, descomprime el paquete. ¿Qué información dispones después de la extracción?. Indica la finalidad de lo extraído

Tras desempaquetar el paquete .deb:

```
❯ ar -x mdadm_4.2-5_amd64.deb
```

Los contenidos del paquete se muestran en el directorio tras desempaquetar el paquete.

```
❯ ls -l
total 876
-rw-r--r-- 1 usuario usuario  15156 Sep 27 10:03 control.tar.xz
-rw-r--r-- 1 usuario usuario 427708 Sep 27 10:03 data.tar.xz
-rw-r--r-- 1 usuario usuario      4 Sep 27 10:03 debian-binary
```

En el archivo control.tar.xz se encuentran todos los ficheros de control del paquete. Entre ellos los scripts que se ejecutan antes y después de la instalación, las plantillas, los ficheros de configuración o el fichero md5sums con los hashes de verificación de las versiones de los ficheros del paquete.

```
❯ tar -tf control.tar.xz 
./
./conffiles
./config
./control
./md5sums
./postinst
./postrm
./preinst
./prerm
./templates
./triggers
```

Por su parte, en el archivo data.tar.xz se recogen los ficheros de configuración, binarios, ficheros de servicios y otro tipo de ficheros que componen la parte fundamental del paquete y que son, en definitiva, los que se encargan de que la herramienta instalada cumpla su función.

## Indica el procedimiento para descomprimir lo extraído por ar del punto anterior. ¿Qué información contiene?

Para descomprimir el contenido de cada archivo .tar.xz del paquete desempaquetado anteriormente se puede usar el comando `tar` con la opción `-x`, para indicar que se debe realizar la descompresión y la opción `-f` para indicar el nombre del archivo que se debe descomprimir.

```
❯ tar -xf control.tar.xz
❯ tar -xf data.tar.xz
❯ ls -l
total 964
-rw-r--r-- 1 usuario usuario    181 feb 24  2023 conffiles
-rwxr-xr-x 1 usuario usuario   1161 feb 24  2023 config
-rw-r--r-- 1 usuario usuario    808 feb 24  2023 control
-rw-r--r-- 1 usuario usuario  15156 sep 27 12:22 control.tar.xz
-rw-r--r-- 1 usuario usuario 427708 sep 27 12:22 data.tar.xz
-rw-r--r-- 1 usuario usuario      4 sep 27 12:22 debian-binary
drwxr-xr-x 8 usuario usuario   4096 feb 24  2023 etc
drwxr-xr-x 4 usuario usuario   4096 feb 24  2023 lib
-rw-r--r-- 1 usuario usuario   5060 feb 24  2023 md5sums
-rw-r--r-- 1 usuario usuario 443056 feb 24  2023 mdadm_4.2-5_amd64.deb
-rwxr-xr-x 1 usuario usuario   5279 feb 24  2023 postinst
-rwxr-xr-x 1 usuario usuario   1734 feb 24  2023 postrm
-rwxr-xr-x 1 usuario usuario    455 feb 24  2023 preinst
-rwxr-xr-x 1 usuario usuario    485 feb 24  2023 prerm
drwxr-xr-x 2 usuario usuario   4096 feb 24  2023 sbin
-rw-r--r-- 1 usuario usuario  25872 feb 24  2023 templates
-rw-r--r-- 1 usuario usuario     82 feb 24  2023 triggers
drwxr-xr-x 3 usuario usuario   4096 feb 24  2023 usr
```

Listando el contenido del directorio de trabajo tras descomprimir los ficheros del paquete se puede comprobar que, como se había adelantado en el ejercicio anterior, el archivo control contiene ficheros de utilidad para gestionar la instalación del paquete. Entre estos ficheros, algunos son importantes para verificar la seguridad e integridad de los ficheros que forman parte del paquete mientras que algunos otros incluyen órdenes relacionadas con el proceso de instalación como scripts que se ejecutan antes o después de la instalación. También incluye ficheros que contienen las diferentes variables que se deben configurar para el correcto funcionamiento del paquete.

Por su parte, en el archivo data se incluyen todos los ficheros que forman parte de la herramienta instalada con este paquete, es decir, desde sus ficheros de configuración, binarios que se pueden ejecutar a través de la línea de comandos, ficheros de ayuda y manual, ficheros para permitir a la bash autocompletar el comando al tabular, los ficheros para generar los daemons necesarios para el correcto funcionamiento de la herramienta, etc. En definitiva, este directorio contiene todos los ficheros necesarios para el funcionamiento del paquete descargado tras su instalación.

<div class="page"/>

# Trabajo con repositorios

## Añade a tu fichero sources.list los repositorios de bookworm-backports y sid

```
deb http://deb.debian.org/debian/ bookworm main non-free-firmware
deb-src http://deb.debian.org/debian/ bookworm main non-free-firmware
deb http://deb.debian.org/debian/ bookworm-backports main non-free-firmware
deb http://deb.debian.org/debian/ sid main non-free-firmware
```

## Configura el sistema APT para que los paquetes de debian bookworm tengan mayor prioridad y por tanto sean los que se instalen por defecto

Para configurar el orden de prioridad de instalación del sistema APT se puede configurar el fichero /etc/apt/preferences. Por defecto, este fichero no existe en el equipo y hay que crearlo.

Para que los paquetes de debian bookworm (stable) tengan la mayor prioridad se debe añadir la siguiente configuración:

```
Package: *
Pin: release a=stable
Pin_Priority: 500
```

## Configura el sistema APT para que los paquetes de bookworm-backports tengan mayor prioridad que los de unstable

Para que los paquetes de la rama bookworm-backports tengan mayor prioridad que los de la rama unstable en el gestor de paquetes APT se puede añadir la siguiente configuración al fichero /etc/apt/preferences.

```
Package: *
Pin: release a=stable-backports
Pin_Priority: 100
Package: *
Pin: release a=unstable
Pin_Priority: 1
```

## ¿Cómo añades la posibilidad de descargar paquetería de la arquitectura i386 en tu sistema. ¿Que comando has empleado?. Lista arquitecturas no nativas. ¿Cómo procederías para desechar la posibilidad de descargar paquetería de la arquitectura i386?

Para añadir la posibilidad de descargar paquetería de la arquitectura i386 en el sistema Debian con arquitectura nativa amd64 se puede usar el comando `dpkg`.

```
❯ sudo dpkg --add-architecture i386
```

Para listar las arquitecturas no nativas habilitadas en el sistema, el comando dpkg ofrece la opción `--print-foreign-architecutres`.

```
❯ dpkg --print-foreign-architectures
i386
```

Finalmente, para eliminar una determinada arquitectura de la lista de arquitecturas no nativas añadidas al sistema se usa la opción `--remove-architecture` del mismo comando.

```
❯ sudo dpkg --remove-architecture i386
```

Si se han instalado paquetes de la arquitectura no nativa configurada en el sistema se puede generar un conflicto al intentar eliminar esta opción mientras estos paquetes están instalados. Por eso, es necesario desinstalar los paquetes de esta arquitectura antes de eliminarla de la lista. Para la arquitectura i386, por ejemplo, esto se puede hacer con el comando `apt purge “.*:i386”`.

La lista de arquitecturas no nativas que se configuran en un sistema Debian se almacena en el directorio /var/lib/dpkg/arch.

## Si quisieras descargar un paquete, ¿cómo puedes saber todas las versiones disponible de dicho paquete?

Para conocer las versiones disponibles de un paquete en los repositorios de Debian en cada rama de la distribución se puede ejecutar el comando `rmadison`. A este comando se le debe indicar el nombre del paquete y devuelve una lista con todas las versiones disponibles para cada una de las ramas de Debian.

```
❯ rmadison openssh-client
openssh-client | 1:7.9p1-10+deb10u2 | oldoldstable     | amd64, arm64, armhf, i386
openssh-client | 1:8.4p1-2~bpo10+1  | buster-backports | amd64, arm64, armel, armhf, i386, mips, mips64el, mipsel, ppc64el, s390x
openssh-client | 1:8.4p1-5+deb11u3  | oldstable        | amd64, arm64, armel, armhf, i386, mips64el, mipsel, ppc64el, s390x
openssh-client | 1:9.2p1-2+deb12u3  | stable           | amd64, arm64, armel, armhf, i386, mips64el, mipsel, ppc64el, s390x
openssh-client | 1:9.8p1-8          | testing          | amd64, arm64, armel, armhf, i386, mips64el, ppc64el, riscv64, s390x
openssh-client | 1:9.9p1-1          | unstable         | amd64, arm64, armel, armhf, i386, mips64el, ppc64el, riscv64, s390x
```

## Indica el procedimiento para descargar un paquete del repositorio stable

El gestor de paquetes APT utiliza la opción `-t` para recibir la rama de Debian de la que se debe instalar un paquete determinado como argumento. En este caso, para instalar un paquete del repositorio stable se puede usar o no la opción `-t`, puesto que la rama stable es la rama desde la que se descargan los paquetes por defecto. De esta forma, estos dos comandos se pueden usar de forma indistinta.

```
sudo apt install openssh-client
```

```
sudo apt install openssh-client -t stable
```

```
sudo apt install openssh-client -t bookworm
```

Cabe destacar que, para que una rama de la distribución esté disponible como argumento para la opción `-t` de este comando, la rama debe estar incluida en el fichero sources.list. De esta manera, las opciones para un sistema en el que las ramas configuradas en este fichero son la stable, la stable-security y la stable-updates, los argumentos que acepta la opción `-t` del comando son esas tres ramas.

```
❯ apt install -t 
bookworm           bookworm-updates   stable             stable-updates
bookworm-security  now                stable-security  
```

## Indica el procedimiento para descargar un paquete del repositorio de bookworm-backports

Dando por supuesto que la rama configuada en el fichero sources.list y con prioridad más alta es la stable o bookworm, para descargar paquetes de la rama bookworm-backports se debe indicar este parámetro con la opción `-t` del comando apt install.

```
sudo apt install openssh-client -t bookworm-backports
```

## Indica el procedimiento para descargar un paquete del repositorio de sid

Para que un paquete se instale desde la rama unstable o sid de los repositorios de debian se puede usar uno de estos dos parámetros en la opción `-t` del comando `apt-install`:

```
sudo apt install openssh-client -t sid
```

```
sudo apt install openssh-client -t unstable
```

## Indica el procedimiento para descargar un paquete de arquitectura i386

Si se necesita instalar el paquete para una arquitectura no nativa del sistema operativo, esta opción también se puede indicar a través del comando `apt-install`.

```
sudo apt install openssh-client:i386
```

<div class="page"/>

# Trabajo con directorios

## /var/lib/apt/lists/

Este directorio almacena información sobre los paquetes de los repositorios de Debian. Este directorio se rellena con la actualización de la paquetería a través del comando `apt update`. A él acceden otros comandos del gestor de paquete apt para recuperar información relativa a los paquetes del sistema, como `apt-cache` o `apt install`.

## /var/lib/dpkg/available

Este es el fichero en el que `dpkg` almacena un registro de los paquetes disponibles para el sistema. Se trata de un fichero que funciona a modo de cache para el gestor de paquetes `dpkg`. El gestor de paquetes apt usa una caché diferente.

## /var/lib/dpkg/status

El fichero status almacena información sobre el estado de los paquetes del sistema. La herramienta `dpkg` accede a este fichero para saber si un paquete debe ser desempaquetado, configurado o eliminado.

## /var/cache/apt/archives/

En este directorio se almacena la información relativa a los paquetes descargados y actualizados a través del gestor de paquetes apt. Cumple una función similar a la que tiene el fichero available de la herramienta `dpkg`. Este directorio almacena toda la información incluso después de que los paquetes se hayan instalado con éxito. Varios comandos del gestor de paquetes apt acceden a este directorio para recuperar la información que necesitan para mostrarla o para instalar o actualizar paquetes.

## /var/log/apt/history.log

Este es un fichero de log en el que se almacena información sobre los paquetes que se han instalado, actualizado o desinstalado a través del gestor de paquetes apt. Otras herramientas para la gestión de paquetes como `dpkg` tienen sus propios archivos de log, de manera que la información sobre los paquetes gestionados con una de estas herramientas no aparecen en el log de las otras.