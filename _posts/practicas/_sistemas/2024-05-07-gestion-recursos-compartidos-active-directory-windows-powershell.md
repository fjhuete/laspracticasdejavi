---
title:  "Gestión de recursos compartidos en Windows Active Directory con cmd y Powershell"
date:   2024-05-07 01:00:00 +0200
categories: sistemas
tag: [sistemas, comandos, PowerShell, Windows, recursos compartidos, Active Directory, Directorio Activo, cmd, controlador de dominio, Implantación de Sistemas Operativos]
---
Active Directory o Directorio Activo es un servicio de directorio de Windows en el que un equipo servidor comparte recursos como usuarios, grupos o directorios con los equipos clientes que se conectan a él.

En este post se muestra cómo se puede compartir un directorio en el controlador de dominio desde la cmd y desde la PowerShell.

# Compartir un directorio en el controlador de dominio desde cmd

En primer lugar, se crea el directorio.

```
mkdir C:\RecursoCompartido
```

Para establecer el directorio como recurso compartido:

```
net share recursoCompartido=C:\RecursoCompartido
```

El recurso compartido es visible para el cliente.

# Compartir un directorio en el controlador de dominio desde PowerShell

Tras crear el directorio, se establece como recurso compartido

```powershell
New-SmbShare -Name recursoCompartido -Path C:\Compartido\
```
Después, se concede permiso de lectura al grupo de usuarios de dominio y se revocan el resto de permisos.

```powershell
Grant-SmbShareAccess -Name recursoCompartido -AccountName 'DOMA\Usuarios del dominio' -AccessRight Read
Revoke-SmbShareAccess -Name recursoCompartido -AccountName 'Todos'
```

El recurso compartido ya es visible para el cliente. Posteriormente, se crea una unidad con el recurso compartido.

```powershell
New-PSDrive -Name Y -PSProvider FileSystem -Root \\DC1DOMA\recursoCompartido -Persist
```

Y, finalmente, el cliente puede mapear el recurso y acceder a él.

```powershell
New-SmbMapping -LocalPath Y: -RemotPath \\DC1DOMA\recursoCompartido
```