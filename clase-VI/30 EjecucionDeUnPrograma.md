# 30 Ejecución de un Programa

[⬅ Volver al índice](README.md)

Llegó el momento de ver cómo se ejecuta un programa en nuestro **modelo
de procesador**. El programa y el modelo están en el apunte del modelo de
procesador (sección de recursos y bibliografía); hacia el final, cerca de
la página 13, se listan las instrucciones del modelo agrupadas por modo de
direccionamiento, que son los que vimos en la clase pasada.

## Instrucciones del modelo

El modelo tiene nueve instrucciones:

| Modo de direccionamiento | Cantidad | Qué hacen                                                                                 |
| ------------------------ | -------- | ----------------------------------------------------------------------------------------- |
| Directo                  | 4        | Mover de memoria a A, restar, sumar y mover de A a memoria (la dirección va entre corchetes). |
| Inmediato                | 1        | Cargar una constante en B.                                                                |
| Registro                 | 2        | `A - B` (resultado en A) y `A + B` (resultado en A).                                      |
| Indexado                 | 1        | Restar a A el dato que está en la dirección de memoria contenida en B.                    |
| Relativo                 | 1        | Salto condicional: se le suma un valor al IP solo cuando el flag Z indica **no cero**.    |

La primera instrucción es la misma que teníamos en el modelo de Sistemas
de Computación I (código de operación `0111`) y hace lo mismo: va a la
dirección que indica el dato asociado, toma el dato y lo copia en A.

## El programa de ejemplo

Con estas instrucciones armamos un programa que no lleva a ningún lado:
solo sirve para ejercitar las distintas instrucciones y modos. Ocupa
**seis instrucciones** en memoria (la resta indexada y el salto se
ejecutan más de una vez). El dato inicial es 6 y se le va restando 2:

1. **Directo**: carga el dato en A (A = 6).
2. **Directo**: le resta un dato de memoria y guarda el resultado en A
   (resta 2).
3. **Inmediato**: carga en B, de forma inmediata, la dirección de ese
   mismo dato de memoria.
4. **Indexado**: le resta a A el contenido de la memoria cuya dirección
   está guardada en B. Va al mismo lugar que la instrucción 2, pero llega
   por otro camino: así comparamos los dos modos. A vale 2.
5. **Salto condicional**: como el resultado no es cero (flag Z en "no
   cero"), se le suma al IP el dato asociado y salta de vuelta a la resta
   indexada.
6. **Indexado** otra vez: se resta de nuevo 2 y A pasa a valer 0.
7. **Salto condicional**: ahora el resultado es cero, así que no salta y
   continúa con la instrucción siguiente.
8. **Directo**: guarda el 0 en memoria.

Recordar cómo trabajan las instrucciones de salto: en ensamblador se
escribe una **dirección absoluta**, pero en lenguaje máquina queda una
**dirección relativa**, un salto relativo. El dato asociado es el valor
que se suma al IP para generar el salto.

## Líneas de control del modelo

Igual que el modelo de Sistemas de Computación I, este modelo tiene una
**unidad de control** de la que salen **líneas de control**. En aquel
modelo había que seguir cada línea con el dedo para ver dónde impactaba;
acá sería muy difícil, así que las líneas tienen un **código en el
nombre** que indica a qué lugar llegan.

| Prefijo / nombre | Qué hacen                                                                                                          |
| ---------------- | ------------------------------------------------------------------------------------------------------------------ |
| **H…** (habilita) | Habilitan que la información llegue a la entrada de la ALU: `HAX` (contenido de A hacia la entrada X), `HAY`, `HBX`, `HBY`, `HIP`. `0X` y `0Y` ponen un 0 en X o en Y. |
| **C…** (copia)    | Permiten que la información se copie en un registro: `CA` llega al registro A, `CB` al B, y análogamente los demás (IP, RDI, etc.). |
| `E` y `M`         | Manejan dos **multiplexores** de buses de 4 bits. Con 0 pasa una entrada y con 1 la otra; el cableado de `M` está invertido respecto de `E`. |
| `-/+`             | Controla si la ALU suma o resta.                                                                                  |
| Modo de acceso (AM) | Controla si la memoria se direcciona en una posición (valor 1) o en dos posiciones (valor 0).                    |
| Lectura/escritura | Controla si la memoria se lee o se escribe.                                                                        |

### Lectura y escritura en la memoria

El modo lectura se habilita con un 0 en la línea de control. El modo
escritura incorpora el **clock**: cuando la línea de control vale 1, la
salida depende del clock. Durante medio ciclo la línea sigue en modo
lectura, para darle tiempo al dato de estabilizarse, y luego de medio
ciclo pasa a modo escritura. Ese es el objetivo del circuito.

### Orden de análisis

Siempre se analiza en este orden:

1. Primero las líneas **H**: qué información llega a la ALU.
2. Después las líneas **C**: dónde entra el resultado.
3. Para la memoria, el modo de acceso (AM) y la línea de
   lectura/escritura.
4. Para los multiplexores, `M` y `E`.

### Pasar un dato sin cambio

Dentro del datapath las salidas de los registros se relacionan con las
entradas a través de la ALU. Para que un dato pase sin cambios se usa la
opción de **sumar cero**: si B vale cualquier cosa, se lo manda a una
entrada, se le suma cero, y a la salida aparece B, que se puede llevar a
cualquier lado.

## Los "registros dobles" del gráfico

En el esquema parecen aparecer registros dobles, pero no lo son. Cada
registro es de 4 bits y está hecho con cuatro flip-flops maestro-esclavo.

- Con el **clock en 0**, los maestros copian y los esclavos retienen.
- Con el **clock en 1**, los maestros retienen y los esclavos copian lo
  que retienen los maestros; entonces maestros y esclavos quedan iguales.

El dato de un registro está guardado en los **esclavos**, porque ahí está
disponible en la salida para ser usado. Cuando el clock vuelve a 0 en el
ciclo siguiente, el registro arranca con ese valor retenido en los
esclavos.

Por eso, en cada registro se grafica lo que pasa en **dos instantes**:
cuando el clock vale 0 y cuando vale 1. No son ocho flip-flops: son los
mismos cuatro maestro-esclavo a los que se les saca una "foto" con el
clock en 0 y otra con el clock en 1. Esto es fundamental para entender el
avance de los tiempos y no equivocarse.

## Cómo se completa un ciclo

El modelo usa **una hoja por cada ciclo**: se toma un modelo en blanco y
se va completando paso a paso la evolución de lo que pasa adentro, con
esta secuencia:

1. Se refleja la **situación inicial**.
2. Con el clock en 0, se lee la memoria y la unidad de control genera
   valores en las líneas de control.
3. Las líneas de control activan **movimientos de información hacia la
   ALU**.
4. Determinada información entra a determinados **registros**: se genera
   un cambio.
5. Eventualmente se direcciona la **memoria**.
6. La unidad de control se prepara para su nuevo cambio.

Son más o menos siete u ocho pasos por ciclo. Repitiendo estas pautas
imagen tras imagen se puede desarrollar la ejecución de cualquiera de las
instrucciones. Como siempre en estos ejercicios, el ejercicio ya está
resuelto en el código: si el programa está bien escrito, el ejercicio se
resuelve solo, siguiendo los pasos automáticos y repetitivos que
realizaría el procesador internamente.

---

[⬅ Volver al índice](README.md)
