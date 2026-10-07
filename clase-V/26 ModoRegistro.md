# 26 Modo Registro

[⬅ Volver al índice](README.md)

En el **modo de direccionamiento registro**, el **dato asociado** ya no
es el dato ni la dirección de memoria donde está: es una **referencia al
registro** donde se encuentra el dato a operar.

## Qué cambia respecto de los modos anteriores

Hasta ahora el dato asociado aparecía explícito: en Assembler lo
escribíamos nosotros (una dirección o una constante) y en memoria
aparecía como bytes a continuación del código de operación.

Con el modo registro eso se corta. Las instrucciones son de este tipo:

```
MOV BX, AX
MOV BX, CX
MOV BX, DX
```

Acá estamos diciendo "lo que está en AX (o CX, o DX), copialo a BX". En
la instrucción no aparece ningún valor ni ninguna dirección: solo los
nombres de los registros.

## Ejemplo en el debugger

Cargamos las tres instrucciones con el ensamblador del debugger, en este
orden: `MOV BX, AX`, `MOV BX, CX`, `MOV BX, DX`. Los registros CX y DX
valen 0 y el único que tiene un valor es AX (6000), así que ya con estas
tres alcanza para ver el efecto.

Al desensamblar el rango de memoria donde las cargamos, aparecen las tres
instrucciones con estos bytes:

| Instrucción  | Código de operación | Dato asociado | Registro referenciado |
| ------------ | ------------------- | ------------- | --------------------- |
| `MOV BX, AX` | 89                  | C3            | AX                    |
| `MOV BX, CX` | 89                  | CB            | CX                    |
| `MOV BX, DX` | 89                  | D3            | DX                    |

- El **código de operación** 89 le dice al procesador: "tengo que mover a
  BX algo que está en un registro".
- El **dato asociado** (C3, CB o D3) responde la pregunta "¿en cuál
  registro?": C3 indica AX, CB indica CX y D3 indica DX.

Por eso se dice que el dato asociado es una **referencia al registro**:
es una indicación de en qué registro se encuentra el dato. El procesador
ve C3 y sabe que el dato está en AX; ve CB y sabe que está en CX; ve D3 y
sabe que está en DX.

## Lo mismo con otra operación: ADD

Si en vez de `MOV` usamos `ADD`, cambia el código de operación pero el
mecanismo es el mismo. Agregamos `ADD BX, AX` (suma a BX lo que hay en
AX):

| Instrucción  | Código de operación | Dato asociado | Registro referenciado |
| ------------ | ------------------- | ------------- | --------------------- |
| `MOV BX, AX` | 89                  | C3            | AX                    |
| `ADD BX, AX` | 01                  | C3            | AX                    |

`MOV` se codifica con 89 y `ADD` con 01, pero en los dos casos la
referencia a AX aparece como C3. El dato asociado le dice al procesador
con qué registro tiene que hacer el movimiento, la suma o la operación
que sea.

## Reescribir una instrucción en memoria

En el debugger se puede pisar una instrucción ya cargada. Por ejemplo,
reescribir una instrucción como `ADD BX, CX` en el lugar de otra: en
memoria se mantiene el 01 del `ADD` y el par 89 C3 se reemplaza por
01 CB, es decir, cambian el código de operación y la referencia (CB
apunta a CX).

Funciona porque la instrucción nueva ocupa **la misma cantidad de bytes**
que la anterior.

## Limitación del debugger: instrucciones de distinta longitud

Lo que no se puede hacer es reescribir una instrucción por otra de
**longitud distinta**. El debugger solo pisa los bytes de la instrucción
nueva; si esta es más corta, queda un byte sobrante de la instrucción
anterior y cambia el sentido de todo lo que viene después.

Ejemplo de la clase: se reescribió en la primera dirección una
instrucción más corta (`MOV BX, AX`, 2 bytes) donde antes había otra más
larga. Al desensamblar:

- Quedó un byte sobrante (20) de la instrucción anterior.
- El desensamblador lo interpretó como código de operación y tomó la
  instrucción siguiente (que empezaba con A3 04) mal alineada.
- En este caso el desarme se recuperó unas instrucciones más adelante,
  pero hay casos en que no se recupera y todo lo que sigue queda mal
  interpretado.

Para arreglarlo se volvió a escribir la instrucción original
(`MOV AX, [200]`) y al desensamblar de nuevo todo se normalizó.

Esto es una limitación del debugger; lo vamos a ver con más detalle
cuando hagamos programación en Assembler.

## Ejecución paso a paso

Con AX = 6000 y CX = DX = 0, ejecutamos la secuencia de cinco
instrucciones y vemos cómo evoluciona BX:

| Instrucción  | Resultado en BX                         |
| ------------ | --------------------------------------- |
| `MOV BX, AX` | BX recibe el valor de AX (6000)         |
| `MOV BX, CX` | 0 (CX vale 0)                           |
| `MOV BX, DX` | 0 (DX vale 0)                           |
| `ADD BX, CX` | 0 (se suma CX = 0)                      |
| `ADD BX, AX` | 6000 (se suma AX = 6000)                |

Después de la última instrucción lo que aparece en memoria es basura: no
forma parte del programa.

---

[⬅ Volver al índice](README.md)
