---
layout: post
title:  "MySQL Balanceo de Carga & Failover con HAProxy"
date:   2015-01-11
categories: MySQL
---

La Base de Datos es un servicio muy importante que debe estar operativo el 99.9999%, hay muchas arquitecturas para lograr esto, todo depende de las necesidades, hoy les explico la más básica usando un Master y dos Slaves con AWS y HAProxy.

Los Slaves se usan para las lecturas, todo el tráfico que es de disposición **NO** inmediata (<1s) debe ser accedido desde los Slaves, la idea es hacer un balanceo de carga, le quitamos peso al Master y se lo delegamos a los Slaves.

Como podemos imaginar, usar Slaves no son muy seguros, de vez en cuando se rompe por una serie de motivos, o necesitamos hacer mantenimiento y no debemos detener el servicio, entonces creamos una arquitectura tolerante a fallos como se muestra en la siguiente imagen:

![MySQL Arquitectura]({{ "assets/mysql_master_slave_failover.png" | absolute_url }})

Podemos observar que todo lo que está susceptible a fallos está duplicado. Hago una breve descripción de cada elemento que interviene en el diagrama:

* **APPS:** Las aplicaciones con una configuración especial saben dirigir el tráfico de lectura o de escritura.
* **Master:** Usamos un RDS Multi A-Z, esto permite una redundancia de servidores en multiples zonas, y entra en acción una zona u otra si una de ellas falla. Si no usamos RDS recomiendo tener una configuración Multi Master, hay una solución muy buena llamada Percona.
* **DNS:** Cada servidor se le crea un CNAME, pero uno de ellos, está definido con dos IP's, que es para los HAProxy, así tenemos redundancia, Route 53 de AWS permite hacer esto (Como los de Amazon nos quitan trabajo).
* **Slave:** Las instancias de MySQL, no requiere ninguna configuración especial.
* **HAProxy:** Éste servicio debe usar la versión 1.5 que tiene soporte para MySQL. Depende como lo configuremos, pero balancea las conexiones en Round Robin por ejemplo, también podemos definir el número máximo de conexiones, como una conexión de backup que valla contra el Master en caso de que todos los Slaves fallen.

### Cronología de un desastre:

Vamos a ver en orden que pasa si algo falla y como se resuelve:

* Si el DNS detecta que uno de los HAProxy falla, el DNS le pasa la conexión a otro HAProxy.
* Si el HAProxy detecta que un slave no responde, le pasa la conexión a otro Slave.
* Si todos los Slaves no responden, el HAProxy redirige la conexión al Master.

Para que todos los pasos anteriores sean transparentes para la aplicación, está debe tener una tolerancia a fallos de conexiones. Lo que quiero decir, es: que si una conexión es interrumpida, la vuelve a reanudar sin mostrar mensajes de error.

Es muy importante tener algún sistema de monitoring de cada cosa que está involucrado en el diagrama, sino no podemos entrar en acción rápido para prevenir algún desastre. También debe tener bien calculado y configurado el número de conexiones soportadas por cada instancia de MySQL.
