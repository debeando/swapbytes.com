---
layout: post
title:  "Collation, diferencias entre utf8_general_ci y utf8_unicode_ci"
date:   2016-02-08
categories: MySQL
---

Para los que estamos acostumbrados a trabajar en Linux es normal usar el formato de codificación UTF-8, y en mi opinión personal es lo que se debería de usar para todo. En el caso de MySQL se puede definir en una base de datos, tabla y columna el formato de codificación como CHARSET y un sub formato llamado COLLATE. La intención del COLLATION es poder ayudar en la Presición vs Rendimiento con los carácteres especiales.

Para conocer todos los formatos disponibles de codificación y el sub formato por defecto de MySQL, ejecute el siguiente comando:

```SQL
SHOW CHARACTER SET;
```

Como podemos ver en la lista, el COLLATION por defecto de UTF8 es el utf8_general_ci, existe otro llamado utf8_unicode_ci, y entre los dos hay grandes diferencias y al conocerlas entenderemos porque una está por defecto:

## Precisión para ordenar los datos:

* **utf8_unicode_ci:** Se basa en el estándar Unicode para ordenar, y ordena con precisión en una amplia gama de idiomas.
* **utf8_general_ci:** Se acerca mucho para ordenar correctamente con Unicode en muchos idiomas comunes, pero tiene una serie de imprecisiones al ordenar en algunos idiomas, por lo que no es recomendable para la correcta ordenación en todos los idiomas.

## Rendimiento:

* **utf8_general_ci:** Es más rápido en las comparaciones y la ordenación, está puntualmente diseñado para el rendimiento, ignora los detalles según el idioma.
* **utf8_unicode_ci:** Utiliza un algoritmo mucho más complejo para comparar, tiene como objetivo la clasificación correcta de acuerdo en una amplia gama de idiomas. Esto hace que sea más lento para ordenar y comparar un gran número de campos.

## Limitaciones:

* No se puede realizar comparación directa entre valores de diferente COLLATION, puede resolverlo usando el operador BINARY, pero usar esta alternativa baja mucho el rendimiento.

Quedo claro que el formato por defecto utf8_general_ci es el más rápido y el menos impreciso. La definición de estos formatos depende de nuestras necesidades muy particulares.

La siguiente consulta permite listar todas las tablas que no tienen el collation por defecto:

```SQL
SELECT table_schema, table_name, engine, table_collation
FROM INFORMATION_SCHEMA.TABLES
WHERE table_type = 'BASE TABLE'
  AND table_schema NOT IN ('mysql', 'performance_schema')
  AND (engine <> 'InnoDB' OR  table_collation <> 'utf8_general_ci');
```

Si queremos profundizar un poco más sobre el tema, les dejo los siguientes enlases oficiales de MySQL que explican todo estoy muy bien, y hasta tiene unos ejemplos:

* [Character Sets and Collations in General][1]
* [Examples of the Effect of Collation][2]
* [The BINARY Operator][3]

 [1]: https://dev.mysql.com/doc/refman/5.6/en/charset-general.html
 [2]: https://dev.mysql.com/doc/refman/5.6/en/charset-collation-effect.html
 [3]: https://dev.mysql.com/doc/refman/5.6/en/charset-binary-op.html
