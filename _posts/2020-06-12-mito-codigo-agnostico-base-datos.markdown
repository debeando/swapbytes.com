---
layout: post
title:  "El mito del 'código debe ser agnóstico a la base de datos'."
date:   2020-05-15
categories: database
published: false
tags: [database]
---

Muchas veces nos dicen que **código debe ser agnóstico a la base de datos** y esto se debería de interpretar de una forma correcta, vamos a explicar esta filosofía tan compleja y a su vez conflictiva:

El termino [agnóstico](https://es.wikipedia.org/wiki/Agnosticismo) significa del griego "sin conocimiento", y se cree que su origen surge cuando se crearon los primeros software intermedios llamados [DBAL/DAL](https://en.wikipedia.org/wiki/Database_abstraction_layer) que son librerías que ayudaban a la comunicación entre aplicación y base de datos, una [API](https://en.wikipedia.org/wiki/Application_programming_interface#Libraries_and_frameworks) más y no la del HTTP. De esta forma se puede abstraer el funcionamiento y crear compatibilidad con multiples gestores de bases de datos usando el mismo código. Existen muchas variantes de este tipo de software, y pueden ser cualquiera de las siguientes; DBAL, ODBC, ORM, ADO, PDO, DAL, Active Record, etc... En este post no hablaremos qué hace cada uno, sino filosofar.

Este software intermedio generó una gran aceptación en el mundo del desarrollo por la comodidad de no aprender un lenguaje adicional como el SQL, permitiendo despreocuparse del detalle de los gestores de bases de datos, simplemente usando su lenguaje de programación principal podía manipular datos sin necesidad de saber los detalles internos. Esta forma de trabajar ha generado muchos debates a favor y en contra.
