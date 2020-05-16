---
layout: post
title:  "Restaurar rápido un dump de MySQL"
date:   2018-07-08
categories: MySQL
published: false
---

Hay situaciones que necesitan restaurar una Base de Datos de MySQL **rápido**, comento una serie de trucos que puede ayudar en ahorrarnos casi un 40% del tiempo. Lo divido en varios pasos.

### Configurar el servidor local de MySQL:

Lo que hacemos aquí para no entrar con muchos detalles, es ampliar el uso de memoria para cargar los datos más rápido, como desactivar algunas funciones de consistencia a nivel de disco, y más.

**IMPORTANTE: No usar estos valores en producción.**

```bash
vim /etc/mysql/my.cnf
```

Agregamos estas líneas:

```bash
bulk_insert_buffer_size=512M
innodb_read_io_threads=4
innodb_write_io_threads=8
innodb_additional_mem_pool_size=512M
innodb_buffer_pool_size=1024M
innodb_log_buffer_size=512M
innodb_log_file_size=1024M
innodb_doublewrite=OFF
innodb_support_xa=OFF
innodb_stats_on_metadata=OFF
innodb_flush_log_at_trx_commit=0
innodb_flush_method=nosync
skip-sync-frm=OFF
skip-innodb_doublewrite
```

Para terminar este paso, solo debemos detener e iniciar el servicio, no basta con reiniciar:

```bash
service mysql stop
service mysql start
```
