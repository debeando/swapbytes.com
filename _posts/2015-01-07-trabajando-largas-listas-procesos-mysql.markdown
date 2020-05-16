---
layout: post
title:  "Trabajando con largas listas de procesos en MySQL"
date:   2015-01-07
categories: MySQL
---

Los fans de la terminal nos encontramos frecuentemente con algunas limitaciones por el tamaño del monitor y todo el contenido, en este caso hablo de listar y filtrar entre muchos rows en MySQL sin la opción WHERE como; **"SHOW PROCESSLIST;"** y **"SHOW TABLES;"**. Generalmente necesitamos buscar algo en largas listas para; matar procesos, verificar conexiones, consultas, etc... y como podemos hacer para mostrar los que nos interesa?.

Lo podemos cambiar estando dentro de la consola de MySQL, por ejemplo; Necesito listar todas las conexiones que sean distintas al estado **Sleep**:

```SQL
mysql> \P grep -v Sleep
PAGER set to 'grep -v Sleep'
mysql> SHOW PROCESSLIST;
+--------+---------+-----------+------+---------+-------+-------+------------------+
| Id     | User    | Host      | db   | Command | Time  | State | Info             |
+--------+---------+-----------+------+---------+-------+-------+------------------+
| 109530 | root    | localhost | NULL | Query   |     0 | NULL  | show processlist |
```

Listo, así de simple o lo hacemos más complejo? ahora necesito saber cuales son las conexiones dormidas, y lo ordenamos de forma descendente, nos quedaría así:

```SQL
mysql> \P grep Sleep | sort -t \| -k 7 -n -r
PAGER set to 'grep Sleep | sort -t \| -k 7 -n -r'
mysql> SHOW PROCESSLIST;
| 7550471 | app | 10.65.54.110:53481   | demo  | Sleep | 9559 |      | NULL |
| 7550429 | app | 10.65.54.110:53479   | demo  | Sleep | 8877 |      | NULL |
| 7560240 | app | 10.33.181.84:57960   | demo  | Sleep | 2029 |      | NULL |
| 7560214 | app | 10.33.181.84:57955   | demo  | Sleep | 1058 |      | NULL |
| 7560993 | app | 10.33.181.84:58083   | demo  | Sleep |  616 |      | NULL |
| 7548835 | app | 10.36.142.140:39371  | demo  | Sleep |  225 |      | NULL |
| 7548697 | app | 10.36.142.140:39350  | demo  | Sleep |  211 |      | NULL |
| 7555321 | app | 10.65.19.225:45810   | demo  | Sleep |  208 |      | NULL |
| 7562608 | app | 10.65.19.225:47517   | demo  | Sleep |  109 |      | NULL |
| 5517613 | app | localhost:25380      | mysql | Sleep |    7 |      | NULL |
| 7562775 | app | 10.250.249.182:42425 | demo  | Sleep |    5 |      | NULL |
| 7562774 | app | 10.250.249.182:42424 | demo  | Sleep |    5 |      | NULL |
| 7562679 | app | 10.250.249.182:42370 | demo  | Sleep |    1 |      | NULL |
```

El ejemplo habla por si solo, y como podemos observar, simplemente estamos usando los comandos básicos de Linux, no hay mayor cosa. Un ejemplo más, si queremos listar cuales tablas quedan de respaldo cuando se hace un alter table con las [Percona Tools][1]:

```SQL
mysql> \P egrep "_*_(new|old)";
PAGER set to 'egrep "_*_(new|old)";'
mysql> SHOW TABLES;
| _users_old                         |
| _acl_new                           |
```

Si queremos quitar el filtro y ponerlo como antes, tenemos dos formas;

* **La primera:**

```SQL
mysql> \P
Default pager wasn't set, using stdout.
```

* **La segunda:**

```SQL
mysql> NOPAGER;
PAGER set to stdout
```

Listo, ya somos un poco más hábiles con el comando **mysql**.

[1]: http://www.percona.com/software/percona-toolkit/
