---
layout: post
title:  "Buscar registros huérfanos"
date:   2016-02-12
categories: MySQL
---

Los registros huérfanos son aquellos que se almacenan en un modelo relacional padre-hijo, los registros padres hacen referencia a filas primarias, y los registros hijos hacen referencia a filas secundarías, esto puede pasar por varias razones en una DDBB, básicamente sino existe claves foráneas definidas, o alguien desactivo de forma momentánea el check [foreign_key_checks][1], y para mantener la integridad de los datos debemos de vez en cuando identificar estos registros huérfanos, podríamos eliminarlos o asignarlos a un padre de forma provisional. Yo opto más por lo segundo, conservar los datos es nuestra prioridad.

Los registros huérfanos pueden tener un **Foreign Key igual a NULL o tener un ID de un registro primario que no exista**, pero debemos tener bastante cuidado con el NULL, ya que puede hacer referencia a una relación "PUEDE" y no "DEBE", lo cual eliminamos algo que no debemos.

Hay varias formas de hacer esta consulta, pero solo una es la más apropiada por temas de rendimiento, seguramente la condición que pensamos de primero es **NOT IN**, pues esa no, ni tampoco usar un **LEFT OUTER JOIN**, deberías de usar **NOT EXISTS** que es mucho más eficiente, a continuación muestro los tres ejemplos, el último es el bueno:

**A) Primer ejemplo incorrecto:**

```SQL
SELECT count(*)
FROM payment AS child
WHERE rental_id NOT IN (SELECT rental_id FROM rental);
```

**B) Segundo ejemplo incorrecto:**

```SQL
SELECT count(*)
FROM payment AS child
LEFT OUTER JOIN rental AS parent ON parent.rental_id = child.rental_id
WHERE child.rental_id IS NOT NULL AND parent.rental_id IS NULL;
```

**C) Primer ejemplo correcto:**

```SQL
SELECT count(*)
FROM payment AS child
WHERE rental_id IS NOT NULL
  AND NOT EXISTS (
  SELECT NULL FROM rental AS parent WHERE parent.rental_id = child.rental_id
);
```

Hay algo curioso en el ejemplo A y C, a ver si se dan cuenta? La única pista, es que tiene que ver con los NULL.

Ahora que los hemos identificado, que hacemos con ellos? bueno, la forma correcta sería la siguiente:

* Agregar un registro primario (padre) para agrupar los registros secundarios (hijos) que están huérfanos.
* Hacer un update en el Foreign Key de todos los registros secundarios con el valor correspondiente al registro primario.
* Verificar que todo este bien.

Espero que les haya sido útil.

[1]: http://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_foreign_key_checks
