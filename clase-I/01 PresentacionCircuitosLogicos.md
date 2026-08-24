# 01 Presentación de Circuitos Lógicos

[⬅ Volver al índice](README.md)

Comenzamos la unidad uno del programa, que se refiere a las **compuertas
lógicas** o **circuitos lógicos**: elementos electrónicos que constituyen
prácticamente el 90 y tanto por ciento del interior de un computador.

## Un poco de historia: el álgebra de Boole

Allá por 1865, el matemático inglés **Boole** desarrolló un álgebra donde
las variables son lógicas: indican si una expresión es verdadera o falsa
(por ejemplo, A = "hoy es un día de sol" es verdadero o falso según
corresponda). Relacionando expresiones entre sí se puede construir
pensamiento.

Estas variables — **variables lógicas** o **booleanas** — toman dos
valores y se relacionan mediante operaciones:

- **suma lógica**
- **producto lógico**
- propiedades conmutativa y distributiva, igual que en el álgebra numérica

## La relación con la electrónica del computador

La electrónica del computador es **binaria**: es fácil construir
circuitos que toman solo dos valores (tensión cero o tensión máxima), lo
que le da gran estabilidad. Como las variables de Boole también toman dos
valores, su álgebra sirve para expresar las relaciones entre los
circuitos del sistema.

## Semiconductores y encapsulados

Los semiconductores se encapsulan en chips plásticos, con distintos
formatos: patitas por el costado, patitas por abajo, o chips
"superficiales" que se sueldan directamente sobre el circuito. Cada chip
es, en el fondo, una gran malla de compuertas lógicas relacionadas entre
sí.

## ¿Qué son las compuertas lógicas?

Las compuertas lógicas son elementos electrónicos que implementan las
operaciones del álgebra de Boole: tienen entradas y salidas, y accedemos
a ellas a través de las patitas del chip.

Se alimentan con tensiones que varían según la tecnología (5 V, 3,3 V,
1,8 V), por lo que un mismo 0 o 1 se representa distinto según el chip.
Para analizar un circuito eso no importa: siempre hablamos simplemente de
cero o uno, falso o verdadero.

Hay miles de chips distintos con distintas funciones, desde muy sencillos
hasta muy complejos — el procesador es un chip muy complejo — pero todos
implementan operaciones del álgebra de Boole sobre sus entradas para
producir sus salidas.

## Ejemplo: sistema de alarma contra incendios

No vamos a estudiar el álgebra de Boole en sí, pero sí podemos usarla para
representar el funcionamiento de un sistema: un sistema de alarma contra
incendios de un edificio.

**Las entradas.** Cada piso tiene dos sensores, de humo y de fuego, porque
hay incendios que generan solo humo y otros que generan solo llama, y se
quiere detectar cualquier tipo. Cada sensor entrega una variable
booleana — cero o uno, falso o verdadero —, más allá del nivel de tensión
particular con que la represente su tecnología.

**Las salidas.** Esas variables se relacionan lógicamente para disparar
distintas acciones:

- activar la alarma si se activa un sensor en cualquier piso,
- detener los ascensores,
- habilitar unas puertas e inhabilitar otras.

**La ecuación.** Esa lógica — "si pasa esto y esto, que pase esto; si pasa
esto y no aquello, que pase tal otra cosa" — se puede expresar como una
ecuación del álgebra de Boole y resolverse operando sobre ella.

---

[⬅ Volver al índice](README.md)
