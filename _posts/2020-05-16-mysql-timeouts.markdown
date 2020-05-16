---
layout: post
title:  "MySQL y los Timeouts"
date:   2020-05-15
categories: MySQL
published: true
tags: [mysql, timeout]
---

Suele pasar mucho con los de **business intelligence, data science y sus variantes**, puede deberse a varias razones, vamos al grano:

- Si te conectas desde tu casa u oficina al cloud para traer información, la realidad es que la intermitencia no la puedes controlar, puede ser por el ISP, o por la VPN o quien sabe que.
- Volúmenes de datos muy grandes. Haz que la query retorne volúmenes más pequeños, en vez de todo el mes, que sea por día.
- Tiempos de respuesta muy elevados, te falta un indice ó por la misma razón debes hacer lo anterior, traerte trozos pequeños y razonables de datos, o algún bloqueo.
- Estas tratando con una ddbb transaccional por lo que los tiempos están ajustados para ello y más si es una aplicación web que no debe tardar más de 30s en responder por ejemplo porque sino el hilo de ejecución del web server muere.

Se podría configurar el cliente o tu script o programa para que cambie esos tiempos a la hora de establecer la conexión y recibir los datos, pero antes debes saber que hay definido y luego ver que ajustas. Haz la siguiente query:

```sql
SELECT @@net_write_timeout, @@wait_timeout;
```
Cada vez que el cliente se conecta hereda una serie de configuraciones globales, pero también puedes ajustar algunas de ellas en tu sesión, como lo vamos hacer ahora. Cada una de esas variables significa algo:

- **net_write_timeout:** Indica la cantidad de segundos en que el servidor escribe al cliente y debe esperar para abortar la conexión.
- **wait_timeout:** Indica la cantidad de segundos sin actividad que debe esperar para abortar la conexión.
Como ajustas wait_timeout:

```sql
SET SESSION wait_timeout = 300;
SELECT * FROM book WHERE year BETWEEN 2000 AND 2001;
```

Esto es bastante de probar y ajustar hasta conseguir los valores adecuados, recuerda que la cache y el buffer pool también pueden jugartela con los tiempos de respuesta.

Las variables las puedes cambiar en su conexión / session de MySQL sin perjudicar el resto del comportamiento del cluster.

Adicionalmente, recomiendo hacer lecturas de datos sin generar bloqueos que pueda comprometer la replicación o una transacción entre otras cosas, para ello debes definir el nivel de aislamiento transaccional:

```sql
SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED ;
SELECT * FROM book WHERE year BETWEEN 2000 AND 2001;
```

Fin.
