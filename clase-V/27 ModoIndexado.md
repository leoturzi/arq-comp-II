# 27 Modo Indexado

[⬅ Volver al índice](README.md)

El **modo de direccionamiento indexado** (también llamado **indirecto**)
es el siguiente que vemos, después del directo y el inmediato.

## Descripción

El **dato asociado** es una **referencia a un registro**, igual que en el
modo registro. La diferencia está en lo que contiene ese registro: acá no
guarda el dato, sino la **dirección de memoria del dato a operar**.

Las instrucciones tienen este formato:

```
MOV AX, [BX]
MOV AX, [SI]
MOV AX, [DI]
```

El registro que contiene una dirección se llama **registro índice**
(algo parecido pasa con el IP, que es un puntero: contiene una
dirección). No todos los registros pueden usarse como índice:

- Los registros de uso general **AX, CX y DX** no. Podrían guardar una
  dirección, pero no se pueden usar como índice.
- **BX** es el único de los de uso general que sí puede ser índice.
- **SI** (*source index*, índice del origen) y **DI** (*destination
  index*, índice del destino) son los registros previstos para ser
  índices. Solo sirven para eso.

## Ejemplo en el debugger

Agregamos al programa tres instrucciones a continuación de la última
cargada con `A` (en la clase, a partir de la dirección 101A, que era la
primera libre):

```
MOV AX, [BX]
MOV AX, [SI]
MOV AX, [DI]
```

Datos que preparamos antes de ejecutar:

| Elemento       | Contenido |
| -------------- | --------- |
| Registro BX    | 6000      |
| Registro SI    | 2000      |
| Registro DI    | 2002      |
| Dirección 6000 | AAAA      |
| Dirección 2000 | 1111      |
| Dirección 2002 | 2222      |

(Los registros se modifican con `R` seguido del nombre del registro, por
ejemplo `R SI`.) El programa está a partir de la dirección 1000 y los
datos en el área que arranca en 2000.

Al desensamblar con `U 101A 101E`, las tres instrucciones tienen el mismo
código de operación y cambia solo el dato asociado:

| Instrucción    | Código de operación | Dato asociado | Registro referenciado |
| -------------- | ------------------- | ------------- | --------------------- |
| `MOV AX, [BX]` | 8B                  | 07            | BX                    |
| `MOV AX, [SI]` | 8B                  | 04            | SI                    |
| `MOV AX, [DI]` | 8B                  | 05            | DI                    |

- `8B` le dice al procesador: "es un movimiento hacia AX de una dirección
  que se encuentra en un registro".
- El dato asociado (07, 04 o 05) le dice **de qué registro** sale esa
  dirección.

### Ejecución

| Instrucción    | Qué hace el procesador                                        | Resultado |
| -------------- | ------------------------------------------------------------- | --------- |
| `MOV AX, [BX]` | BX vale 6000; va a la dirección 6000 y copia su contenido a AX | AX = AAAA |
| `MOV AX, [SI]` | SI vale 2000; va a la dirección 2000 y copia su contenido a AX | AX = 1111 |
| `MOV AX, [DI]` | DI vale 2002; va a la dirección 2002 y copia su contenido a AX | AX = 2222 |

Es como si cargáramos entre los corchetes el valor del registro y después
hiciéramos el movimiento desde esa dirección.

## Por qué se llama indirecto

Porque la dirección **no está en la instrucción**, como sí pasa en el
modo directo. De la instrucción hay que ir al registro, y del registro se
obtiene la dirección donde buscar el contenido: se llega al dato de forma
indirecta.

## Qué aporta respecto del modo directo

- En el **modo directo** la instrucción hace referencia siempre a la
  misma dirección: si dice 2000, cada vez que se ejecute va a ir a buscar
  al 2000. Es una variable con lugar fijo.
- En el **modo indexado** la instrucción solo dice "la dirección está en
  BX". Antes de ejecutarla se puede operar sobre BX, de modo que la
  dirección sea el resultado de un cálculo previo. Ya no se va siempre a
  la misma posición: lo que llega a AX puede ser variable.

Combinado con **bucles** (ejecutar el mismo fragmento de programa una y
otra vez), si en cada pasada se modifica BX, cada vez que se pasa por la
instrucción se hace referencia a una posición de memoria distinta. Así se
puede hacer un barrido de un área de memoria, incluso de toda la memoria,
repitiendo las mismas acciones con otros datos. Con el modo directo habría
que escribir cientos de instrucciones, cada una con un valor distinto.

## Uso típico en programas de alto nivel

Cuando en un lenguaje de alto nivel se definen **vectores o matrices**
(estructuras de datos, no una sola variable), al traducirlo a lenguaje
máquina se suele usar el modo indexado.

En memoria una matriz solo puede estar de forma secuencial, pero
conceptualmente se la ve por filas o por columnas. Jugando con el valor de
BX se puede recorrer la columna uno, la dos, la tres, etc. Se vuelve a
pasar por la misma instrucción pero modificando el puntero, así que ya no
apunta a un lugar fijo, sino a un lugar de la memoria que se puede
cambiar **durante el programa**.

---

[⬅ Volver al índice](README.md)
