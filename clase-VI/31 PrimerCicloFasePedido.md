# 31 Primer Ciclo de la Fase de Pedido

[⬅ Volver al índice](README.md)

Hacemos andar el modelo de procesador: primero se carga el programa y
se arma la situación inicial, y después se analiza el **primer ciclo de
la fase de pedido** de la primera instrucción.

## Cargar el programa en la memoria

Ningún procesador funciona si no tiene el programa cargado en la
memoria. Es como en el `debug`: escribimos en memoria, byte a byte, los
códigos de operación y los datos asociados de cada instrucción.

El programa tiene **seis instrucciones**:

1. Directo: mover un dato de memoria a A (código de operación `0111`).
2. Directo: restar a A un dato de memoria (`SUB A, [dirección]`).
3. Inmediato: cargar un valor en B.
4. Indexado: restar a A el dato cuya dirección está en B.
5. Salto condicional.
6. Directo: guardar A en memoria.

Después de las instrucciones faltan los **datos**: el área de variables
tiene el valor 6 y, en otra dirección, el valor 2.

## Situación inicial

Si esto fuera el `debug`, para que el programa arranque alcanzaría con
apuntar el **IP** a la primera instrucción. En el modelo hay que
configurar además la situación de inicio internamente.

Se registra la posición inicial **antes de que arranque la ejecución**,
así que se la coloca para el **clock en 0**; lo que pasa con el clock en
1 será consecuencia de lo que pase con el clock en 0. Recordar que los
registros guardan su información en los **esclavos**: cuando está
retenida ahí, es accesible a través de la salida del registro.

La situación inicial es:

| Elemento                             | Valor inicial                                                                |
| ------------------------------------ | ---------------------------------------------------------------------------- |
| Memoria                              | El programa y los datos recién cargados.                                     |
| IP                                   | `0000`: dirección de la primera instrucción (próxima instrucción).           |
| **RL** (registro de la unidad de control) | `0000`: el modelo siempre arranca con el microcódigo `0000`, que es el primer ciclo de la fase de pedido. |
| Todos los demás registros            | `XXXX`: indeterminado.                                                       |

- **Valor 0000 en el IP y el RL**: no está representado qué circuito lo
  pone. Se puede imaginar un botón de arranque que, mientras se
  mantiene apretado, fuerza ese 0; al soltarlo se deja entrar el clock
  y empiezan todas las transiciones.
- **`XXXX`** significa que puede haber ceros, unos o cualquier
  combinación: algo va a haber, porque ninguna electrónica queda en una
  situación indefinida, siempre migra hacia el 0 o hacia el 1, pero no
  sabemos hacia cuál.

Las únicas tres cosas determinadas son: el programa y los datos en
memoria, el IP en 0 y el RL en 0. Para poder completar cada punto del
circuito conviene imaginar que hasta acá se frenó el tiempo; ahora
arranca.

## Clock en 0: paso a paso

Esta primera instrucción es `MOV A, [dirección]`; estamos en el primer
ciclo de su fase de pedido.

1. **Se direcciona la ROM de la unidad de control.** Los cuatro ceros del
   RL van, a través del decodificador, a la ROM de la unidad de control:
   se direcciona la posición 0 y su contenido aparece en la salida. Ese
   contenido es el que alimenta las **líneas de control**; lo copiamos
   en el gráfico como rótulo.
2. **Líneas activas** en esa posición: `HIP`, `0X` y `CRDI`.
   - `HIP` habilita el IP hacia la ALU.
   - `0X` habilita un 0 hacia la otra entrada de la ALU.
   - `CRDI` copia el resultado en el registro RDI (registro de
     direcciones).
3. **La ALU suma**: `0000 + 0000 = 0000`. Ese resultado aparece en la
   entrada de todos los registros del datapath. También aparecen valores
   en los flags: signo 0 (suma de positivos), cero 1 (el resultado es
   cero), overflow 0 y carry 0. Como la línea de copia de los flags está
   en 0, esos valores no se guardan; es prolijo anotarlos porque son cosas
   que pasan.
4. **Registros con la línea de copia en 0**: tienen el clock en 0, así
   que los esclavos **retienen** y los maestros **copian** lo que llega a
   su entrada:
   - Los que están conectados directamente a la salida de la ALU copian
     `0000`.
   - El IP, con `M = 0`, recibe el valor que viene del sumador "tonto"
     que siempre suma 2 (las instrucciones ocupan dos posiciones), es
     decir `IP + 2`.
   - El registro cuya entrada es el bus de datos de la memoria copia algo
     indeterminado (`XXXX`): la memoria todavía no fue direccionada, así
     que está entregando un valor indeterminado.
5. Las líneas **AM = 0** y **E = 1** están determinadas por el circuito
   de control. Como `E = 1`, hacia el registro que copia desde la memoria
   llega ese valor indeterminado.

Con esto queda completa la situación para el clock en 0: todos los
registros tienen su valor.

## Clock en 1

Siempre conviene analizar a partir del **RL**, porque es el que
"motoriza" las líneas de control, que controlan todo el procesador. El RL
está conectado "al revés" respecto de los demás registros.

- **RL**: el flip-flop que copiaba 0000 pasa a retener; el otro pasa a
  copiar lo que tiene en la entrada, que es la dirección siguiente del
  microcódigo (`0001`). Pero **la salida del RL sigue en `0000`** durante
  este ciclo: los cuatro flip-flops son siempre los mismos; lo que cambia
  es lo que sale por esos cuatro cables según el clock valga 0 o 1. La
  próxima dirección recién va a aparecer en la salida en el ciclo
  siguiente.
- Como el RL sigue en `0000`, se sigue direccionando la misma posición de
  la ROM, así que siguen las **mismas líneas activas**, la misma suma y
  los mismos flags.
- Los registros con la línea de copia en 0 (por ejemplo `CA`) no cambian:
  siguen copiando y reteniendo lo mismo.
- **RDI**: es el único registro con la línea de control en 1. Como `1` con
  cualquier cosa es "cualquier cosa", le llega el ciclo de clock completo.
  Con el clock en 0 el maestro copiaba la entrada y el esclavo retenía lo
  anterior; al pasar a 1 el maestro retiene `0000` y el esclavo copia ese
  `0000`.

Entonces `0000` sale ahora por el bus de direcciones y llega a la
memoria: se direcciona la posición `0000` en **modo lectura** y en
**modo de leer dos posiciones**. Termina el ciclo con esa posición de
memoria direccionada, y el contenido recién se aprovecha en el ciclo
siguiente.

Por eso el registro de instrucciones (RI) queda en `XXXX`: la memoria
entrega su contenido al bus de datos, pero como la línea de copia del RI
nunca vale 1 en este ciclo, lo que hay en la entrada nunca reemplaza lo
que hay en la salida.

## Método de análisis

Lo único que hay que hacer es **seguir la secuencia lógica de
propagación** de los datos; no hay que inferir nada:

1. Un cambio (el RL) direcciona la ROM de la unidad de control.
2. Eso genera valores en las líneas de control.
3. Las líneas **H** liberan información hacia la ALU.
4. La ALU deja su resultado en la entrada de los registros.
5. El registro que tenga la línea **C** en 1 termina copiando el dato.

Algunas aclaraciones sobre cómo trabajar:

- La ROM de la unidad de control es una memoria de **solo lectura**, de
  posiciones fijas. Es una memoria interna de la máquina, no forma parte
  de la memoria principal.
- Conviene copiar en orden todas las líneas para no olvidarse ninguna.
- Primero se analizan las **H** (qué llega a la ALU y cómo se
  distribuye); las **C** se analizan al final, y tienen efecto no en el
  primer ciclo sino en el segundo.
- En este modelo no hay una línea que habilite la salida de la memoria:
  siempre entrega su dato. Por eso, al comenzar la ejecución, como el
  valor de RDI era indeterminado, lo que la memoria entrega puede ser el
  contenido de cualquier posición.

---

[⬅ Volver al índice](README.md)
