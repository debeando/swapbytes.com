---
layout: post
title:  "Bloqueos en MySQL"
date:   2015-11-17
categories: MySQL
---

El bloqueo es la esencia de cualquier manejador de bases de datos relacional (RDBMS), para el caso de MySQL es importante destacar que el engine **InnoDB** lo gestiona de forma óptima, hasta la fecha no hay otro que lo haga mejor.

No podemos confundir bloqueos con [niveles de aislamiento][1], ya que el bloqueo se hace a nivel de registros usando una instrucción específica, mientras el nivel de aislamiento es el comportamiento por defecto para cualquier otra instrucción normal que manipule los registros.

Los bloqueos existen para poder garantizar el [ACID][2], su correcta implementación permite que halla **concurrencia**, de lo contrario se puede presentar la **contención** y es fatal.

Los bloqueos los podemos dividir en dos grandes grupos; está el bloqueo pesimista y el bloqueo optimista, vamos a entender cada uno:

* **Pesimista:** Es aquel que se apodera del recurso (row/tabla) y no lo libera hasta que se haya modificado. Generalmente se hace con instrucciones propias del RDBMS. Para el caso de MySQL tenemos dos instrucciones; el [SELECT ... FOR UPDATE y el SELECT ... LOCK IN SHARE MODE][3] y no hablar de [LOCK TABLE][4]. Éste tipo de bloqueo produce [Deadlock][5] sin importar la forma como lo controlemos, al final estamos bloqueando un recurso que usa una conexión especifica, tiene un timeout, y otros procesos que esperan por el, esto puede generar una contención, problemas de atomicidad, y muchas más situaciones lamentables.
* **Optimista:** Es la mejor forma de bloquear un recurso fomentando la concurrencia, no existe instrucción SQL que añada ésta funcionalidad, más bien usamos las instrucciones DML existentes, y se basa en la lógica del [MVCC][6]. Consiste en añadir una columna para versionar el registro y evitar que el último estado sea modificado. Por ejemplo: cuando una sesión quiere modificar un registro, ésta lee y hace UPDATE con la última versión conocida de dicho registro, si hay otra transacción que se adelanta para hacer el mismo UPDATE, la versión cambia, y el UPDATE será obsoleto.

Para la mayoría de las aplicaciones WEB, se debe utilizar el bloqueo optimista, como podemos ver, no es más que una implementación de una columna que versiona el registro y la combinación simple de un SELECT y un UPDATE, de esta forma evitamos los [Deadlock][5], la contención, y muchos otros problemas. Los ORM, como por ejemplo Hibernate o ActiveRecord tienen ésta funcionalidad implementada, nos quitan una capa más de detalle y no será necesario modificar el modelo y las consultas.

 [1]: https://www.swapbytes.com/niveles-aislamiento-base-de-datos/
 [2]: https://en.wikipedia.org/wiki/ACID
 [3]: http://dev.mysql.com/doc/refman/5.7/en/innodb-locking-reads.html
 [4]: http://dev.mysql.com/doc/refman/5.7/en/lock-tables.html
 [5]: https://en.wikipedia.org/wiki/Deadlock
 [6]: https://es.wikipedia.org/wiki/Multiversion_concurrency_control
