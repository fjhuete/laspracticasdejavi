---
title:  "Gestión de dispositivos de almacenamiento con Powershell"
date:   2024-05-05 01:00:00 +0200
categories: sistemas
tag: [sistemas, comandos, PowerShell, Windows, dispositivos de almacenamiento, sistema de ficheros, Implantación de Sistemas Operativos]
---
El módulo Storage de la PowerShell de Windows contiene múltiple cmdlets para gestionar los dispositivos de almacenamiento del sistema. En este post se recoge una lista de cmdlets de este módulo que cumplen diferentes funciones para realizar este trabajo.

# Get-Disk

## Definición

Muestra los discos visibles en el sistema operativo

### Sintaxis

```powershell
Get-Disk [opciones]
```

## Opciones

Alguna de las opciones más relevantes de este comando son:

- `-FriendlyName`. Muestra el disco que corresponde con el nombre indicado.
- `-Number`. Muestra el disco que corresponde al número indicado.

## Ejemplos de uso

Mostrar todos los discos:

```powershell
Get-Disk
```

Mostrar un disco en concreto:

```powershell
Get-Disk -Number 1
```

# Get-Partition

## Definición

Lista todas las particiones visibles en todos los discos del sistema

### Sintaxis

```powershell
Get-Partition [opciones]
```

## Opciones

Entre las opciones más relevantes están:

- `-DiskNumber`. Muestras las particiones asociadas a un disco
- `-DriveLetter`. Muestra las particiones asociadas a un volumen
- `-PartitionNumber`. Muestra las particiones que correspondan con el número de partición indicado.

## Ejemplos de uso

Para ver todas las particiones de todos los discos

```powershell
Get-Partition
```

Para ver todas las particiones de un disco

```powershell
Get-Partition -DiskNumber 2
```

Para ver todas las particiones asociadas a un volumen

```powershell
Get-Partition -DriveLetter C
```

# Resize-Partition

## Definición

Redimensiona una partición y el sistema de ficheros que contiene

### Sintaxis

```powershell
Resize-Partition [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando están:

- `-DiskID`. Identifica el ID de la partición que se quiere redimensionar.
- `-DiskNumber`. Identifica el número del disco en el que está la partición.
- `-DriveLetter`. Indica el volumen al que pertenece la partición.

## Ejemplos de uso

Para redimensionar una partición a un nuevo tamaño

```powershell
Resize-Partition -DiskNumber 1 -PartitionNumber 3 -Size 8GB
```

Para aumentar una partición al máximo tamaño posible

```powershell
Resize-Partition -DiskNumber 1 -PartitionNumber 3 -Size $size.SizeMax
```

# Get-PartitionsupportedSize

## Definición

Devuelve información sobre el tamaño de las particiones soportados por cada disco

### Sintaxis

```powershell
Get-PartitionsupportedSize [opciones]
```

## Opciones

Algunas de las opciones más relevantes de este comando son:

- `-DiskId`. Identifica el ID del disco que se quiere inspeccionar.
- `-DiskNumber`. Identifica el número del disco que se quiere inspeccionar.
- `-PartitionaNumber`. Identifica el número de la partición que se quiere analizar.

## Ejemplos de uso

Para conocer el tamaño máximo que puede alcanzar una partición:

```powershell
Get-PartitionSupportedSizes -DiskNumber 0 -PartitionNumber 4
```

# New-Partition

## Definición

Crea una partición en un disco existente

### Sintaxis

```powershell
New-Partition [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando están:

- `-DiskId`. Identifica el ID del disco que se quiere particionar.
- `-DiskNumber`. Identifica el número del disco que se quiere particionar.
- `-Size`. Indica el tamaño de la nueva partición.
- `-AssingDriveLetter`. Asigna una letra a la nueva partición automáticamente.
- `-UseMaximunSize`. Usa todo el espacio disponible en el disco para la nueva partición.

## Ejemplos de uso

Para crear una nueva partición de un tamaño determinado

```powershell
New-Partition -DiskNumber 1 -Size 512MB
```

Para crear una nueva partición que use todo el espacio disponible en un disco

```powershell
New-Partition -DiskNumber 1 -UseMaximumSize
```
Para crear una nueva partición y asignarle una letra automáticamente

```powershell
New-Partition -DiskNumber 1 -AssignDirveLetter
```

# Initialize-Disk

## Definición

Crea una tabla de particiones en un disco para inicializarlo

### Sintaxis

```powershell
Initialize-Disk [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando están:

- `-Number`. Indica el número del disco.
- `-PartitionStyle`. Determina el tipo de tabla de particionado.
- `-VirtualDisk`. Inicializa un disco virtual.

## Ejemplos de uso

Para crear una tabla de particiones GPT en un disco

```powershell
Initialize-Disk -Number 1 -PartitionStyle GPT
```

# Remove-Partition

## Definición

Elimina una partición

### Sintaxis

```powershell
Remove-Partition [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando están:

- `-DiskNumber`. Indica el número del disco.
- `-DriveLetter`. Indica la letra del volumen.
- `-PartitionNumber`. Indica el número de la partición.

## Ejemplos de uso

Para eliminar una partición

```powershell
Remove-Partition -DiskNumber 1 -PartitionNumber 4
```

# Format-Volume

## Definición

Da formato a un volumen

### Sintaxis

```powershell
Format-Volume [opciones]
```

## Opciones

Algunas de las más destacadas de este comando son:

- `-FileSystemLabel`. Añade una etiqueta al sistema de ficheros.
- `-Partition`. Indica la partición.
- `-DriveLetter`. Indica la letra del volumen.
- `-FileSystem`. Indica el sistema de ficheros con el que se formatea.

## Ejemplos de uso

Para formatear el volumen D

```powershell
Format-Volume -Driveletter D
```

Para un disco en FAT32

```powershell
Format-Volume -Driveletter F -Filesystem FAT32 -Force
```

# Add-PartitionAccessPath

## Definición

Añade una letra o un punto de montaje a una partición

### Sintaxis

```powershell
Add-PartitionAccessPath [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando se encuentran:

- `-DiskNumber`. Indica el número del disco.
- `-PartitionNumber`. Indica el número de la partición.
- `-AccessPath`. Indica le letra que se le asigna a la partición

## Ejemplos de uso

Añadir una letra a una partición

```powershell
Add-PartitionAccessPath -DiskNumber 1 -PartitionNumber 2 -AccesPath F:
```

# Set-Disk

## Definición

Actualiza los atributos de los discos duros del sistema

### Sintaxis

```powershell
Set-Disk [opciones]
```

## Opciones

Algunas de las opciones más relevantes de este comando son:

- `-Number`. Indica el número de un disco.
- `-Path`. Indica la ruta o letra de un disco.
- `-IsOffline`. Indica que un disco no está activado.
`- -IsReadOnly`. Indica que un disco es de sólo lectura.

## Ejemplos de uso

Para poner un disco en activo

```powershell
Set-Disk -Number 5 -IsOffline $False
```

Para hacer que se pueda escribir en un disco que es de sólo lectura

```powershell
Set-Disk -Number 4 -IsReadOnly $False
```

Para poner un disco offline

```powershell
Set-Disk -Number 1 -IsOffline $True
```

# Clear-Disk

## Definición

Elimina la información de la tabla de particiones de un volumen. También elimina la información que contiene el disco.

### Sintaxis3

```powershell
Clear-Disk [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando están:

- `-Number`. Indica el número del disco.
- `-RemoveData`. Elimina el contenido del disco.
- `-RemoveOEM`. Elimina las particiones de tipo OEM de la tabla de particiones.

## Ejemplos de uso

Para eliminar la tabla de particiones de un disco

```powershell
Clear-Disk -Number 1
```

Para eliminar la tabla de particiones y el contenido de un disco que ya contiene datos

```powershell
Clear-Disk -Number 1 -RemoveData
```

# Optimize-Volume

## Definición

Optimiza un volumen

### Sintaxis

```powershell
Optimize-Volume [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando están:

- `-DirveLetter`. Para indicar la letra del volumen.
- `-Analyz`. Para analizar un volumen.
- `-Defrag`. Para desfragmentar un volumen.
- `-FileSystemLabel`. Para indicar la etiquete del sistema de ficheros.

## Ejemplos de uso

Para analizar el estado de optimización de un disco.

```powershell
Optimize-Volume -DriveLetter C -Analyze -Verbose
```

Para desfragmentar un volumen.

```powershell
Optimize-Volume -DriveLetter C -Defrag -Verbose
```

# Repair-Volume

## Definición

Analiza y repara volúmenes.

### Sintaxis

```powershell
Repair-Volume [opciones]
```

## Opciones

Entre las opciones más relevantes de este comando están:

- `-FileSystemLabel`. Indica la etiqueta el sistema de ficheros.
- `-DriveLetter`. Indica la letra del volumen
- `-OfflineScanAndFix`. Modo de reparación en el que se analiza y repara un disco que no está montado en el sistema.
- `-Scan`. Modo en el que se analiza un disco. Puede estar online en el sistema.
- `-SpotFix`. Modo en el que se pone un disco offline por un breve periodo de tiempo durante el que se reparan los errores identificados.

## Ejemplos de uso

Para analizar un volumen.

```powershell
Repair-Volume -DriveLetter C -Scan
```

Para poner un volumen en modo offline y reparar todos sus errores.

```powershell
Repair-Volume -DriveLetter F -OfflineScanAndFix
```

Para reparar de forma rápida y puntual un error concreto en un volumen.

```powershell
Repair-Volume -DriveLetter F -SpotFix
```

# Get-VirtualDisk

## Definición

Lista todos los discos virtuales.

### Sintaxis

```powershell
Get-VirtualDisk [opciones]
```

## Opciones

Algunas opciones de este comando son:

- `-FriendlyName`. Indica el nombre comprensible del disco virtual
- `-Name`. Indica el nombre del disco virutal
- `-IsSnapshot`. Indica si es una snapshot

## Ejemplos de uso

Para mostrar la lista de discos virtuales

```powershell
Get-VirtualDisk
```

# New-VirtualDisk

## Definición

Crea un nuevo disco virtual en el espacio de almacenamiento indicado.

### Sintaxis

```powershell
New-VirtualDisk [opciones]
```

## Opciones

Algunas opciones de este comando son:

- `-StoragePoolName`. Indica el nombre del almacenamiento.
- `-FriendlyName`. Indica el nombre comprensible.
- `-Size`. Indica el tamaño
- `-UseMaximumSize`. Usa todo el tamaño disponible
- `-PhysicalDisksToUse`. Indica los discos físicos en los que se almacena la información del disco virtual.
- `-PhysicalDiskRedundancy`. Indica el tipo de redundancia entre los discos físicos que forman parte del disco virtual.

## Ejemplos de uso

Para crear un disco duro virtual.

```powershell
New-VirtualDisk -StoragePoolFriendlyName Datos -FriendlyName Datos -Size 5GB
```

# Set-VirtualDisk

## Definición

Modifica los atributos de un disco virtual existente.

### Sintaxis

```powershell
Set-VirtualDisk [opciones]
```

## Opciones

Algunas opciones de este comando son:

- `-NewFriendlyName`. Indica el nuevo nombre comprensible del disco virtual.
- `-Usage`. Indica el tipo de uso del disco virtual.
- `-OtherUsageDescription`. Añade información sobre el uso del disco virtual.
- `-PhysicalDiskRedundancy`. Indica el tipo de redundancia de los discos físicos que forman parte del disco virtual.

## Ejemplos de uso

Para cambiar el nombre comprensible de un disco virtual.

```powershell
Set-VirtualDisk -FriendlyName Datos -NewFriendlyName DatosActualizados
```

# Resize-VirtualDisk

## Definición

Redimensiona un disco virtual

### Sintaxis

```powershell
Resize-VirtualDisk [opciones]
```

## Opciones

Algunas opciones de este comando son:

- `-FriendlyName`. Indica el nombre comprensible del disco.
- `-Name`. Indica el nombre del disco.
- `-Size`. Indica el tamaño del disco.

## Ejemplos de uso

Para redimensionar un disco virtual

```powershell
Resize-VirtualDisk -FirendlyName Datos -Size (8GB)
```

# Show-VirtualDisk

## Definición

Hace que un disco virtual esté disponible.

### Sintaxis

```powershell
Show-VirtualDisk [opciones]
```

## Opciones

Algunas opciones de este comando son:

- `-FriendlyName`. Indica el nombre comprensible del disco.
- `-Name`. Indica el nombre del disco virtual.
- `-TargetPortAddress`. Indica la dirección del puerto de destino.
- `-InitiatorAddress`. Indica la dirección de origen.

## Ejemplos de uso

Para hacer un disco virtual accesible a la máquina local.

```powershell
$initiatorport = (Get-InitiatorPort)
$targetport = (Get-TrgetPort)
Show-VirtualDisk -FriendlyName “Datos” -TargetPortAddress $targetport.NodeAddress -InitiatorAddress $initiatorport.NodeAddress
```