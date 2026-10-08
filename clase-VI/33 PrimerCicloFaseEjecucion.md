# 33 Primer Ciclo de la Fase de Ejecución

[⬅ Volver al índice](README.md)

Terminada la fase de pedido, pasamos al tercer ciclo: el **primer ciclo
de la fase de ejecución** de la instrucción `MOV A, [1101]`. Es la
instrucción en modo directo cuyo dato asociado es la dirección `1101`.

## Armar la situación inicial de la hoja

Igual que al pasar del primer al segundo ciclo, hay que transcribir el
último instante de la hoja anterior (clock en 1) al primero de la nueva
(clock en 0). La memoria no se borra entre un 1 y un 0, así que se
vuelve a copiar completa: programa y datos.

Registros (sin contar el RL):

- Los que venían reteniendo (por ejemplo uno con `0000` retenido) siguen
  reteniendo lo mismo.
- Los que no recibieron cambios desde que arrancó el procesador siguen en
  `XXXX`.
- El RI (registro de instrucciones) quedó copiando la instrucción
  (`0111` de código de operación y el dato asociado `1101`); al pasar el
  clock de 1 a 0, pasa a **retener** ese estado. Ese es el estado que
  tenía el registro cuando el clock era 1, y se transcribe al momento 0 de
  la hoja siguiente porque lo mantiene.

Lo único que queda por representar es el cambio del **RL**, que recibe
clock constantemente. En el ciclo anterior el maestro estaba copiando el
código de operación del RI, `0111`. Al pasar el clock a 0, el maestro
**retiene** `0111` y el esclavo **copia** `0111` del maestro. Entonces
el RL vale `0111`.

## Clock en 0

Se hace siempre el mismo análisis, de la unidad de control hacia el resto.

1. El `0111` del RL viaja al bus de direcciones de la ROM de control y
   activa la posición `0111`. El contenido de esa posición aparece en la
   salida.
2. **Líneas activas** en esa posición:
   - `M` = 1 y lectura/escritura = 1 (porque se invierte).
   - `CRDI` = 1: copiar en el RDI.
   - `0Y` = 1: poner 0 en la entrada Y de la ALU.
   - `HRIX` = 1: habilitar la salida del RI hacia la entrada X de la ALU.
3. **Datapath**: a la entrada X llega el dato asociado (`1101`, que como
   es modo directo es la dirección del dato) y a la Y un `0000`. La ALU
   suma: `1101 + 0000 = 1101`.
   - Flags: signo = 1, cero = 0 (el resultado no es cero), overflow = 0
     (no hay overflow) y carry = 0 (no hay acarreo). Esos son los valores
     que toman transitoriamente los maestros del registro de estado.
4. Ese valor `1101` entra en los maestros de los registros cuya línea de
   copia está en 1.
5. **Memoria**: hasta hace un rato estaba direccionada la primera
   posición con `AM` = 0 (modo de dos posiciones). Cuando arrancaron estas
   líneas de control, `AM` pasó a valer 1: se le genera un cambio a la
   memoria y, como en todo cambio hay transitorios, no se sabe qué valor
   representa en este momento. Al registro que copia desde el bus de
   datos está entrando, por lo tanto, `XXXX`.

### Una aclaración sobre el RL

Se dijo antes que el próximo valor del RL se reemplaza siempre por los
cuatro últimos dígitos de la posición de la ROM de control, y que solo
para la posición `0001` (donde aparecía `1111`) se completaba con el
código de operación del RI. Eso era una simplificación; lo que realmente
pasa es esto:

- **Solo** cuando la posición de la ROM vale `0001`, la línea que
  controla el multiplexor del RL vale 1, y el próximo dato del RL viene
  del registro de instrucciones.
- En **todos los otros momentos** esa línea vale 0, y el próximo dato del
  RL viene de los cuatro últimos dígitos de la posición de microcódigo.

## Clock en 1

Se analiza el cambio a partir del RL.

- **RL**: el maestro, que estaba copiando, pasa a **retener** `0111`. El
  esclavo, que retenía, pasa a **copiar**; como la línea de control
  del multiplexor vale 0, lo que se copia viene de la ROM de control (la
  próxima posición del microcódigo). La salida del RL sigue en `0111` en
  este ciclo, así que sigue direccionada la misma posición de la ROM, siguen
  las mismas líneas de control y los mismos datos llegan a la ALU.
- Todos los registros que siguen con la línea de copia en 0 **repiten su
  estado**.
- **RDI**: es el único con la línea de copia en 1, así que le entra el
  clock completo: el maestro, que estaba copiando, pasa a retener `1101`,
  y el esclavo, que retenía, pasa a copiar `1101`.

Ese `1101` avanza por el bus de direcciones y llega a la memoria, donde se
conjuga con `AM` = 1 y lectura: se direcciona **una sola posición**, la
`1101`. Así termina el tercer ciclo, es decir, el primer ciclo de la fase
de ejecución.

## Método

Siguiendo siempre el mismo estándar de pasos se puede completar la
ejecución de todo el programa, hoja por hoja. Para no equivocarse:

1. Completar la **situación inicial**: los esclavos que vienen de la
   situación anterior.
2. Ir al **RL**: genera las líneas de control.
3. Las líneas **mueven información**.
4. Esa información entra, o no, en los registros.

Ocurre en dos etapas: con el **clock en 0** se vuelve a calcular el RL y
se ve qué llega a cada registro; con el **clock en 1** los registros que
tienen un 1 en la línea de control generan un cambio. Paso a paso, esos
cambios son los que permiten que se realice la ejecución.

---

[⬅ Volver al índice](README.md)
