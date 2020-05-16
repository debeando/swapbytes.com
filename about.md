---
layout: default
title: Sobre mí
---

Soy un apasionado por las bases de datos, linux y la programación, y me gusta mesclar todo esto para buscar la armonía. Solo trabajo con Linux, mi favorito es CentOS y luego Debian. Desde hace mucho uso Amazon Web Services (AWS) y como me encanta tener toda la infrastructura codificada y versionada, uso Terraform y Puppet o Ansible. MySQL es mi base de datos favorita, y tambien todos los Fork's como MariaDB y Percona, luego está PostgreSQL. El SQL me parece el mejor lenguaje que existe para comunicarme con las base de datos y manipularlos, me encanta poder ayudar a los Desarrolladores y cualquier variante de los Analistas de datos, optimizar consultas es un reto muy interesante y que requiere ser muy meticuloso, y sin dejar de ayudar a Customer Service para resolver todos los casos complejos que el Backend y otras piezas no pueden. Los datos es el tesoro bruto más preciado de una empresa y de los usuarios, por eso, siempre pienso en la Alta Disponibilidad y la Seguridad, me apoyo mucho con ProxySQL y Orchestrator. Con todo esto, no puedo dejar pasar la monitorización, es algo muy importante e imprescindible, es la única forma de conocer los signos vitales y su comportamiento para detectar problemas que no son visibles a simple vista, de esta forma puedo reconocer síntomas, buscar y demostrar la solución más óptima.

Por todo esto, me considero un DBA de MySQL y un Sysadmin.

Con todo esto, no dejo de usar otras tecnologías.

## ¿Qué significa Swapbyt3s?

Todo en esta vida moderna es un *intercambio (swap)* de *bytes*, cuando hacemos uso del swap para intercambiar el valor de una variable por otra se requieren tres pasos. Entonces un día me llego ésta palabra **swapbytes**, más adelante quice remplazar la letra *e por un 3*, y por pura casualidad tiene mucho más sentido.

```golang
package main

import (
  "fmt"
)

func main() {
  a := 1
  b := 2

  fmt.Printf("a: %d, b: %d\n", a, b)

  swap(&a, &b)

  fmt.Printf("a: %d, b: %d\n", a, b)
}

func swap(x *int, y *int) {
   t := *x
  *x  = *y
  *y  = t
}
```

Pruebalo en [play.golang.org](https://play.golang.org/p/XnNVUxiQLh1)

## Mi lema

"Los problemas se resuelven desde su origen."

Cuando me refiero a su origen e incluso su destino, hago referencia a la Base de Datos, porque cuando alimentas un sistema
con basura, obtendrás basura.
