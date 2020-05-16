---
layout: post
title:  "Niveles de aislamiento en Base de Datos"
date:   2015-01-24
categories: Base de Datos
---

El aislamiento es una parte importante de la propiedad ACID que garantiza que las transacciones sean fiables. Esto permite que las transacciones que se ejecutan simultáneamente no interfieran con otras, **garantizando la integridad de los datos**, al no existir aislamiento en una transacción podría modificar los datos que otra transacción está leyendo, por lo que se crea una inconsistencia cuando se crean datos.

Ahora que entendemos que es el aislamiento en términos generales, vamos a conocer cuales son los niveles de aislamiento, estos determinan como las transacciones se comportan con otras transacciones, es como ser más o menos restrictivo. Escoger cual es el mejor nivel depende de las necesidades de la aplicación, primero debe entender cuales son los beneficios y consecuencias de cada una de ellas.

InnoDB soporta los cuatro niveles de aislamiento estándar, a continuación se describen los estándares usados en muchos maneadores de bases de datos relacionales, no solo se aplica para MySQL, sino para Oracle, SQL Server y PostgreSQL.

*   **READ-UNCOMMITTED (LECTURA NO CONFIRMADA):** La ejecución de la instrucciones SELECT se llevan a cabo sin bloqueo, puede utilizar una versión antigua de una fila que ya no existe. Por lo tanto, el uso de este nivel no tiene aislamiento y no garantiza la trasacción, tales lecturas no son consistentes. Esto también se le llama una **lectura sucia**. De lo contrario, este nivel de aislamiento funciona igual que "READ COMMITTED".
*   **READ-COMMITTED (LECTURA CONFIRMADA):** Está es la opción favorita de Oracle y la que se recomienda para muchos casos. Con éste nivel de aislamiento se evita el fenómeno de la **lectura sucia**, porque los cambios no confirmados no son visibles para cualquier otra transacción, hasta que se confirme el cambio. Dentro de este nivel de aislamiento, cada SELECT utiliza su propia instantánea de los datos que se confirmo (commit) antes de la ejecución de la instrucción SELECT. Ahora, ya que cada SELECT tiene su propia instantánea, por lo que el mismo SELECT cuando se ejecuta varias veces durante la misma transacción podría regresar diferentes conjuntos de resultados. Este fenómeno se le llama **lectura no repetible**.
*   **REPEATABLE-READ (LECTURA REPETIBLE):** Lee todos los datos de forma coherente dentro de la misma transacción, es como hacer una foto instantánea de los datos desde la primera lectura. Con este nivel de aislamiento se evita el fenómeno de la **lectura no repetible**. Este nivel de aislamiento devuelve el mismo conjunto de resultados para diferentes SELECT dentro de una misma transacción. Una instantánea de la SELECT se toma la primera vez que se ejecuta durante la transacción y la misma instantánea se utiliza dentro de la transacción cada vez que se ejecuta el mismo SELECT. Una transacción que se ejecuta en este nivel de aislamiento no tiene en cuenta los cambios de los datos realizados por otras transacciones, independientemente de si los cambios se han confirmado (commit) o no. Esto asegura que las lecturas siempre son consistentes (repetible). Este nivel de aislamiento es el predeterminado para InnoDB. Aunque este nivel de aislamiento **resuelve el problema de lectura no repetible**, pero hay otro **fenómeno fantasma**.
*   **SERIALIZABLE:** Con éste nivel de aislamiento se evita el **fenómeno de fantasma**. Coloca un bloqueo de rango en el conjunto de datos, cuando las transacciones se ejecuta en este nivel de aislamiento se bloquean todos los registros y recursos que se tiene acceso, así bloquea todo cambio, impidiendo que otros usuarios actualizar o insertar filas en el conjunto de datos hasta que la transacción se ha completado. Este nivel de aislamiento es el más fuerte posible.

Cada uno de estos niveles de aislamiento tienen sus beneficios y consecuencias, vamos a explicar cada situación:

## Se puede evitar el nivel de aislamiento SERIALIZABLE

Si, Como vemos es el más restrictivo sacrificando consistencia por rendimiento, un nivel de aislamiento parecido es REPEATABLE-READ, tiene una forma de bloqueo especial que ayuda a evitar los fenómenos fantasmas, podemos asegurarnos de hacer un buen bloqueo sin fenómenos fantasmas usando la estructura SELECT con FOR UPDATE o LOCK IN SHARE MODE. El bloqueo compartido permite a otras transacciones leer los registros examinados, pero no actualizar o borrar mientras que otra transacción esté interviniendo.

## Realicación y Niveles de aislamiento

El tipo de replicación predeterminada en MySQL es la replicación basada en declaraciones [Statement Based Replication (SBR)], mantiene los cambios de los datos por la re-ejecución de las sentencias SQL ejecutadas en el maestro a los esclavos. Esto requiere que el nivel de aislamiento sea más estricto, de modo que los cambios de datos deben ser consistentes, de tal manera que el mismo SQL debe garantizar los mismos cambios en el esclavo. Cuando usamos este tipo de replicación puedes configurar el nivel de aislamiento en SERIALIZABLE o REPEATABLE-READ. Si usas la versión 5.1 o superior de MySQL debes usar el nivel de aislamiento READ-COMMITTED para garantizar la consistencia de datos.

## Los niveles de rendimiento y aislamiento

Como hemos podido ver, mientras más bloqueos menos rendimiento y mayor consistencia, la intención es buscar un equilibrio según nuestras necesidades, ya que los extremos son perjudiciales. Usar el nivel de aislamiento SERIALIZABLE no es para nada favorable a nivel de rendimiento, pero usar READ-UNCOMMITTED mejora mucho el rendimiento pero no garantiza la integridad de los datos, por lo que nos quedan las dos opciones del medio.
