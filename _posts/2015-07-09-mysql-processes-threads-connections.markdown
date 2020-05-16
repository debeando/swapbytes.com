---
layout: post
title:  "Processes, Threads & Connections"
date:   2015-02-15
categories: Base de Datos
---

Suele pasar mucho que nos confundimos con estos tres términos; procesos, hilos y conexiones (Processes, Threads & Connections), les quiero explicar un poco de que va cada uno y como están relacionados:

*   MySQL Server es un proceso de aplicación.
*   MySQL Server es multi hilo (multithreaded).
*   MySQL Server acepta multiples conexiones por TCP/IP.
*   MySQL Server dedica por cada conexión un hilo (thread).
*   Un hilo se puede llamar de vez en cuando proceso, y un proceso puede hacer referencia a una conexión.

La última parte suena confuso, y me explico; MySQL Server (mysqld) es solo un proceso que se está ejecutando en el Sistema Operativo, el cual éste proceso permite tener muchos hilos de ejecución, un hilo por cada conexión realizada con el servidor, cuando se cierra la conexión se destruye el hilo.

Crear y eliminar conexiones es una tarea costosa, porque se requiere administrar de forma dinámica una serie de recursos físicos, en especial la Memoria RAM, por eso existe la cache de hilos para optimizar las conexiones no persistentes, recuerda ver las variables: <a href="https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html#sysvar_thread_cache_size" target="_blank">thread_cache_size</a> y <a href="https://dev.mysql.com/doc/refman/5.6/en/server-status-variables.html#statvar_Threads_cached" target="_blank">Threads_cached</a>.

Si pensamos en todo esto, nos preguntamos porque el comando "**SHOW PROCESSLIST**" se llama así y no "**SHOW THREADLIST**"? Debe ser que es más fácil asociar proceso que hilo, una respuesta acertada no la sé.

Vamos a demostrar que todo esto tiene sentido en el servidor, lo primero es que debemos crear varias conexiones y las mandamos al background, cada una de las conexión ejecutas es una sentencia SQL SLEEP para esperar 120 segundos y evitar que se cierre, al terminar el tiempo de espera se cierran y así lo hacemos más divertido:

```bash
$ for i in {1..4}
do
  mysql -h localhost -u root -e 'SELECT SLEEP(120);' &
done
```

Ahora accedemos para ejecutar los comandos administrativos e ir comparando valores:

```bash
$ mysql -h localhost -u root
mysql> show processlist;
+----+------+-----------+------+---------+------+------------+-------------------+
| Id | User | Host      | db   | Command | Time | State      | Info              |
+----+------+-----------+------+---------+------+------------+-------------------+
|  5 | root | localhost | NULL | Query   |    9 | User sleep | SELECT SLEEP(120) |
|  6 | root | localhost | NULL | Query   |    9 | User sleep | SELECT SLEEP(120) |
|  7 | root | localhost | NULL | Query   |    9 | User sleep | SELECT SLEEP(120) |
|  8 | root | localhost | NULL | Query   |    9 | User sleep | SELECT SLEEP(120) |
|  9 | root | localhost | NULL | Query   |    0 | init       | show processlist  |
+----+------+-----------+------+---------+------+------------+-------------------+
```

Con este comando podemos ver cual es el ID de la conexión en la lista de procesos:

```bash
SELECT CONNECTION_ID();
```

Aquí podemos consultar el estado de las variables relacionadas a las conexiones, estás indican el número de conexiones cacheadas, conectadas, creadas y ejecutadas:

```bash
mysql> SHOW GLOBAL STATUS LIKE 'Threads%';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Threads_cached    | 0     |
| Threads_connected | 5     |
| Threads_created   | 5     |
| Threads_running   | 5     |
+-------------------+-------+
```

Al finalizar las consultas enviadas anteriormente con el loop, podemos ver de nuevo el estado de las variables del servidor, ahora se tienen cacheadas las últimas 4 conexiones, solo hay una conexión activa que es la que está ejecutando este comando, en total se han ejecutado 5 conexiones, y solo hay una en ejecución, que es ésta.

```bash
mysql> SHOW GLOBAL STATUS LIKE 'Threads%';
+-------------------+-------+
| Variable_name     | Value |
+-------------------+-------+
| Threads_cached    | 4     |
| Threads_connected | 1     |
| Threads_created   | 5     |
| Threads_running   | 1     |
+-------------------+-------+
```

## Variables

Hay un par de [variables](https://dev.mysql.com/doc/refman/5.6/en/connection-threads.html) que son para gestionar los recursos de conexión, es bueno revisarlas y asegurarse que todo está bien configurado en el servidor.
