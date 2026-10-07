# 24 Modo Directo

[⬅ Volver al índice](README.md)

En el **modo de direccionamiento directo**, el **dato asociado** de la
instrucción es la **dirección de memoria donde se encuentra el dato a
operar**.

Todas las instrucciones que vimos en Sistemas de Computación I
funcionaban así. Por ejemplo:

```
MOV AX, [2000]
```

## Cómo se ve en memoria

En memoria esta instrucción son los bytes:

```
A1 00 20
```

Los escribimos en hexadecimal, pero en memoria solo hay binario: `A1`
es `1010 0001`. El procesador interpreta:

- `A1` como el **código de operación**: mover un dato desde la memoria
  al registro AX.
- `00 20` como el **dato asociado**. Como la instrucción corresponde al
  modo directo, se lo interpreta como la **dirección** donde está el
  dato: la 2000 (con el byte menos significativo primero).

En lenguaje ensamblador se escribe `MOV AX, [2000]`. Los **corchetes**
indican que lo que hay adentro es una dirección de memoria (y que se
opera con su contenido), y el movimiento va siempre de derecha a
izquierda: se toma el contenido de la dirección 2000 y se lo lleva a AX.

## Ejemplo en el debugger

Así lo hacíamos en Sistemas de Computación I:

1. Con `E 1000` escribimos los bytes `A1 00 20` a partir de la dirección
   1000.
2. Con `R` miramos los registros. El **IP** no apunta a la primera
   instrucción, así que hay que modificarlo para que apunte a 1000; si
   no, nunca se ejecutaría.
3. Con IP en 1000, la línea informativa del debugger muestra que en 1000
   está `A1 00 20`, interpretado como `MOV AX, [2000]`, y además que en
   la dirección 2000 hay un `CC00`.
4. Con `T` se ejecuta esa única instrucción: el contenido de la
   dirección 2000 aparece en AX.

¿Qué hizo el procesador? Leyó el código de operación `A1` ("tengo que
hacer un movimiento hacia AX"), leyó el dato asociado ("desde la
dirección 2000"), fue a 2000, encontró guardado `00 CC` y lo copió a AX.
Recordar que un dato de 16 bits lo expresamos como `CC00`, pero en
memoria primero va el byte que contiene la unidad y después el superior;
por eso en 2000 hay `00 CC`.

## El comando A: escribir en ensamblador

El debugger también permite cargar instrucciones en lenguaje mnemónico
con el comando `A` (*assemble*), que se encarga de convertirlas al
lenguaje de la máquina.

Por ejemplo, `A 1003` (la dirección que quedó libre justo después de la
primera instrucción). A partir de ahí el debugger invita a escribir en
ensamblador, y después de cada instrucción avisa cuál es la próxima
dirección libre, porque sabe cuántos bytes ocupó:

| Dirección | Instrucción      | Bytes que ocupa | Próxima dirección |
| --------- | ---------------- | --------------- | ----------------- |
| 1003      | `MOV AX, [2000]` | 3               | 1006              |
| 1006      | `ADD AX, [2002]` | 4               | 100A              |

En 100A se puede seguir con otra instrucción (por ejemplo, la resta) y
armar así el programa del trabajo práctico de Sistemas de Computación I.

### Por qué la segunda instrucción quedó en 1003

Como la primera instrucción ocupa tres bytes (A1 de código de operación
más 16 bits de dirección) y la escribimos a mano en 1000, 1001 y 1002,
el siguiente lugar libre es el 1003. Ahí repetimos `MOV AX, [2000]`:
repetir la misma instrucción no es más que un ejemplo. Una instrucción
repetida usa posiciones de memoria nuevas; el programador puede poner las
instrucciones que quiera, y que resuelvan o no el problema es otro tema.

## Verificar con el comando U

Con `U` (*unassemble*) le pedimos al debugger que interprete lo que hay
en memoria entre dos direcciones, por ejemplo `U 1000 1006`. Muestra:

| Dirección | Bytes         | Instrucción      |
| --------- | ------------- | ---------------- |
| 1000      | `A1 00 20`    | `MOV AX, [2000]` |
| 1003      | `A1 00 20`    | `MOV AX, [2000]` |
| 1006      | `03 06 02 20` | `ADD AX, [2002]` |

Esto confirma que lo cargado con `A` (desde 1003) y lo escrito a mano con
`E` (en 1000) conviven en memoria.

## Modificar la memoria a mano

Se puede corregir en memoria lo que se había cargado con `A`. Con `E`
dejamos igual la instrucción de 1000 y en 1003 cambiamos el `A1` por `A3`
y el primer byte de la dirección (`00`) por `04`:

```
A3 04 20
```

Al desensamblar de nuevo, la primera instrucción quedó igual y la
segunda cambió: `A3` también es un `MOV`, pero en sentido contrario, de
AX hacia la memoria. Queda como `MOV [2004], AX`.

El programa de ejemplo ahora hace lo siguiente:

1. Lee 16 bits de la dirección 2000 y los lleva a AX.
2. Guarda AX en la dirección 2004.
3. Suma a AX el contenido de la dirección 2002.

No resuelve nada importante: es solo un ejemplo para ver el efecto de las
distintas instrucciones.

## Área de instrucciones y área de datos

Al cargar un programa en lenguaje máquina, en un área de la memoria
cargamos las instrucciones y en otra los datos. La dirección 2000 forma
parte del área de datos porque la elegimos nosotros; con 4000 hubiera
quedado en 4000 y con 8000 en 8000.

El programador decide dónde poner el programa y dónde los datos. En este
ejemplo el programa empieza en 1000 y los datos se buscan en 2000 y
siguientes.

---

[⬅ Volver al índice](README.md)
