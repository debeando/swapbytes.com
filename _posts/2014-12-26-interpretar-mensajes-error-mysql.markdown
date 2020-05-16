---
layout: post
title:  "Interpretar los mensajes de error de MySQL"
date:   2014-12-26
categories: Base de Datos
---

Cuando trabajas en un equipo de desarrollo y eres responsable de la base de datos, te preguntan frecuentemente por los errores que van apareciendo y su posible relación con MySQL. De todo puede pasar, desde problemas de configuración, de conexión, de lógica y sintaxis, en fin... en estos casos hay que orientarlos y quiero explicarles de que va todo esto.

Los mensajes de error se dividen en dos grandes grupos, los que se producen del lado del **servidor** y los del lado del **cliente**. Para conocer un poco más que significa cada código de error, existe una aplicación llamada [perror](http://dev.mysql.com/doc/refman/5.6/en/perror.html).

### Errores del lado del Servidor

Aquí se presentan todos aquellos [errores del lado del servidor](http://dev.mysql.com/doc/refman/5.6/en/error-messages-server.html) que **tienen que  ver en su mayoría con problemas de lógica o sintaxis del SQL, o alguna violación de un constraint.** Todos estos los identificamos porque su código de error están entre el número 1000 y 1999.

Cuando se genera este tipo de error en MySQL, el servidor envía el código y el mensaje al cliente. Es importante destacar que NO hay forma de ocultar estos errores.

En caso de ser una excepción por alguna validación del constraint, se recomienda que la aplicación sea capaz de poder parsear el mensaje y controlarlo.

### Errores del lado del Cliente

Las aplicaciones o drivers que se utilizan para conectarse a MySQL necesitan de la librería [Connector de MySQL](http://dev.mysql.com/downloads/connector/c/), en ella existe una definición de los [mensajes de Error](http://dev.mysql.com/doc/refman/5.6/en/error-messages-client.html), el siguiente enlace es la librería del lenguaje C. Podemos examinar el código fuente para ir aprendiendo un poco de como funcionan.

Están por encima del valor de 2000. Por ahora no son más de 70 errores, y **tienen que ver con problemas de conexión**. La librería Connector se encarga de ponerle un mensaje al código de error.

En este caso hay que probar siempre muy bien la nueva versión de la librería a implementar en producción, aquí empiezan muchos problemas cuando hay actualizaciones, mi experiencia es con la gema [mysql2](https://github.com/brianmario/mysql2) de ruby.

Para poder hallar con el problema hay que investigar un poco por todos lados, aquí dejo varios tips:

* Lo más importante, leer el mensaje de error y analizarlo. No llamar impulsivamente al DBA para que lo lea por ti, google es tu amigo.
* Revisar si ha habido algún cambio de driver y configuración.
* Si es un problema de conexión, es bueno ver los logs para hallar con alguna pista.

Suerte!
