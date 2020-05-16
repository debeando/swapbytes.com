---
layout: post
title:  "Seguridad en MySQL con mysql_config_editor"
date:   2015-07-23
categories: MySQL
---

En la versión 5.6.6 se incluyo una nueva herramienta llamada [mysql_config_editor][1], la cual permite almacenar las credenciales de autenticación de una forma segura y otros datos básicos en un archivo llamado '.mylogin.cnf' en el directorio home del usuario, luego estas credenciales pueden ser recuperadas para conectarse al servidor de MySQL deseado.

A simple vista no parece, pero es muy importante que considere que **el archivo '.mylogin.cnf' no está cifrado**, y no es completamente seguro, pero añade una capa más de seguridad ocultando los datos sensibles por la línea de comandos. Por ejemplo, ocultamos la contraseña en el; historial, alias, logs, scripts, etc... donde puede ser visible de muchas formas.

En el siguiente ejemplo se muestra la forma tradicional de conectarse a una base de datos usando todos los parámetros básicos; host, usuario y clave:

```bash
$ mysql -h production.db.empresa.org -u root -pmypass
```

Usando la nueva herramienta nos conectamos de la siguiente forma:

```bash
$ mysql --login-path=production
```

Asumiendo que ya hemos creado la configuración 'production' usando la herramienta, podemos ver que estamos especificando con el parámetro '--login-path' para que use la configuración 'production' y así conectarse al servidor.

Para poder agregar una configuración, debemos ejecutar el siguiente comando:

```bash
mysql_config_editor set --login-path=production --host=production.db.empresa.org --user=root -p
```

La contraseña no se puede pasar por línea de comando, debemos escribirla para mantenerla un poco más segura. Si queremos conocer todas las configuraciones disponibles, solo debemos ejecutar el siguiente comando:

```bash
mysql_config_editor print --all
```

Si queremos aprender más sobre esta herramienta, recomiendo [visitar su página web][1].

[1]: http://dev.mysql.com/doc/refman/5.6/en/mysql-config-editor.html
