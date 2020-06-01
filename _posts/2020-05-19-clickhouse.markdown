---
layout: post
title:  "Introducción a ClickHouse"
date:   2020-05-15
categories: ClickHouse
published: false
tags: [clickhouse]
---

Tengo un par de años trabajando con esta maravilla de base de datos para el uso de analisis que me la presento su creador []() en el DataOps que hicimos en el 2018 en Barcelona, ES. Ahora que tengo la experiencia y la confianza para contarles de esta belleza. Vamos al grano.

A resumidas cuentas, lo obvio de ClickHouse es:

- Está desarrollado por Yantex (El google Ruso).
- Está escrito en C++.
- Es un proyecto open source bajo licencia Apache 2.
- Es una base de datos de tipo columnar para analisis de datos [OLAP (OnLine Analytical Processing)](https://en.wikipedia.org/wiki/Online_analytical_processing).
- Soporta SQL.
- Su primera aparición estable fue en el 2016.

Ahora hablemos de su magia negra, donde esta lo bueno:

- Usa algoritmos de muy bajo nivel SSE para exprimir al maximo el rendimiento de la CPU para los cálculos matriciales y vectoriales.
- Usa un formato de almacenamiento de datos muy optimizado para ahorrar espacio.
- Tiene todas esas cosas para escalar, alta disponibilidad, etc...
- Con poco hardware haces mucho.
- Tiene conectores a otras bases de datos; Kafka, MySQL, Redis, JDBC, CSV, HTTP, etc.
- Usa varios protocolos de comunicación, el suyo propio y también el de MySQL, que te puedes conectar con la tool cli de MySQL y hacer queries en SQL.
- Si combinas una fuente externa y una vista materializada tienes un worker que extrae datos.
- Puedes hacer JOIN con otras tablas que están en otro servidor y otra tecnologia, un JOIN con un MySQL, un Redis, etc.
- Usa vistas materializadas, este es un punto muy fuerte.
- Tiene un mecanismo de protección para evitar eliminar una tabla de forma involutaria luego que supera cierto tamano.

Vamos aclarando algunos puntos:

- Por se una base de datos de tipo OLAP (OnLine Analytical Processing) del todo, **no es transacciónal**. Por lo que no se puede comparar con un MySQL o un PostgreSQL porque estos son OLTP (OnLine Transaction Processing).
- En ClickHouse no es importante hacer UPDATE o DELETE, y aplicará los cambios a su tiempo, por eso no es de uso transaccional.
- 
