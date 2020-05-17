---
layout: post
title:  "MySQL y los emojis"
date:   2020-05-15
categories: MySQL
published: true
tags: [mysql, collation]
---

Esta es la típica tontería inútil que da problemas, veamos como se implementa y donde puede haber problemas, al grano, vemos todo lo que hace falta para que funcione, y los ejemplos que trataremos están hechos para saber investigar el problema y poder resolverlo:

- Debes utilizar el encoding `utf8mb4`.
- El encoding debe estar configurado en varios sitios; en las tablas a usar, en la conexión de la aplicación y en las variables de entorno. En este punto es donde suele haber problemas.
- Debes asegurarte de que los datos se guardan y se recuperan bien.
- Realiza pruebas en un entorno seguro y controlado.

Si ya estas utilizando el encoding `utf8` no es problema que implementes `utf8mb4`.

Vamos a crear primero una tabla como debe ser, luego modificar una tabla existente y luego agregamos varios registros de ejemplo:

```SQL
DROP TABLE IF EXISTS foo;
CREATE TABLE IF NOT EXISTS `foo` (
  `id` bigint(20) UNSIGNED NOT NULL AUTO_INCREMENT,
  `text` varchar(2000) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO foo (`text`) VALUES ("test case");
INSERT INTO foo (`text`) VALUES ("🚌");
```

Podemos modificar una tabla existente para que use `utf8mb4`, en el ejemplo pongo como crear la tabla y luego como modificarla:

```SQL
DROP TABLE IF EXISTS foo;
CREATE TABLE IF NOT EXISTS `foo` (
  `id` bigint(20) UNSIGNED NOT NULL AUTO_INCREMENT,
  `text` varchar(2000) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

ALTER TABLE foo CONVERT TO CHARACTER SET utf8mb4;
```

**ADVERTENCIA:** Cuidado con el tamano de la tabla a la hora de modificarla, y el encoding diferente al `utf8`.

Ahora insertamos varios registros y vemos que pasa:

```SQL
INSERT INTO foo (`text`) VALUES ("test case");
INSERT INTO foo (`text`) VALUES ("🚌");
```

Posiblemente se te genere la siguiente salida, donde el primer registro se agrego perfectamente pero en el segundo registro tienes **2 warnings**, hay que prestar mucho a esa información que suele ser ignorada.

```
mysql> INSERT INTO foo (`text`) VALUES ("test case");
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO foo (`text`) VALUES ("🚌");
Query OK, 1 row affected, 2 warnings (0.01 sec)

mysql> SHOW WARNINGS;
+---------+------+-----------------------------------------------------------------------+
| Level   | Code | Message                                                               |
+---------+------+-----------------------------------------------------------------------+
| Warning | 1300 | Invalid utf8 character string: 'F09F9A'                               |
| Warning | 1366 | Incorrect string value: '\xF0\x9F\x9A\x8C' for column 'text' at row 1 |
+---------+------+-----------------------------------------------------------------------+
2 rows in set (0.00 sec)

mysql> SELECT * FROM foo;
+----+-----------+
| id | text      |
+----+-----------+
|  1 | test case |
|  2 | ????      |
+----+-----------+
2 rows in set (0.00 sec)

mysql>
```

Como puedes ver, usando el comando `SHOW WARNINGS` te lista los mensajes, aquí podemos observar no hay compatibilidad con el emoji y sobre escribe el valor de dicho emoji para hacerlo compatible al `CHARACTER_SET` actual, también puedes observar que hiciste `SELECT` del contenido de la tabla y no está el emoji sino una serie de símbolos,

```SQL
SELECT id, `text`, HEX(`text`) FROM foo WHERE id = 2;
SELECT HEX("🚌");
```

Es cierto que se creo antes la tabla con el soporte a `utf8mb4` y aún así esta dando problemas, debemos revisar las variables del servidor relacionadas a este tipo de configuración, posiblemente nos falta algo, hagamos la siguiente consulta para averiguarlo:

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
       @@init_connect\G
```

Por ejemplo no usamos la sentencia `SHOW VARIABLES LIKE 'character%';` y su variante porque debes ejecutarla por cada tipo de variables [collation|character] o una variable en específico, es cosa tuya. De la query anterior posiblemente obtengamos el siguiente resultado:

```
*************************** 1. row ***************************
    @@collation_connection: utf8_general_ci
      @@collation_database: latin1_swedish_ci
        @@collation_server: latin1_swedish_ci
    @@character_set_client: utf8
@@character_set_connection: utf8
  @@character_set_database: latin1
@@character_set_filesystem: binary
   @@character_set_results: utf8
    @@character_set_server: latin1
    @@character_set_system: utf8
            @@init_connect:
1 row in set (0.00 sec)
```

Ya podemos ver que tenemos un desorden en las variables, toda una ensalada de opciones, deberíamos unificar el mayor número de opciones. Como hemos dicho, necesitamos tener definida la opción `utf8mb4`

Tampoco está demás verificar el collation de la tabla:

```SQL
SELECT table_collation FROM information_schema.tables WHERE table_schema = 'demo' AND table_name = 'foo';
```

Como la creamos nosotros desde el principio, y no te olvidaste de hacer el ALTER, seguramente todo está bien, ahora cambiamos la configuración en la sesión actual de la conexión y actualizamos el registro problemático y verificamos que todo está bien:


```SQL
SET character_set_client = 'utf8mb4'; SET character_set_connection = 'utf8mb4'; SET character_set_results = 'utf8mb4';
UPDATE demo.foo SET `text` = "🚌" WHERE id = 1;
```

Es muy importante que en la conexión de nuestro código que usa la base de datos y dicha tabla se le especifique el `CHARACTER_SET` para evitar algún problema, este es un ejemplo en python, consulta tu lenguaje y/o framework.

<script src="https://emgithub.com/embed.js?target=https%3A%2F%2Fgithub.com%2Fswapbyt3s%2FMyScripts%2Fblob%2Fmaster%2Fexamples%2Fpython%2Fmysql_emoji.py&style=github&showBorder=on&showLineNumbers=on&showFileMeta=on"></script>

Fin.