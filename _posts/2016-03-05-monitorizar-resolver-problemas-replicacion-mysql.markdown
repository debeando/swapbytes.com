---
layout: post
title:  "Monitorizar y resolver problemas de replicación en MySQL"
date:   2016-03-05
categories: MySQL
---

La replicación de datos con slaves es algo muy fácil de configurar y aporta muchos beneficios, pero aveces trae sus grandes problemas si no tenemos en cuenta algunos aspectos de configuración, hardware y red entre otras cosas, vamos a comentar un buen grupo de ellos y ayudarte a saber donde puede estar el problema, se que es muy largo el POST, hay mucho que cubrir, pero tratare de dar la idea.

## Ventajas de usar Slaves

* Divide las escrituras en un master y lecturas en los slaves para distribuir las cargas de trabajo. Esto no lo hace MySQL de forma transparente, la aplicación debe saber que sentencias van al master y al slave.
* Los Slaves que son los servidores de lectura, se pueden optimizar para hacer minería de datos o procesos de reportes.
* Se puede usar un slave para remplazar el master en caso de algún desastre como una medida de prevención.
* Como respaldo usando el retraso en la replicación para recuperar datos, ver la variable [MASTER_DELAY][1].
* Incluso podemos configurar el MultiMaster de dos nodos, pero esto aveces no funciona bien por como esta programada la aplicación.

Los problemas que surgen por la replicación no son nuevos, podemos clasificarlos en dos grandes grupos; Cuando se interrumpe la replicación por alguna inconsistencia, o un retraso de la replicación a pesar que no esté interrumpida.

Para saber el estado de una replicación ejecute este comando en el slave:

```SQL
SHOW SLAVE STATUS\G
```

Recuerde que el estado de la replicación es individual en cada slave. A partir de aquí, tenemos una serie de variables que nos indica que está pasando y también les explico como resolver cualquiera de estos dos grandes problemas.

### Como identificar una replicación interrumpida:

Este problema se puede presentar por una serie de motivos:

* Inconsistencia de datos entre el Master y el Slave.
* Diferentes configuraciones.
* Diferente hora.
* Problemas de red.
* Bloqueos de tablas o de registros.
* Muchos INSERT, UPDATE o DELETES.

Cuando se ejecuta el comando "SHOW SLAVE STATUS" que mencionamos anteriormente, entre todas las variables que nos muestra, debemos hacerle caso a estas:

* **Slave_IO_Running:** Si es "Yes", entonces el esclavo está conectado al master.
* **Slave_SQL_Running:** Si es "Yes", entonces el esclavo está procesando las consultas SQL.
* **Seconds_Behind_Master:** Indica la cantidad de posiciones retrasadas del binlog, si es 0 es que todo está bien.
* **Last_Error:** Si "Slave_SQL_Running" es "No", entonces muestra la última sentencia SQL que produjo un error y se detiene la replicación.

Para resolver la mayoría de los casos, solo debemos ejecutar estos tres comandos:

```SQL
STOP SLAVE; SET GLOBAL SQL_SLAVE_SKIP_COUNTER=1; START SLAVE;
```

Y volver a ejecutar este comando para verificar que no hayan más errores:

```SQL
SHOW SLAVE STATUS\G
```

Es muy importante saber porque pasó esto y como prevenirlo, el echo de saltar el error y continuar nos crea una inconsistencia de los datos, debemos tomar nota del mensaje de error y resolverlo a mano.

### Como identificar el retraso de la replicación:

Primero debemos entender como funciona y cuales son los indicadores que se pueden encontrar en el estado de la replica. La replicación en MySQL trabaja con dos hilos, IO_THREAD y SQL_THREAD.

* El primer hilo es IO_THREAD, se conecta a un maestro, lee los eventos del binarios del maestro y los va guardando en un log local llamado **relay log** (registro retrasado). Corresponde este hilo con la variable de estado **Slave_IO_Running**.
* El segundo hilo es SQL_THREAD, lee los eventos del relay log almacenado localmente en el esclavo, el cual fue escrito por el hilo IO_THREAD y luego los aplica lo más rápido posible en el slave. Corresponde este hilo con la variable de estado **Slave_SQL_Running**.

Siempre que se presenta un retraso en la replicación, es importante saber cual de los dos hilos se encuentra el problema. Normalmente, los hilos de E/S no causarían enormes retrasos de replicación, ya que una vez que se leen del master, se aplican en el slave. Sin embargo, el retraso se debe a una limitación del hardware o por alguna configuración:

* Si es por el rendimiento de la red, afecta al hilo **IO_THREAD**, aquí debemos evaluar si es un tema de saturación de la red, la [NIC][2] no es suficiente para el trafico, u otro problema de red.
* Si es la velocidad del Hardware del slave al procesar el relay log es afectado por el **SQL_THREAD**. Diferentes configuraciones del servicio, indice de los esquemas, carga de trabajo, hardware lento.

Todo estos detalles hay que verlos con mucho cuidado, se requiere paciencia, pero en líneas generales, aquí se han mencionado todos los puntos que debemos evitar para tener problemas en la replicación Master/Slave.

Les deseo suerte.

[1]: https://dev.mysql.com/doc/refman/5.6/en/replication-delayed.html
[2]: https://es.wikipedia.org/wiki/Tarjeta_de_red
