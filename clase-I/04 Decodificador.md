# 04 Decodificador

[⬅ Volver al índice](README.md)

Ya vimos las [cinco compuertas lógicas](02%20Compuertas.md) y cómo usar
una de sus patitas como
[línea de control](03%20ConfiguracionesSencillas.md). Ahora vamos a dar un
paso más: con la compuerta AND podemos **detectar** algo.

## Norma de escritura de circuitos

A partir de acá los circuitos tienen cables que se cruzan, y necesitamos
una norma para saber si hay contacto entre ellos: si el cruce tiene un
**circulito**, hay contacto (como una soldadura); sin circulito, no hay
contacto, uno pasa por arriba del otro.

> Otra norma habitual en bibliografía hace lo inverso: una jorobita marca
> "sin contacto" y un cruce simple se sobreentiende con contacto. Acá
> usamos circulito = contacto.

## Bus de líneas y combinaciones

Ahora ya sabemos cómo es la norma para hacer circuitos más complejos.
Imaginemos que tenemos un bus de ocho líneas — puede ser de direcciones,
puede ser de datos, no importa —: es un bus por donde van pasando
distintas configuraciones de 8 bits. Si tenemos ocho líneas, ¿cuántas
combinaciones distintas podemos tener?

---
[⬅ Volver al índice](README.md)
