---
title:  "Interconexión entre dos servidores Postgres"
date:   2024-11-17 01:00:00 +0200
categories: administracion-bbdd
tag: [bases de datos, PostgreSQL, interconexion de servidores, administracion-bbdd, Administración de Sistemas Gestores de Bases de Datos]
excerpt: En este post se explica brevemente cómo se puede establecer una interconexión entre dos servidores PostgreSQL
---

# Enlace entre dos servidores Postgres

## Configuración del acceso remoto al servidor Postgres

En primer lugar es necesario contar con dos servidores diferentes y que en ambos esté instalado el SGBD Postgres. Además, es necesario configurar la conexión remota en ambos casos. Para ello se editan los ficheros de configuración /etc/postgresql/15/main/pg_hba.conf y /etc/postgresql/15/main/postgresql.conf y se añaden las siguientes líneas:

```
#En el ficheero /etc/postgresql/15/main/postgresql.conf

listen_addresses = '*'

#En el fichero /etc/postgresql/15/main/pg_hba.conf

host    all             all             10.0.0.0/24             scram-sha-256
host    all             all             all                     scram-sha-256
```

Una vez que ambos servidores están configurados e instalados hay que crear los usuarios y las bases de datos en cada uno de ellos y añadirles contenido.

```
debian@postgres:~$ sudo su
root@postgres:/home/debian# su postgres
postgres@postgres:/home/debian$ psql

postgres=# create user uno with password 'uno';
CREATE ROLE
postgres=# create database bd1;
CREATE DATABASE
postgres=# grant all privileges on database bd1 to uno;
GRANT

debian@postgres:~$ psql -W bd1 uno

bd1=> create schema scott;
CREATE SCHEMA
bd1=> create table scott.dept (
  deptno integer,
  dname  text,
  loc    text,
  constraint pk_dept primary key (deptno)
);
CREATE TABLE
bd1=> create table scott.emp (
  empno    integer,
  ename    text,
  job      text,
  mgr      integer,
  hiredate date,
  sal      integer,
  comm     integer,
  deptno   integer,
  constraint pk_emp primary key (empno),
  constraint fk_mgr foreign key (mgr) references scott.emp (empno),
  constraint fk_deptno foreign key (deptno) references scott.dept (deptno)
);
CREATE TABLE
bd1=> insert into scott.dept (deptno,  dname,        loc)
       values    (10,     'ACCOUNTING', 'NEW YORK'),
                 (20,     'RESEARCH',   'DALLAS'),
                 (30,     'SALES',      'CHICAGO'),
                 (40,     'OPERATIONS', 'BOSTON');
INSERT 0 4
bd1=> insert into scott.emp (empno, ename,    job,        mgr,   hiredate,     sal, comm, deptno)
       values   (7369, 'SMITH',  'CLERK',     7902, '1980-12-17',  800, NULL,   20),
                (7499, 'ALLEN',  'SALESMAN',  7698, '1981-02-20', 1600,  300,   30),
                (7521, 'WARD',   'SALESMAN',  7698, '1981-02-22', 1250,  500,   30),
                (7566, 'JONES',  'MANAGER',   7839, '1981-04-02', 2975, NULL,   20),
                (7654, 'MARTIN', 'SALESMAN',  7698, '1981-09-28', 1250, 1400,   30),
                (7698, 'BLAKE',  'MANAGER',   7839, '1981-05-01', 2850, NULL,   30),
                (7782, 'CLARK',  'MANAGER',   7839, '1981-06-09', 2450, NULL,   10),
                (7788, 'SCOTT',  'ANALYST',   7566, '1982-12-09', 3000, NULL,   20),
                (7839, 'KING',   'PRESIDENT', NULL, '1981-11-17', 5000, NULL,   10),
                (7844, 'TURNER', 'SALESMAN',  7698, '1981-09-08', 1500,    0,   30),     
                (7934, 'MILLER', 'CLERK',     7782, '1982-01-23', 1300, NULL,   10);
      commit;
INSERT 0 14
WARNING:  there is no transaction in progress
COMMIT
```

```
debian@postgres2:~$ sudo su
root@postgres2:/home/debian# su postgres
postgres@postgres2:/home/debian$ psql

postgres=# create user dos with password 'dos';
CREATE ROLE
postgres=# create database bd2;
CREATE DATABASE
postgres=# grant all privileges on database bd2 to dos;
GRANT

debian@postgres2:~$ psql -W bd2 dos

bd2=> create schema scott;
CREATE SCHEMA
bd2=> create table scott.dept (
  deptno integer,
  dname  text,
  loc    text,
  constraint pk_dept primary key (deptno)
);
CREATE TABLE
bd2=> create table scott.emp (
  empno    integer,
  ename    text,
  job      text,
  mgr      integer,
  hiredate date,
  sal      integer,
  comm     integer,
  deptno   integer,
  constraint pk_emp primary key (empno),
  constraint fk_mgr foreign key (mgr) references scott.emp (empno),
  constraint fk_deptno foreign key (deptno) references scott.dept (deptno)
);
CREATE TABLE
bd2=> insert into scott.dept (deptno,  dname,        loc)
       values    (10,     'ACCOUNTING', 'NEW YORK'),
                 (20,     'RESEARCH',   'DALLAS'),
                 (30,     'SALES',      'CHICAGO'),
                 (40,     'OPERATIONS', 'BOSTON');
INSERT 0 4
bd2=> insert into scott.emp (empno, ename,    job,        mgr,   hiredate,     sal, comm, deptno)
       values   (7369, 'SMITH',  'CLERK',     7902, '1980-12-17',  800, NULL,   20),
                (7499, 'ALLEN',  'SALESMAN',  7698, '1981-02-20', 1600,  300,   30),
                (7521, 'WARD',   'SALESMAN',  7698, '1981-02-22', 1250,  500,   30),
                (7566, 'JONES',  'MANAGER',   7839, '1981-04-02', 2975, NULL,   20),
                (7654, 'MARTIN', 'SALESMAN',  7698, '1981-09-28', 1250, 1400,   30),
                (7698, 'BLAKE',  'MANAGER',   7839, '1981-05-01', 2850, NULL,   30),
                (7782, 'CLARK',  'MANAGER',   7839, '1981-06-09', 2450, NULL,   10),
                (7788, 'SCOTT',  'ANALYST',   7566, '1982-12-09', 3000, NULL,   20),
                (7839, 'KING',   'PRESIDENT', NULL, '1981-11-17', 5000, NULL,   10),
                (7844, 'TURNER', 'SALESMAN',  7698, '1981-09-08', 1500,    0,   30),     
                (7934, 'MILLER', 'CLERK',     7782, '1982-01-23', 1300, NULL,   10);
      commit;
INSERT 0 14
WARNING:  there is no transaction in progress
COMMIT
```

## Configuración de la interconexión entre servidores Postgres

Para permitir la interconexión entre ambos servidores es necesario crear un enlace con la orden `create extension dblink;`

```
bd1=# create extension dblink schema scott;
CREATE EXTENSION
bd2=# create extension dblink schema scott;
CREATE EXTENSION
```

Una vez que se ha habilitado el enlace en ambas bases de datos se puede acceder desde un servidor al otro para consultar la información almacenada en él.

```
bd1=> select * from dblink('dbname=bd2 host=10.0.0.47 user=dos password=dos','select * from scott.dept') as dept (deptno integer, dname text, loc text);
```

Pero la búsqueda devuelve el siguiente error:

```
ERROR:  function dblink(unknown, unknown) does not exist
```

Para solucionarlo se ejecuta esta orden:

```
set search_path to scott;
```

Al repetir la consulta, se obtiene el resultado.

```
bd1=> select * from dblink('dbname=bd2 host=10.0.0.47 user=dos password=dos','select * from scott.dept') as dept (deptno integer, dname text, loc text);
 deptno |   dname    |   loc    
--------+------------+----------
     10 | ACCOUNTING | NEW YORK
     20 | RESEARCH   | DALLAS
     30 | SALES      | CHICAGO
     40 | OPERATIONS | BOSTON
(4 rows)
```

De la misma forma, si se ejecuta la misma consulta en el servidor 2, se puede recuperar esta información del servidor 1.

```
bd2=> set search_path to scott;
SET
bd2=> select * from dblink('dbname=bd1 host=10.0.0.134 user=uno password=uno','select * from scott.dept') as dept (deptno integer, dname text, loc text);
 deptno |   dname    |   loc    
--------+------------+----------
     10 | ACCOUNTING | NEW YORK
     20 | RESEARCH   | DALLAS
     30 | SALES      | CHICAGO
     40 | OPERATIONS | BOSTON
(4 rows)
```