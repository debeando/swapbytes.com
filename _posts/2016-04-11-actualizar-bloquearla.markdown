---
layout: post
title:  "Actualizar datos de una tabla sin bloquearla desde un archivo"
date:   2016-04-11
categories: MySQL
---

Hay muchas formas de hacerlo, para variar quise buscar una alternativa rápida y eficaz para evitar escribir un nuevo script, y se me ocurrió usar los comandos de linux, la verdad que resulto muy bien, aquí les dejo un ejemplo:

```bash
cat tokens.csv \
| \
awk -F ',' '{ print "UPDATE users SET token = \"" $2 "\" WHERE token IS NULL AND email = \"" $1 "\";"; system("sleep 0.1");}' \
| \
mysql --login-path=foo \
      --safe-updates \
      --batch \
      --silent \
      --database=foo`
```

Explico un poco, básicamente se lee el archivo con cat, luego se crea la sentencia UPDATE usando el awk, por cada update hacemos una pausa de 100 milisegundo para no saturar, y se lo mandamos al cliente de MySQL.

De vez en cuando nos toca hacer un gran UPDATE sobre una tabla grande que nos puede llevar 10 min. para completar su ejecución, el problema es que si lo hacemos directamente bloqueamos la tabla hasta que termine, eso puede ocasionar varios problemas muy graves a nivel de aplicación, hoy presento una alternativa usando Procedimientos Almacenados.

Básicamente consiste en tener una sentencia que hace UPDATE a cada 1000 rows, se ejecuta dicha query N veces hasta llegar a 0 rows actualizados, en cada interacción hacemos una pequeña pausa de 2 segundos para dejar que pasen las transacciones que están esperando usar dicho recurso.

```SQL
DROP PROCEDURE IF EXISTS sp_users_update;

DELIMITER |
CREATE PROCEDURE sp_users_update()
  BEGIN
    SET @row_count = 0;

    REPEAT
      -- Query to update:
      UPDATE LOW_PRIORITY users SET username = CONCAT('DELETED_', username)
      WHERE deleted_at IS NOT NULL
      AND username NOT LIKE 'DELETED_%'
      LIMIT 1000;

      -- Save row counts for update:
      SET @row_count = (SELECT ROW_COUNT());

      -- Sleep for not blocking table:
      SELECT SLEEP(2);
    UNTIL @row_count = 0 END REPEAT;
  END;
|
DELIMITER ;

CALL sp_users_update();

DROP PROCEDURE IF EXISTS sp_users_update;
```

La verdad que es una alternativa rápida a comparación de un script, es cierto que el proceso de buscar con LIKE es algo costoso para la CPU si son muchos rows, de echo pude observar un pico durante la actualización, nada grave por ser una situación controlada y probada en otros entornos.
