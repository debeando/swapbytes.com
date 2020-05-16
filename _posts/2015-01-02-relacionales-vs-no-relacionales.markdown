---
layout: post
title:  "Bases de datos relacionales y no relacionales"
date:   2015-01-24
categories: Base de Datos
---

El gran dilema, bases de datos relacionales ([RDBMS](http://en.wikipedia.org/wiki/Relational_database_management_system)) y no relacionales ([NoSQL](http://martinfowler.com/bliki/NosqlDefinition.html)), todos preguntan, todos hablan de ello, estamos comparando cual es mejor, en fin, hay una gran incertidumbre en el tema, muchos apuntan a un extremo o al otro, cometen errores y nos olvidamos de ver con objetividad. Quiero explicar de que va todo esto de una forma simple para entendernos. Quiero recordar que grandes volúmenes de datos no son un simple millón de rows, son mucho más, billones por ejemplo, ahora imagina billones de rows que interactúan con otros billones de rows para generar información más significativa, cuando hay grandes volúmenes de información se aprecia todo de una forma diferente, esto impacta en tiempo y dinero.

Cada tipo de base de datos nació en una época y con unas necesidades diferentes, y aún **ambas son necesarias hoy en día**. Todo el problema parte por los grandes volúmenes de datos, ¿Cómo los procesamos?, ¿Como puedo mantener mi infraestructura tecnológica?, ¿Cómo hago para que millones de usuarios puedan usar mi aplicación de forma muy rápida? y quien sabe cuantas preguntas más...

En la década de los 70 nacen las bases de datos relacionales que le dan uso a un lenguaje de consulta estructurado llamado SQL, la idea era organizar la información (normalizar) en grupos de datos bajo una semejanza, y así poder mantener una coherencia entre ellos (integridad), fue creciendo el volumen de información pero pocos tenían acceso a manipularla, mientras Internet fue expandiéndose y cada vez más personas acceden a los datos, nos dimos cuenta que los RDBMS son muy lentos, y como fueron diseñados traerían problemas, se inventaron muchas soluciones, pero como todo, se llega a un límite. Y aquí entran las NoSQL, una forma de almacenar y manipular los datos sin necesidad de ser restrictivo como el SQL, con un objetivo muy básico, **sacrificar integridad por velocidad**.

La causa principal del rendimiento es mantener la integridad de los datos, cuesta mucho, por otro lado, no podemos decir que la humanidad es ordenada, hay un desorden "natural" en ella, a pesar que algunos seamos unos maniacos de clasificar y etiquetar todo con un nombre, el problema con las RDBMS, es que cada ves que debemos organizar la información es algo muy costoso y directamente proporcional al volumen de datos, **mientras más información, más tiempo y recursos necesitamos para ordenarla y procesarla**, por eso que no se normaliza y se permite la redundancia.

Ahora imagina que tienes una empresa con información distribuida en muchos servidores de bases de datos, sea del tipo que sea, necesitamos procesar todos estos volúmenes de información sin importar que cambien, con esta simple idea entramos en el mundo del BigData, ¿Cómo funciona? imagina muchos servidores buscando información al mismo tiempo, organizando y procesando para dar un par de números en unos segundos, esto es lo que hacen las NoSQL, es maravilloso no?

Todo se resumen a cuales son tus necesidades, y a partir de allí posiblemente tengas una infraestructura híbrida. Lo importante es **saber que información debe ser consistente y que no**. Lo que quiero decir, por ejemplo, es que todos los datos son importantes, pero hay unos que son más importantes que otros, lo que podemos llamar la base de la información, que está almacenada en una RDBMS, luego esa base es consumida por los usuarios generando grandes volúmenes de datos que son almacenadas en las NoSQL.

**Ejemplos**:

* Una tienda de artículos, todo el detalle del artículo está en una RDBMS, pero hay una serie de funcionalidades para el usuario que generan grandes volúmenes de información que pueden estar en una NoSQL, como las; lista de deseos, favoritos, comentarios, puntuaciones y el motor de búsqueda.
* Un video juegos online, guarda información del estado de tu partida, se reanuda cada vez que entras en la aplicación, y cambia a medida que la uses. Éste es un buen caso para guardar los datos en una NoSQL.
* Centralizas todos los logs generados por tus aplicaciones para luego buscar indicadores de seguridad, errores, y de más, también es una buena opción usar una NoSQL.

Usar una NoSQL tampoco es así de simple, tenemos varios tipos (Clave Valor, Documentales y Grafos) y cada una con un propósito diferente. Inclusive, el modelado de datos entre ambas es diferente; para una RDBMS se usa la [normalización](http://en.wikipedia.org/wiki/Database_normalization) y para una NoSQL de tipo Clave Valor se usa [arreglo asociativo](http://en.wikipedia.org/wiki/Associative_array).

Para terminar, es importante **no confundir términos** usados exclusivamente en las RDBMS con las NoSQL, por ejemplo el acrónimo [ACID](http://es.wikipedia.org/wiki/ACID), en las NoSQL no se aplica porqué no hay relaciones, pero si se garantiza que los datos han sido grabados correctamente.
