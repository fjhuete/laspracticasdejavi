---
title:  "Configuración del balanceador de carga HAProxy"
date:   2024-11-24 01:00:00 +0200
categories: servicios
tag: [Balanceador de carga, HAProxy, Apache2, Nginx, Administración de Sistemas, servicios, Servicios de Red e Internet]
excerpt: En esta entrada se documenta el proceso de configuración y uso del balanceador de carga HAProxy.
---

# Configuración de HAProxy como balanceador de carga

Este escenario está formado por un balanceador de carga que reparte el tráfico entre dos servidores web que sirven una aplicación web escrita en PHP.

En el equipo que funciona como balanceador de carga se instala la herramienta `HAproxy`.

```
sudo apt install haproxy
```

Para configurar el balanceador de carga de manera que reparta la carga entre los servidores de forma equitativa se añade la siguiente configuración en el fichero /etc/haproxy/haproxy.cfg

```
frontend servidores_web
	bind *:80 
	mode http
	stats enable
	stats uri /ha_stats
	stats auth cda:cda
	default_backend servidores_web_backend

backend servidores_web_backend
	mode http
	balance roundrobin
	server backend1 192.168.100.100:80 check
	server backend2 192.168.100.101:80 check
```

Para poder acceder a la aplicación web se tiene que añadir la resolución estática al ficheo /etc/hosts del equipo local.

```
192.168.121.120	www.example.org
```

De esta forma, al acceder de manera consecutiva al navegador, cada vez el balanceador redirigirá la conexión a uno de los dos servidores web del escenario.

Además, la herramienta `haproxy` ofrece una interfaz gráfica sencilla en la que muestra algunas estadísitcas del funcionamiento del balanceador de carga. Esta herramienta gráfica es accesible a través de una navegador web en la dirección del balanceador de carga `www.example.org/ht_stats`.

Aunque los equipos cliente acceden al balanceador de carga, las peticiones que llegan a los servidores web lo hacen desde el balanceador de carga, por tanto, todas ellas muestran como IP de origen la IP de la red local del balanceador de carga y no las IP públicas desde las que acceden los clientes.

# Comprobar el rendimiento del balanceador del carga

Para comprobar el rendimiento del balanceador de carga se pueden usar herramientas como `ab` y `hatop`. 

```
sudo apt install apache2-utils hatop
```

Con `ab`(Apache Benchmark) se pueden hacer peticiones en lote a un servidore web y la salida del comando muestra el rendimiento del servidor.

```
vagrant@balanceador:~$ ab -n 1000 -c 100 http://www.example.org/app.php
This is ApacheBench, Version 2.3 <$Revision: 1913912 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking www.example.org (be patient)
Completed 100 requests
Completed 200 requests
Completed 300 requests
Completed 400 requests
Completed 500 requests
Completed 600 requests
Completed 700 requests
Completed 800 requests
Completed 900 requests
Completed 1000 requests
Finished 1000 requests


Server Software:        Apache/2.4.62
Server Hostname:        www.example.org
Server Port:            80

Document Path:          /app.php
Document Length:        115 bytes

Concurrency Level:      100
Time taken for tests:   19.640 seconds
Complete requests:      1000
Failed requests:        0
Non-2xx responses:      1000
Total transferred:      283000 bytes
HTML transferred:       115000 bytes
Requests per second:    50.92 [#/sec] (mean)
Time per request:       1964.009 [ms] (mean)
Time per request:       19.640 [ms] (mean, across all concurrent requests)
Transfer rate:          14.07 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    2   4.9      0      22
Processing:   104 1903 875.5   2015    4708
Waiting:       83 1903 875.6   2015    4708
Total:        104 1905 874.0   2020    4708

Percentage of the requests served within a certain time (ms)
  50%   2020
  66%   2426
  75%   2584
  80%   2672
  90%   2967
  95%   3284
  98%   3648
  99%   3867
 100%   4708 (longest request)
```

La herramienta `hatop` permite comprobar el rendimiento de los servidores, ver su estado, sus estadísticas y, además, permite interactuar con el balanceador de carga para conectar o desconectar algunos de los servidores web del backend para, por ejemplo, desconectarlos del balanceador y ponerlos en mantenimiento.

```
HATop version 0.8.2                                   Wed Nov 20 11:29:10 2024 

  HAProxy Version: 2.6.12-1+deb12u1  (released: 2023/12/PID: 382 (proc 1)

         Node: balanceador (uptime 0d 0h06m47s)

        Pipes: [                                                       0/0]
  Connections: [                                                  0/262124]

  Procs:   1   Tasks:    14    Queue:     0    Proxies:   2   Services:    4

NAME            W STATUS CHECK   ACT BCK  QCUR  QMAX   SCUR   SMAX   SLIM   STOT

>>> servidores_web
FRONTEND        0 OPEN             0   0     0     0      0    100 262124   1000

>>> servidores_web_backend
backend1        1 UP     L4OK      1   0     0     0      0      0      0      0
backend2        1 UP     L4OK      1   0     0     0      0    100      0   1000
BACKEND         2 UP               2   0     0     0      0    100  26213   1000



 1-STATUS  2-TRAFFIC  3-HTTP  4-ERRORS  5-CLI      ENTER=MENU SPACE=SEL [#3/#1] 
```