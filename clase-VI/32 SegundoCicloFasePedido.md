# 32 Segundo Ciclo de la Fase de Pedido

[⬅ Volver al índice](README.md)

Con la primera hoja completa (lo que pasó en el primer ciclo), armamos la
segunda hoja: el **segundo ciclo de la fase de pedido** de la primera
instrucción, `MOV A, [dirección]`.

## Pasar de una hoja a la siguiente

Hay que transcribir lo que pasa entre el último instante de la hoja
anterior (clock en 1) y el primero de la nueva (clock en 0). Algunas
cosas cambian y otras se mantienen igual.

- **Memoria**: no se borra entre un 1 y un 0. Una memoria que se borra
  "por cualquier cosa" no sirve. Por eso hay que **volver a copiar** el
  programa y los datos en la hoja nueva; si no, se parte de premisas
  falsas (si en el segundo ciclo la memoria estuviera vacía, al leer algo
  parecería que "esto anda mal").
- **Registros que ya retenían**: siguen reteniendo lo mismo. Si un
  registro venía reteniendo desde el clock en 0 del ciclo anterior, sin
  cambios, lo sigue haciendo (por ejemplo B sigue reteniendo `XXXX`).
- **Registros que copiaron en el ciclo anterior**: el que terminó
  copiando `0000` en los esclavos, al pasar el clock a 0 los retiene. Es el
  caso del registro RDI.
- **RL**: es el único registro del procesador que recibe clock en todo
  momento, y funciona con el clock invertido. Con el clock en 1 su
  maestro estaba copiando `0001`; cuando el clock pasa de 1 a 0 el
  maestro **retiene** `0001` y el esclavo, con el clock inverso,
  **copia** del maestro: `0001`.

Para arrancar una hoja nueva siempre hay que: copiar la memoria, poner en
todos los esclavos el valor que tienen al momento cero y **calcular el
RL**. En Sistemas de Computación I se hacía lo mismo (nueva hoja,
copiar el programa, copiar el valor de los registros y recalcular el
RL); es la misma secuencia, pero ahora la entendemos en términos de
circuitos lógicos.

## Clock en 0

Una vez reflejada la situación en el momento cero, arranca el clock.

1. **El `0001` del RL** alimenta el bus de direcciones de la ROM de la
   unidad de control: se direcciona la **segunda posición** y su
   contenido aparece en la salida (el gráfico solo lo rotula).
2. **Líneas activas** en esa posición:
   - `CP` = 1: copiar en el IP.
   - `CRI` = 1: copiar en el registro de instrucciones (RI).
   - `E` = 1.
   - `M` = 0: elige la conexión del IP incrementado (el "sumador tonto"
     que suma 2).
3. **Campo "código de operación del RI"** de la ROM: es el único
   microcódigo donde `E` vale 1. Cuando `E` vale 1, el siguiente valor del
   RL no viene de ese campo de la ROM sino del código de operación del
   RI; por eso lo que se ponga en ese campo es indistinto (podría
   escribirse `XXXX`).
4. **Memoria**: el RDI mantiene retenido `0000`, así que se sigue
   direccionando las primeras dos posiciones (`AM = 0` y lectura/escritura
   = 1, o sea lectura). Esto viene del ciclo anterior, por lo tanto la
   memoria ya entrega su contenido. Como en Sistemas de Computación I, al
   seleccionar dos posiciones, el código de operación va a la parte baja
   del bus (`0111`) y el dato asociado a la parte alta.
5. Como el clock está en 0, los maestros del RI copian esos dos datos:
   en el RI quedan el código de operación y el dato asociado.
6. **Datapath**: no hay ninguna línea **H** activa, así que no se sabe qué
   entra a la ALU ni qué sale. Los registros siguen en modo copia (el
   maestro copia aunque el esclavo retenga), y sus entradas
   inevitablemente son `XXXX`; los flags también. La excepción es el
   IP: su entrada viene del sumador de la salida, así que vuelve a
   haber `0010` (el IP incrementado).

## Clock en 1

Se pasa al clock en 1 empezando por el RL y por los registros que tienen
la línea de copia en 1.

- **RL**: el clock entra completo. Con el clock en 0 el esclavo copiaba
  `0001`; al pasar a 1 pasa a **retener** `0001` (se cierra con el último
  valor que tenía). Entonces el `0001` sigue direccionando la misma
  posición de la ROM y siguen las mismas líneas de control: `CRI` = 1,
  `CP` = 1 y ninguna entrada habilitada hacia la ALU.
- **RI** (con `CRI` = 1): los maestros pasan de copiar a retener
  el código de operación y el dato asociado, y los esclavos pasan a copiar
  lo que retienen los maestros. La instrucción que estaba apuntada por el
  IP (la próxima instrucción) termina en los esclavos del RI.
- **IP** (con `CP` = 1): igual, los esclavos copian el valor incrementado
  (`0010`).
- Es como si hubiera un cable directo desde la memoria hasta los esclavos
  del RI y los maestros del RL: el código de operación del RI
  también entra al RL, para determinar el siguiente microcódigo.
- Los registros con la línea de copia en 0 no cambian: siguen con
  `XXXX`.

## Conclusión: fin de la fase de pedido

Al pasar de 0 a 1 se actualizaron los esclavos:

- La instrucción que estaba apuntada como **próxima instrucción** terminó
  en los esclavos del RI, así que pasa a ser la **instrucción actual**.
- Al mismo tiempo, el **IP quedó incrementado** y apunta a la nueva
  próxima instrucción.

Con esto concluye la **fase de pedido**.

---

[⬅ Volver al índice](README.md)
