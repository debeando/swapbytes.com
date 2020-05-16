---
layout: post
title:  "El teorema CAP"
date:   2016-06-12
categories: Base de Datos
---

Éste post me parece muy importante para poder entender como funcionan y hacer grandes sistemas pensados para escalar, sea por tráfico, por multi-sitio, multi-país, y más… todos estos sistemas distribuidos presentan la misma particularidad, y en el año 2000 un señor llamado [Eric Brewer](https://en.wikipedia.org/wiki/CAP_theorem), pudo definir tres importantes propiedades y desarrollo un teorema. El se dio cuenta que mientras más aplicaciones basadas en la WEB existan, menos debemos preocuparnos por la consistencia de los datos, si queremos alta disponibilidad de nuestras aplicaciones entonces no podemos garantizar la consistencia de los datos.

El teorema CAP, también llamado formalmente Teorema de Brewer, dice que un **sistema de datos distribuido pude asegurar dos de estas tres propiedades: Consistencia, Disponibilidad y Tolerancia al particionado.** Bien, que significa cada una:

- La consistencia (Consistency), Todos los nodos deben ver los mismos datos al mismo tiempo, esto quiere decir que; cualquier cambios en los datos se debe aplicar en todos los nodos, y cuando se recupere el dato tiene que ser el mismo en todos los nodos. Esto se le llama consistencia atómica, y se consigue replicando la información en todos los nodos.
- La disponibilidad (Availability), Cada petición en un nodo debe recibir y garantizar una confirmación si ha sido resuelta satisfactoriamente. En pocas palabras, se debe leer y escribir en todos los nodos.
- La tolerancia al particionado (Partition Tolerance), El sistema debe funcionar a pesar de que haya sido dividido por un fallo de comunicación, garantizando la disponibilidad a pesar que un nodo se separe del grupo sin importar la causa.

![El teorema CAP]({{ "/assets/CAPTheorem.png" | absolute_url }})

El teorema solo nos puede garantizar las siguientes combinaciones:

- CP (Consistency & Partition): El sistema aplicara los cambios de forma forma consistente y aunque se pierda la comunicación entre nodos ocacionando el particionado, no se asegura que haya disponibilidad.
- AP (Availability & Partition): El sistema siempre estará disponible a las peticiones aunque se pierda la comunicación entre los nodos ocacionando el particionado, y en consecuencia por la perdida de comunicación existirá inconsistencia porque no todos los nodos serán iguales.
- CA (Consistency & Availability): El sistema siempre estará disponible respondiendo las peticiones y los datos procesados serán consistentes. En este caso no se puede permitir el particionado.

La correcta decisión de que combinación necesitamos depende de nuestras necesidades de negocio. Nunca olvide que lo más importante en una base de datos relacional es la Consistencia. Conociendo el teorema CAP, nos puede ayudar aún más para saber que Sistemas de Base de Datos debemos escoger, si un SQL o un NoSQL. Si queremos profundizar más en el tema, recomiendo [este post](http://www.julianbrowne.com/article/brewers-cap-theorem).
