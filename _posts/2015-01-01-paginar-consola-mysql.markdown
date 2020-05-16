---
layout: post
title:  "Paginar en la consola de MySQL"
date:   2015-01-01
categories: MySQL
---

Yo uso mucho la consola de MySQL, la verdad que es muy útil, pero muchas veces ejecutamos una Query que nos arroja un gran número de rows y columns que no caben en nuestra pantalla, y terminamos recurriendo a usar el scroll de la terminal para explorar los datos, ahora podemos implementar la paginación en la consola de una forma muy fácil.

Solo debemos editar o modificar un fichero de configuración, el que usa [MySQL Command Line](http://dev.mysql.com/doc/refman/5.6/en/mysql.html), manos a la obra:

```bash
vim ~/.my.cnf
```

Agregamos estas líneas al final:

```
pager = less -RSFXin
```

Debe quedar así:

```
[mysql]
user = root
pager = less -RSFXin
```

Podemos observar que hay un parámetro de configuración llamado **pager** que se encarga de pasar por STDOUT los datos a otro programa de la terminal de Linux para que haga su magia, en este caso usamos el comando **less** para paginar el resultado que es retornado por STDIN.
