# 28 Modo Relativo

[⬅ Volver al índice](README.md)

El **modo de direccionamiento relativo** es el que en general se usa para
los **saltos en la ejecución**.

## Para qué sirve

En el modelo de Sistemas de Computación I el **IP** siempre se
incrementaba: no había forma de volver atrás. Ahora vamos a poder ir hacia
atrás o hacia adelante. Por ejemplo:

- Ejecutar una serie de instrucciones y generar un salto que dependa de
  algo: si el registro llegó a tal valor, se repite; si no, se sigue
  adelante.
- Tener distintos módulos de programa: se ejecuta uno, se saltan otros y
  se pasa a un módulo que está más adelante.

## Definición

> En el modo de direccionamiento relativo, el **dato asociado** es un
> valor que se suma al IP para producir un salto de la ejecución.

(Es una definición acomodada para que se entienda; más abajo se ve por
qué el dato es "relativo".)

Es típico de las **instrucciones de salto**:

- **`JMP`** (*jump*): salto **incondicional**. Siempre salta; por ejemplo,
  cuando termina un módulo y se salta a otro.
- **Saltos condicionados**: dependen del valor de un *flag* ("ejecuto esto
  mientras el registro total no sea cero: si no es cero, repito; si es
  cero, sigo"). También usan modo relativo, pero los vemos recién cuando
  lleguemos a programación.

En el modelo de Sistemas de Computación I se saltaba siempre de a dos
posiciones, porque cada instrucción ocupaba dos y se iba a la siguiente.
Con un salto se rompe esa secuencia.

Para un ejemplo sencillo usamos solo saltos incondicionales.

## Ejemplo: JMP 2000

Agregamos al programa una instrucción en la dirección 1020 que salta a la
2000. Lo que escribimos en ensamblador es la dirección absoluta:

```
JMP 2000
```

Pero **el dato asociado no es 2000**. Al desensamblar el rango de
memoria, el ensamblador cargó solo estos bytes:

| Dirección | Bytes      | Código de operación | Dato asociado |
| --------- | ---------- | ------------------- | ------------- |
| 1020      | `E9 DD 0F` | `E9` (salto)        | `0FDD`        |

El dato asociado `0FDD` (en memoria, byte menos significativo primero:
`DD 0F`) es el valor que hay que sumarle al IP. Nosotros escribimos 2000
y el ensamblador lo convirtió a algo que hay que **sumarle al IP**.

### Por qué 0FDD y no la diferencia con 1020

Hay que recordar **cuándo se incrementa el IP**: durante la **fase de
pedido** (búsqueda de la instrucción), no durante la ejecución. El IP
siempre apunta a la *próxima* instrucción; cuando la instrucción se
carga en el registro de instrucciones (RI) deja de ser la próxima y pasa a
ser la actual, así que el IP se incrementa para apuntar a la siguiente.

Para la instrucción de 1020:

1. Antes del pedido el IP vale 1020, y se usa para direccionar la memoria
   y traer la instrucción al RI.
2. El procesador no sabe todavía que es un salto: trata todas las
   instrucciones igual. Como esta ocupa tres bytes, el IP pasa a valer
   **1023**.
3. En la fase de **ejecución** el IP ya vale 1023, y es a ese valor que se
   le suma el dato asociado.

```
  1023     (IP al ejecutar, ya incrementado)
+ 0FDD     (dato asociado)
------
  2000     (nuevo IP)
```

No es que el procesador "corrija" nada: en la fase de ejecución el IP
siempre apunta a la instrucción siguiente, no a la actual. Si se sumara a
1020 se estaría trabajando con la instrucción actual.

Con `T` ejecutamos el salto: no cambia ninguno de los registros de datos;
cambia el IP, que pasa a valer 2000.

## Segundo salto: JMP 1000 desde 2000

En la dirección 2000 ponemos un salto de vuelta a la 1000:

| Dirección | Bytes      | Código de operación | Dato asociado |
| --------- | ---------- | ------------------- | ------------- |
| 2000      | `E9 FD EF` | `E9` (salto)        | `EFFD`        |

Se repite el mismo razonamiento. Cuando el procesador va a la memoria a
leer la instrucción, el IP se actualiza a **2003**, y recién en la fase de
ejecución se suma el dato asociado:

```
  2003
+ EFFD
------
 11000
```

El IP tiene **16 bits**, así que el 1 de la izquierda queda afuera y el
nuevo IP es **1000**: el valor que escribimos como dirección absoluta en
ensamblador. Al ejecutar con `T` volvemos a 1000 y el programa empezaría
de nuevo.

Como estamos ejecutando paso a paso, hay que recordar que estamos viendo
una película, no una foto: lo que pasa en la fase de pedido ocurre antes
que la ejecución.

Este programa de ejemplo entra en un **bucle infinito**: llega al salto a
2000, de ahí vuelve a 1000, y así siempre. Un procesador real lo
ejecutaría de un saque y no pararía nunca; acá lo hacemos paso a paso solo
como ejemplo.

## Por qué se llama relativo

Repetimos en memoria la misma instrucción (`E9 FD EF`, la que cargamos
como salto a 1000) en 2000, 2003, 2006, etc., y desensamblamos con
`U 2000 200E`. Son siempre los mismos bytes, pero el destino cambia según
dónde esté la instrucción:

| Dirección de la instrucción | Bytes      | IP tras el pedido | Salta a |
| --------------------------- | ---------- | ----------------- | ------- |
| 2000                        | `E9 FD EF` | 2003              | 1000    |
| 2003                        | `E9 FD EF` | 2006              | 1003    |
| 2006                        | `E9 FD EF` | 2009              | 1006    |

El salto es **relativo a la posición donde está la instrucción**: siempre
va 1000 hacia atrás respecto de la instrucción. Si arranca en 2000 lleva
a 1000, si arranca en 2003 lleva a 1003, y así.

Eso es lo que realmente significa "relativo": el dato asociado depende de
la posición de la instrucción, no es una dirección fija. La definición
del principio ("valor que se suma al IP para generar un salto de la
ejecución") es la versión para entender el efecto; el modo relativo en
sí explica esta característica relativa del dato asociado respecto de
la dirección donde se encuentra la instrucción.

---

[⬅ Volver al índice](README.md)
