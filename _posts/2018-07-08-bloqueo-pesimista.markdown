---
layout: post
title:  "Ejemplo del bloqueo pesimista"
date:   2018-07-08
categories: MySQL
published: false
---

El bloqueo pesimista es algo que **NO DEBEMOS USAR**, incluso su nombre lo deja muy claro, pero debemos **saber ¿porque?**, y la verdad es que es bastante interesante, hay que tener en cuenta varios detalles; el engine de la tabla debe ser InnoDB, toda **modificación** del registro se debe hacer dentro de una declaración de transacción (BEGIN / COMMIT), usar el nivel de aislamiento adecuado, y por último, recordar que cada transacción tiene un timeout, si abusamos del tiempo de espera se aborta la transacción.

En este post vamos a ver paso por paso como funciona usando dos sesiones que tratan de modificar el mismo registro, utilizaremos varios comandos muy interesantes y también veremos que el tiempo de espera puede afectar.

Primero me gustaría aclarar que el nivel de aislamiento si importa, es recomendable usar REPEATABLE-READ, pero también funciona usando READ-COMMITTED. Al usar otros modos de aislamiento, podría haber alguna diferencia en el comportamiento, pero el principio es el mismo.

## Cuando podemos usar el bloqueo de registros

Básicamente cuando queremos que una sesión sea la única en modificar el registro, y las otras sesiones que necesiten el mismo registro deben esperar para obtener el valor más reciente o no interfieran para evitar datos inconsistentes. Esto se hace cuando tenemos concurrencia, me explico, tenemos la misma aplicación en muchos servidores que consultan el mismo row de la misma base de datos, y el que llega de primero será el que tiene el control absoluto y más nadie puede hacer nada con el hasta que termine de usarse. Vamos a explicarlo con varios ejemplos:

1. Tenemos un sistema de pasarela de pagos, este se comunica con el banco para realizar el cobro de una compra echa por el usuario, necesitamos garantizar que el pago se haga una sola vez, y que el último estado sea el correcto, de lo contrario se puede repetir el cobro (Solo es viable si estamos 100% seguros que el tiempo de respuesta del banco es menor a 30 segundos, pero nos sirve de ejemplo).
2. Se tiene que emitir un número de factura consecutivo por una serie de países, cada país debe tener su propia secuencia, la factura número 1 la tenemos para todos los países, y así poco a poco va incrementando según las compras que se van realizando por cada país y no podemos permitir duplicados por el mismo país.

## Ejemplo práctico

Por complejidad prefiero desarrollar el ejemplo creando el número de factura consecutivo por país. Creamos nuestra primera sesión para ir preparando los datos de prueba, y seguir con el ejemplo:

Es bueno revisar primero el tipo de aislamiento que tenemos:

```SQL
SHOW VARIABLES LIKE '%tx_iso%';
```

Sino es el deseado, lo definimos así:

```SQL
SET GLOBAL tx_isolation = 'REPEATABLE-READ';
```

Creamos una base de datos y una tabla de prueba en la primera sesión:

```SQL
CREATE DATABASE test CHARACTER SET utf8;
USE test;
CREATE TABLE `foo` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `user_id` int(11) NOT NULL,
  `order_number` int(11) DEFAULT '0',
  `country` char(5) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uidx_foo` (`order_number`,`country`)
);

ALTER TABLE ADD UNIQUE KEY `uidx_foo` (`order_number`, `country`);
```

Insertamos unos datos de ejemplo:

```SQL
BEGIN;
INSERT INTO foo (user_id, order_number, country) VALUES(1, 1, 'es_ES');
INSERT INTO foo (user_id, order_number, country) VALUES(2, 1, 'es_VE');
INSERT INTO foo (user_id, order_number, country) VALUES(3, 1, 'es_US');
COMMIT;
```

Abrimos una segunda sesión:

```SQL
BEGIN;
INSERT INTO foo (user_id, country) VALUES(4, 'es_US');
SET @id = LAST_INSERT_ID();
COMMIT;

BEGIN;
SELECT id FROM foo WHERE id = @id FOR UPDATE;
SET @max = (SELECT MAX(order_number) + 1 FROM foo WHERE country = 'es_US');
UPDATE foo SET order_number = @max WHERE id = @id AND order_number = 0;
COMMIT;
```

```SQL
BEGIN;
SET @max = (SELECT MAX(order_number) + 1 FROM foo WHERE country = 'es_US');
INSERT INTO foo (user_id, order_number, country) VALUES(4, @max,'es_US');
COMMIT;
```
