---
layout: post
title:  "MySQL y los emojis"
date:   2020-05-15
categories: MySQL
published: false
tags: [mysql, collation]
---

Esta es la típica tontería inútil que da problemas, veamos como se implementa y donde puede haber problemas, al grano:

- Debes utilizar el encoding `utf8mb4`.
- El encoding debe estar configurado en varios sitios; en las tablas a usar, en la conexión de la aplicación y en las variables de entorno. En este punto es donde suele haber problemas.
- Debes asegurarte que los datos se guardan y se recuperan bien.
- Realiza pruebas en un entorno seguro y controlado.

Si ya estas utilizando el encoding `utf8` no es problema que implementes `utf8mb4`.

Vamos a crear una tabla de ejemplo:

```SQL
```

Podemos modificar una tabla existente para que use `utf8mb4`.

```SQL
```

**ADVERTENCIA:** Cuidado con el tamano de la tabla, y el encoding diferente al `utf8`.

Es importante saber

```SQL
SELECT @@collation_connection,
       @@collation_database,
       @@collation_server,
       @@character_set_client,
       @@character_set_connection,
       @@character_set_database,
       @@character_set_filesystem,
       @@character_set_results,
       @@character_set_server,
       @@character_set_system,
       @@init_connect;
```

Por ejemplo no usamos la sentencia `SHOW VARIABLES LIKE 'character%';` y su variante porque debes ejecutarla por cada tipo de variables [collation|character] o una variable en específico, es cosa tuya.

```SQL
SELECT table_collation FROM information_schema.tables WHERE table_schema = 'demo' AND table_name = 'foo';
```


```SQL
UPDATE demo.foo SET `text` = "🚌" WHERE id = 1;
```


```SQL
select id, text, hex(text) from notes where id = 1097766;
select hex("🚌");
```

```SQL
SET character_set_client = 'utf8mb4'; SET character_set_connection = 'utf8mb4'; SET character_set_results = 'utf8mb4';
```
