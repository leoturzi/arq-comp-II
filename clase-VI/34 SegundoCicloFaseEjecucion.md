# 34 Segundo Ciclo de la Fase de Ejecución

[⬅ Volver al índice](README.md)

Último ciclo de la instrucción `MOV A, [1101]`: el cuarto ciclo, que es el
**segundo ciclo de la fase de ejecución**.

## Situación inicial de la hoja

Se transcribe el último instante de la hoja anterior (clock en 1) al
primero de la nueva (clock en 0). La memoria no se borra entre un 1 y un
0, así que se vuelve a copiar entera: el programa y los datos (6 y 2).

Registros:

- **RDI**: quedó copiando `1101` en los esclavos; al pasar el clock a 0,
  pasa a retener `1101`.
- **IP**: seguía reteniendo `0010`, sigue igual.
- **A y B**: siguen reteniendo `XXXX`. Los registros del datapath ya están
  completos.
- **RI**: sigue reteniendo la instrucción.
- **RL**: recibe clock todo el tiempo. El maestro, que estaba copiando,
  pasa a retener `0100` y el esclavo, que retenía `0111`, pasa a copiar lo
  del maestro: el RL vale `0100`.

Con esto queda completa la situación inicial: es "la última pelea" de esta
instrucción.

## Clock en 0

1. El `0100` del RL entra a la ROM de la unidad de control y se activa esa
   posición. Su contenido sale por las líneas de control (un 1 en `M`,
   ceros, y el próximo valor del RL, que es `0000`: vuelve al inicio de la
   fase de pedido de la próxima instrucción).
2. **Líneas activas**:
   - `0Y` = 1: pone 0 en la entrada Y de la ALU.
   - La línea que habilita el dato que viene de la memoria hacia la
     entrada X de la ALU.
   - `E` = 1, `M` = 1.
   - Lectura/escritura en modo lectura.
   - `CA` = 1: copiar en el registro A.
3. **Memoria**: todavía recibe `1101` en el RDI, con `AM` = 1. El dato que
   se direccionó en el ciclo anterior ya pasó medio ciclo y la memoria ya
   lo puso en el bus de datos: `0110` (el 6) en la parte baja; la parte
   superior del bus queda indeterminada (`XXXX`) porque se direcciona una
   sola posición.
4. El `0110` del bus de datos llega a dos lugares:
   - Al registro cuya entrada es el bus de datos (no sirve de nada: su
     línea de copia es 0, así que no se copia).
   - A la **entrada X de la ALU**, y esto sí es lo útil.
5. **ALU**: X = `0110`, Y = `0000` (viene del registro auxiliar de cero).
   Suma `0110 + 0000 = 0110`. Flags: signo 0, cero 0 (el resultado no es
   cero), overflow 0 y carry 0.
6. Ese `0110` aparece en la entrada de los registros que están copiando.

## Clock en 1

- **RL**: el maestro, que copiaba, pasa a retener, y el esclavo, que
  retenía, pasa a copiar lo que viene de la ROM (porque `E` vale 0): el
  `0000` siguiente. Pero la salida sigue siendo `0100` en este ciclo, así
  que se mantienen las mismas líneas de control y los mismos movimientos de
  información.
- Todos los registros con la línea de copia en 0 **repiten** su estado
  (por ejemplo, el IP repite `0010`).
- **A** (`CA` = 1): recibe el clock completo; el maestro pasa de copiar a
  retener y el esclavo pasa a copiar. A queda con `0110`.

## Qué hizo la instrucción

`MOV A, [1101]` es una instrucción en **modo directo**, así que el dato
asociado es la dirección de memoria donde está el dato. Por eso se
ejecuta en **dos ciclos**:

1. **Primer ciclo de ejecución**: se usa el dato asociado para
   direccionar la memoria.
2. **Segundo ciclo de ejecución**: se toma el dato que entregó la memoria
   (el direccionado por el dato asociado) y se lo guarda donde indica el
   código de operación; en este caso, en A.

## Comentario sobre el multiplexor del IP

La línea `M` (multiplexor del IP) vale 0 en la fase de pedido y vale 1 en
toda la fase de ejecución. En realidad, durante la ejecución da lo mismo
que valga 0 o 1: la copia al IP (`CIP`) solo se pone en 1 en dos momentos
(uno es el segundo ciclo de la fase de pedido), y solo ahí importa el
valor de `M`.

La razón de que en la fase de pedido se conecte al sumador y en la de
ejecución a la ALU es simplemente de **orden**: un criterio de
documentación, como los que uno sigue al programar. A veces hay que
decidir algo intrascendente, pero si no se sigue un orden después puede
confundir.

## Siguiente instrucción

Con esto concluyó la primera instrucción. Para seguir con el resto del
programa se repite el mismo proceso en una hoja nueva:

1. Transcribir el programa y el valor de los registros.
2. Calcular el RL: el maestro retiene lo que venía copiando y el esclavo
   copia lo que el maestro retiene.

La situación de inicio es la del ciclo anterior pero con un **IP
distinto**, y el RL en `0000`: se activan las líneas para repetir el
primer ciclo de la fase de pedido, ahora de la segunda instrucción. Todo
es igual salvo el último paso: en vez de simplemente mover el dato a A,
hay que llevar dos cosas a la ALU (A y el dato leído) para poder
restarlas.

Se dejó una **ejercitación grupal**, pero conviene resolverla de forma
individual.

---

[⬅ Volver al índice](README.md)
