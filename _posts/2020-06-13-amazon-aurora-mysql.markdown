---
layout: post
title:  "Introducción a Amazon Aurora MySQL"
date:   2020-05-15
categories: mysql
published: false
tags: [aws, mysql]
---

Amazon Aurora MySQL se podría considerar la primera base de datos relacional disenada para el cloud que contempla todos los aspectos de arquitectura para su alta disponibilidad y resiliencia conserbando la principal esencia de MySQL, vamos a resumir en xxx grandes grupos esas carácteristicas que la hacen única, y comentaremos detalles a tener en cuenta a la hora de trabajar con Aurora.

A la hora de pensar en una arquitectura resiliente y tolerante lo primero que se nos viene a la cabeza es el storage donde estarán los datos, si trabajamos con grandes volumenes de datos es un problema, usando procesos tradicionales como snapshots & xtrabackup nos puede llevar horas o días tener un nodo del cluster sincronizado y operativo. Eso mismo nos resuelve Aurora con tener un storage inteligente e independiente de los nodos del cluster.

Es importante resaltar que crear un nuevo cluster nuevo a partir de un snapshots puede llevar horas dependiendo del volumen de datos, mi experiencia es que 4T de datos se tardan unas 6h aproximadamente. Y esto lo suelo hacer para refrescar las base de datos de los entornos de desarrollo.




, es un fork de MySQL donde amazon desarrollo una serie de plugins y preparo una arquitectura escalable contemplando los principales puntos que se requiere 

storage
los registros son nuestra base de datos



https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/CHAP_Troubleshooting.html
https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Concepts.DBInstanceClass.html#Concepts.DBInstanceClass.Changing


FreeLocalStorage

se integra al S3 perfectamente, puedes hacer un LOAD y un XXXX

tiene plugins propios de amazon

tabla con valores de replicacion y otras cosas.

Priority: tier 1, La prioridad más alta para promocionar es el valor más bajo del tier, si hay varios con el mismo tier, escoje el de la fecha más reciente.


https://media.amazonwebservices.com/blog/2017/aurora-design-considerations-paper.pdf
