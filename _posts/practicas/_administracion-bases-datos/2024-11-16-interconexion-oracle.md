---
title:  "Interconexión entre dos servidores Oracle"
date:   2024-11-16 01:00:00 +0200
categories: administracion-bbdd
tag: [bases de datos, oracle, interconexion de servidores, administracion-bbdd, Administración de Sistemas Gestores de Bases de Datos]
excerpt: Este post constituye una breve guía con los pasos a seguir para establecer una interconexión entre dos servidores Oracle
---

# Enlace entre dos servidores Oracle

## Configuración de acceso remoto al servidor

Antes de configurar la conexión entre dos servidores Oracle es necesario garantizar que es posible conectarse de forma remota desde un cliente a cada uno de ellos. Para permitir este tipo de conexiones hay un fichero que es imprescindible configurar: el fichero listener.ora.

Este fichero se encuentra en el directorio /opt/oracle/homes/OraDBHome21cEE/network/admin/listener.ora y contiene la información de la interfaz y el puerto por el que escucha el servidor. En el caso del servidor 1, el contenido de este fichero es el siguiente:

```
LISTENER =
   (DESCRIPTION_LIST =
     (DESCRIPTION =
       (ADDRESS = (PROTOCOL = TCP)(HOST = oracleserver)(PORT = 1521))
       (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
     )
   )

 SID_LIST_LISTENER=
   (SID_LIST=
     (SID_DESC=
       (GLOBAL_DBNAME=ORCLCDB)
       (ORACLE_HOME=/opt/oracle/product/21c/dbhome_1)
       (SID_NAME=ORCLCDB))
   )
```

Para poder indicar en el host el nombre del servidor en lugar de su dirección IP es necesario también añadir el el fichero /etc/hosts una línea en la que se asocien ambos datos.

```
127.0.0.1	localhost
127.0.1.1	oracleserver
::1		localhost ip6-localhost ip6-loopback
ff02::1		ip6-allnodes
ff02::2		ip6-allrouters
10.0.0.179	oracleserver
```

Con esta configuración, ya es posible conectarse desde un cliente remoto.

```
debian@oracle2:~$ sqlplus usuario/usuario@//10.0.0.179:1521/ORCLCDB

SQL*Plus: Release 12.2.0.1.0 Production on Sun Oct 20 11:06:40 2024

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Hora de Ultima Conexion Correcta: Dom Oct 20 2024 11:06:24 +00:00

Conectado a:
Oracle Database 21c Enterprise Edition Release 21.0.0.0.0 - Production

SQL>
```

Adicionalmente, si el en fichero tnsnames del cliente remoto se añade una configuración similar a esta, la cadena de conexión se hace más sencilla. Este fichero se encuentra en el directorio /opt/oracle/homes/OraDBHome21cEE/network/admin/tnsnames.ora (ver [resolución de errores](#resolución-de-errores)).

```
ORCLCDB=
        (DESCRIPTION =
              (ADDRESS_LIST =
                (ADDRESS = (PROTOCOL = TCP)(HOST = 10.0.0.179)(PORT = 1521))
              )
         (CONNECT_DATA =
                (SERVICE_NAME = ORCLCDB)
         )
        )
```

De esta manera, el cliente ya tiene acceso al servidor indicando, simplemente, el nombre del servicio configurado en el fichero tnsnames.ora.

```
debian@oracle2:~$ sqlplus usuario/usuario@ORCLCDB

SQL*Plus: Release 12.2.0.1.0 Production on Sun Oct 20 11:24:29 2024

Copyright (c) 1982, 2016, Oracle.  All rights reserved.

Hora de Ultima Conexion Correcta: Dom Oct 20 2024 11:08:24 +00:00

Conectado a:
Oracle Database 21c Enterprise Edition Release 21.0.0.0.0 - Production

SQL>
```

## Configuración del segundo servidor

Ahora que el primer servidor es accesible desde otro cliente de la red local, es momento de configurar el segundo servidor. Para facilitar el proceso de configuración previa a la instalación del servidor, se puede usar un [script de configuración](https://github.com/fjhuete/instalacion_oracle) y posteriormente se puede instalar un paquete .deb convertido usando la herramienta alien a partir del paquete .rpm que distribuye Oracle.

```
debian@oracle2:~$ sudo dpkg -i oracle-database-ee-21c_1.0-2_amd64.deb 
(Leyendo la base de datos ... 27566 ficheros o directorios instalados actualmente.)
Preparando para desempaquetar oracle-database-ee-21c_1.0-2_amd64.deb ...
ln: fallo al crear el enlace simbólico '/bin/awk': El fichero ya existe
Desempaquetando oracle-database-ee-21c (1.0-2) ...
Configurando oracle-database-ee-21c (1.0-2) ...
[INFO] Executing post installation scripts...
[INFO] Oracle home installed successfully and ready to be configured.
To configure a sample Oracle Database you can execute the following service configuration script as root: /etc/init.d/oracledb_ORCLCDB-21c configure
Procesando disparadores para libc-bin (2.36-9+deb12u8) ...
```

Antes de configurar el nuevo servicio se añaden al fichero .bashrc las variables necesarias para el funcionamiento de Oracle.

```
export ORACLE_HOME=/opt/oracle/product/21c/dbhome_1
export ORACLE_SID=ORCLCDB
export ORACLE_BASE=/opt/oracle
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
export PATH=$ORACLE_HOME/bin:$PATH
export NLS_LANG=SPANISH_SPAIN.UTF8
```

Y se ejecuta como root el script de configuración que indica el instalador de Oracle.

```
debian@oracle2:~$ sudo /etc/init.d/oracledb_ORCLCDB-21c configure
Configuring Oracle Database ORCLCDB.
Preparar para funcionamiento de base de datos
8% completado
Copiando archivos de base de datos
31% completado
Creando e iniciando instancia Oracle
32% completado
36% completado
40% completado
43% completado
46% completado
Terminando creación de base de datos
51% completado
54% completado
Creando Bases de Datos de Conexión
58% completado
77% completado
Ejecutando acciones posteriores a la configuración
100% completado
Creación de la base de datos terminada. Consulte los archivos log de /opt/oracle/cfgtoollogs/dbca/ORCLCDB
 para obtener más información.
Información de Base de Datos:
Nombre de la Base de Datos Global:ORCLCDB
Identificador del Sistema (SID):ORCLCDB
Para obtener información detallada, consulte el archivo log "/opt/oracle/cfgtoollogs/dbca/ORCLCDB/ORCLCDB.log".

Database configuration completed successfully. The passwords were auto generated, you must change them by connecting to the database using 'sqlplus / as sysdba' as the oracle user.
```
Para poder acceder a sqlplus con el usuario debian es necesario que esté incluido en el grupo dba.

```
sudo usermod -a -G dba debian
```

## Configuración de la conexión entre servidores

Como en el caso del otro servidor, para permitir la conexión remota desde otro equipo de la red también se debe configurar el fichero listener.ora. En el caso de este segundo servidor, su contenido es el siguiente:

```
LISTENER =
   (DESCRIPTION_LIST =
     (DESCRIPTION =
       (ADDRESS = (PROTOCOL = TCP)(HOST = oracle2)(PORT = 1521))
       (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
     )
   )

 SID_LIST_LISTENER=
   (SID_LIST=
     (SID_DESC=
       (GLOBAL_DBNAME=ORCLCDB)
       (ORACLE_HOME=/opt/oracle/product/21c/dbhome_1)
       (SID_NAME=ORCLCDB))
   )
```

Y para poder usar el hostname en el parámetro host, el fichero /etc/hosts tiene que recoger la IP a la que corresponde ese hostname.

```
127.0.0.1	localhost
127.0.1.1	oracle2
::1		localhost ip6-localhost ip6-loopback
ff02::1		ip6-allnodes
ff02::2		ip6-allrouters
10.0.0.26	oracle2
```

Tras realizar esta configuración en el servidor 2 ya se puede acceder a la base de datos recién creada desde el servidor 1.

```
debian@oracleserver:~$ sqlplus dos/dos@//10.0.0.26:1521/ORCLCDB

SQL*Plus: Release 21.0.0.0.0 - Production on Dom Oct 20 12:11:00 2024
Version 21.3.0.0.0

Copyright (c) 1982, 2021, Oracle.  All rights reserved.

Hora de Última Conexión Correcta: Dom Oct 20 2024 12:10:38 +00:00

Conectado a:
Oracle Database 21c Enterprise Edition Release 21.0.0.0.0 - Production
Version 21.3.0.0.0

SQL> 
```

## Demostración del funcionamiento de la interconexión entre servidores Oracle

Para demostrar el funcionamiento de la interconexión entre servidores se usa, en este caso, el esquema Scott. En el servidor 1 se crea y rellena la tabla de departamentos.

```
SQL> connect uno/uno
Conectado.
SQL> CREATE TABLE DEPT
  2  (
  3   DEPTNO NUMBER(2),
  4   DNAME VARCHAR2(14),
  5   LOC VARCHAR2(13),
  6   CONSTRAINT PK_DEPT PRIMARY KEY (DEPTNO)
  7  );

Tabla creada.

SQL> INSERT INTO DEPT VALUES (10, 'ACCOUNTING', 'NEW YORK');
INSERT INTO DEPT VALUES (20, 'RESEARCH', 'DALLAS');
INSERT INTO DEPT VALUES (30, 'SALES', 'CHICAGO');

1 fila creada.

SQL> 
1 fila creada.

SQL> 
1 fila creada.

SQL> INSERT INTO DEPT VALUES (40, 'OPERATIONS', 'BOSTON');

1 fila creada.
```

En el servidor 2 se crea y rellena la tabla de empleados.

```
SQL> connect dos/dos
Conectado.
SQL> CREATE TABLE EMP
  2  (
  3   EMPNO NUMBER(4),
  4   ENAME VARCHAR2(10),
  5   JOB VARCHAR2(9),
  6   MGR NUMBER(4),
  7   HIREDATE DATE,
  8   SAL NUMBER(7, 2),
  9   COMM NUMBER(7, 2),
 10   DEPTNO NUMBER(2),
 11   CONSTRAINT PK_EMP PRIMARY KEY (EMPNO)
 12  );

Tabla creada.

SQL> INSERT INTO EMP VALUES(7369, 'SMITH', 'CLERK', 7902,TO_DATE('17-DIC-1980', 'DD-MON-YYYY'), 800, NULL, 20);
INSERT INTO EMP VALUES(7369, 'SMITH', 'CLERK', 7902,TO_DATE('17-DIC-1980', 'DD-MON-YYYY'), 800, NULL, 20);
INSERT INTO EMP VALUES(7499, 'ALLEN', 'SALESMAN', 7698,TO_DATE('20-FEB-1981', 'DD-MON-YYYY'), 1600, 300, 30);
INSERT INTO EMP VALUES(7521, 'WARD', 'SALESMAN', 7698,TO_DATE('22-FEB-1981', 'DD-MON-YYYY'), 1250, 500, 30);
INSERT INTO EMP VALUES(7566, 'JONES', 'MANAGER', 7839,TO_DATE('2-ABR-1981', 'DD-MON-YYYY'), 2975, NULL, 20);

1 fila creada.

SQL> 
1 fila creada.

SQL> 
1 fila creada.

SQL> 
1 fila creada.

SQL> INSERT INTO EMP VALUES(7654, 'MARTIN', 'SALESMAN', 7698,TO_DATE('28-SEP-1981', 'DD-MON-YYYY'), 1250, 1400, 30);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7698, 'BLAKE', 'MANAGER', 7839,TO_DATE('1-MAY-1981', 'DD-MON-YYYY'), 2850, NULL, 30);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7782, 'CLARK', 'MANAGER', 7839,TO_DATE('9-JUN-1981', 'DD-MON-YYYY'), 2450, NULL, 10);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7788, 'SCOTT', 'ANALYST', 7566,TO_DATE('09-DIC-1982', 'DD-MON-YYYY'), 3000, NULL, 20);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7839, 'KING', 'PRESIDENT', NULL,TO_DATE('17-NOV-1981', 'DD-MON-YYYY'), 5000, NULL, 10);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7844, 'TURNER', 'SALESMAN', 7698,TO_DATE('8-SEP-1981', 'DD-MON-YYYY'), 1500, 0, 30);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7876, 'ADAMS', 'CLERK', 7788,TO_DATE('12-ENE-1983', 'DD-MON-YYYY'), 1100, NULL, 20);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7900, 'JAMES', 'CLERK', 7698,TO_DATE('3-DIC-1981', 'DD-MON-YYYY'), 950, NULL, 30);

1 fila creada.

SQL> INSERT INTO EMP VALUES(7902, 'FORD', 'ANALYST', 7566,TO_DATE('3-DIC-1981', 'DD-MON-YYYY'), 3000, NULL, 20);

1 fila creada.
```

Desde alguno de los dos servidores se crea la interconexión al otro.

```
SQL> create database link empleados
  2  connect to dos
  3  identified by dos
  4  using 'ORCL2';

Enlace con la base de datos creado.
```

Tras establecer esta configuración para la interconexión entre los servidores de bases de datos de Oracle aparece este error al intentar consultar la tabla de un servidor desde el otro.

```
SQL> SELECT ename from emp@empleados;
SELECT ename from emp@empleados
                      *
ERROR en línea 1:
ORA-12504: TNS:el listener no ha recibido el SERVICE_NAME en CONNECT_DATA
```

La interconexión también se puede crear haciendo referencia a la IP del servidor.

```
SQL> create database link empleados connect to dos identified by dos using '//10.0.0.26:1521/ORCLCDB';

Enlace con la base de datos creado.
```

Usando este formato en la cadena de conexión ya es posible hacer consultas desde un servidor de la base de datos usando la información almacenada en el otro.

```
SQL> select ename
  2  from emp@empleados
  3  where deptno = (select deptno
  4  		from dept
  5  		where dname = 'SALES');

ENAME
------------------------------
ALLEN
WARD
MARTIN
BLAKE
TURNER
JAMES

6 filas seleccionadas.
```

## Resolución de errores

El error mencionado anteriormente se produce por un problema con la ubicación del fichero tnsnames.ora. Este fichero se instala en diferentes ubicaciones del servidor al instalar Oracle. Sin embargo, el servidor sólo usa uno de estos ficheros para establecer las conexiones. En este caso el fichero que se estaba editando (/opt/oracle/homes/OraDBHome21cEE/network/admin/tnsnames.ora) no coincidía con el que estaba usando el servidor (/opt/oracle/product/21c/dbhome_1/network/admin/tnsnames.ora).

Solucionar este problema es tan sencillo como copiar el fichero de configuración en la ubicación correcta.

```
sudo cp /opt/oracle/homes/OraDBHome21cEE/network/admin/tnsnames.ora /opt/oracle/product/21c/dbhome_1/network/admin/tnsnames.ora
```

Tras ubicar correctamente el fichero, ambos servidores de Oracle se pueden comunicar usando el nombre configurado en el fichero tnsnames.ora.

```
SQL> drop database link empleados;

Enlace con la base de datos borrado.

SQL> create database link empleados
  2  connect to dos
  3  identified by dos
  4  using 'ORCL2';

Enlace con la base de datos creado.

SQL> select ename
  2  from emp@empleados
  3  where deptno = (select deptno
  4  		from dept
  5  		where dname = 'SALES');

ENAME
------------------------------
ALLEN
WARD
MARTIN
BLAKE
TURNER
JAMES

6 filas seleccionadas.
```