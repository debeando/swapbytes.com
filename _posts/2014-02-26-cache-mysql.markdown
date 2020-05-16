---
layout: post
title:  "¿Cómo funciona y se configura la Cache de MySQL?"
date:   2014-02-26
categories: Base de Datos
permalink: /como-funciona-configura-cache-mysql/
---

Luego de mucho investigar y probar, me consigo que la cache de MySQL no es tan maravillosa como se cree, por lo que hay que probarla con algo de escepticismo y paciencia, en mi caso, resulta que en el Master puede ser perjudicial, y en los Slaves es beneficiosa.

Algunos DBA's comentan lo siguiente para no usarla:

1. Se diseño cuando los procesadores no eran multi-core.
2. Desde entonces no se ha mejorado esta funcionalidad para los procesadores multi-core.
3. Todas las consultas pasan primero por la Cache y luego se ejecutan, por lo que impide la concurrencia.
4. Cada consulta que pasa por la Cache genera un bloqueo exclusivo.

**Para los curiosos aventureros les dejo como se configura:**

La caché de consultas es una característica muy agradable, pero no hay que prestarle demasiada atención, y no tener la tentación de asignar una cantidad de memoria muy grande. Es un simple mecanismo que guarda las consultas predictivas de tipo SELECT, con su respectivo resultado en memoria durante un cierto periodo de tiempo. Esto ahorra un enorme tiempo de compilación y ejecución de la consulta. Cuando una tabla cambia, cualquier consulta guardada en la cache relacionada a la tabla se elimina. Una tabla cambia cuando se utiliza cualquiera de estos comandos; INSERT , UPDATE , DELETE , TRUNCATE TABLE , ALTER TABLE , DROP TABLE o DROP DATABASE.

El espacio de memoria reservado para la cache se particiona en pequeños bloques, el manejados le asigna como mínimo 4096 bytes (4k) a cada uno.

**La cache ignora los siguientes tipos de consultas:**

* Toda consulta que tiene subconsulta. 
* Toda consulta que requiere una variable. 
* Toda consulta generada por un; procedimiento almacenado, disparador (trigger) o evento. 
* Si utiliza las siguientes funciones: BENCHMARK, CONNECTION_ID, CONVERT_TZ, CURDATE, CURRENT_DATE, CURRENT_TIME, CURRENT_TIMESTAMP, CURTIME, DATABASE, ENCRYPT, FOUND_ROWS, GET_LOCK, LAST_INSERT_ID, LOAD_FILE, MASTER_POS_WAIT, NOW, RAND, RELEASE_LOCK, SLEEP, SYSDATE, UNIX_TIMESTAMP, USER, UUID, UUID_SHORT. 
* Toda consulta que usa tablas temporales. 

**Parámetros de configuración:**

La configuración de la memoria asignada para la cache es muy simple, y una vez asignada hay que ir observando su comportamiento por un tiempo para un ajuste definitivo.

* query_cache_type: Es el parámetro que la activa, esta compuesto por 3 valores que van desde el 0 hasta el 2, cada uno se usa para algo en especifico: 
    * Valor 0: Indica que la cache esta desactivada. 
    * Valor 1: Indica que la cache está activa, guarda en cache todas las consultas excepto las que usen la opción SQL_NO_CACHE en la sentencia SELECT. 
    * Valor 2: Indica que la cache está activa, pero no guarda por defecto todas las consultas, solo aquellas que usen la opción SQL_CACHE en la sentencia SELECT.
* query_cache_size: Indica el tamaño máximo de memoria que se le asigna de forma exclusiva a la cache, esta debe estar representada en bytes.
* query_cache_limit: Indica el tamaño máximo que puede ser usado para guardar el resultado de una consulta en la cache.

Ejemplo de como activar la cache:

```SQL
SET GLOBAL query_cache_type=1;
SET GLOBAL query_cache_size=33554432;
SET GLOBAL query_cache_limit=1048576;
```

**Como saber si funciona:**

Uno de los mejores métodos para saber el beneficio de la cache es examinar el porcentaje de acierto, este valor es la cantidad de consultas que se sirven desde la cache en lugar de ejecutarse, cuando el servidor recibe una sentencia de tipo SELECT, se incrementa la variable Qcache_hits si es almacenada en cache y el de Com_select por ser una nueva ejecución. Por lo tanto, el porcentaje de acierto de la cache de consultas se calcula con la formula:
`Qcache_hits / (Qcache_hits + Com_select)` Un buen porcentaje de acierto no existe, incluso un 30% puede ser útil.

**Comandos para administrar la cache:**

* FLUSH QUERY CACHE: Defragmenta las consultas en la cache. No elimina las consultas de la cache. 
* FLUSH STATUS: Reinicia el contador de consultas acertadas por la cache. 
* RESET QUERY CACHE: Vacía todo el contenido de la memoria de la cache. 

**Analizar los valores devueltos por el estado de la cache:**

Ejecutando el siguiente comando nos arroja todas las variables que indican el estado de la cache:

`SHOW STATUS LIKE 'Qc%';`

Vamos a explicar cada una:

* Qcache_free_blocks: El número de bloques que están disponibles en la memoria de la cache. Por otro lado es el indicador de la fragmentación del espacio libre. Al ejecutar la sentencia FLUSH QUERY CACHE se consolida todo el espacio libre y se refleja con el valor 1. 
* Qcache_free_memory: Es un contador que da una idea de cuanta memoria libre del cache queda. Si el valor es bajo debe incrementar la memoria del cache usando el parametro query_cache_size para. 
* Qcache_hits: Es un contador de las consultas que usaron la cache. Si el numero de es muy bajo se puede debe incrementar el valor de la propiedad query_cache_limit. 
* Qcache_inserts: Es un contador para las consultas que se insertaron en la cache. 
* Qcache_not_cached: Es un contador de todas las consultas recibidas por el manejador de base de datos que no se pudieron guardar en cache, estas pueden ser de tipo diferente a un SELECT o usaron en el SELECT la opción SQL_NO_CACHE. 
* Qcache_lowmem_prunes: Es un indicador de consultas no reutilizadas, el cual remueve la consulta guardada en cache por el orden de llegada y permite cachear las nuevas consultas que están entrando. Si el numero crece muy rápido o es muy alto indica que se esta quedando sin memoria de cache, debe aumentar la memoria con el parámetro query_cache_size. La idea es que pueda contener la cantidad necesarias de consultas, no debe pensar que guardara los valores para siempre. Esto genera fragmentación de la consulta, puede aliviar el problema usando la sentencia FLUSH QUERY CACHE. 
* Qcache_total_blocks: Número de bloques en la cache.
