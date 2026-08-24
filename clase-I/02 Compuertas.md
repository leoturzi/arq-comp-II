# 02 Compuertas

[⬅ Volver al índice](README.md)

Vimos en la [presentación](01%20PresentacionCircuitosLogicos.md) por qué el
álgebra de Boole se relaciona con la electrónica del computador y qué son
las compuertas lógicas. Vamos a ver cuatro compuertas que se ajustan a las
operaciones del álgebra de Boole, más una quinta que no se ajusta pero es
muy usada en electrónica. Con estas cinco se puede construir un
computador completo, simplemente relacionándolas de distinta forma.

## Compuerta OR

Representa el conector "o" del lenguaje. En el álgebra de Boole es la
**suma lógica**:

```
Z = A + B
```

| A | B | Z = A + B |
|---|---|-----------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

La salida es verdadera si A **o** B son verdaderos.

**Ejemplo.** Una alarma de incendio con sensor de humo (A) y sensor de
fuego (B) — se usan los dos porque hay incendios que solo generan humo y
otros que solo generan llama. La alarma se dispara si A **o** B están
activos.

- **Analogía con conjuntos:** la suma lógica es la **unión** de A y B.
- **Analogía circuital:** dos interruptores A y B en **paralelo**
  alimentando una lamparita. Cerrando cualquiera de los dos (o ambos) hay
  tensión. Por eso 1 + 1 también da 1: es una suma **lógica**, no
  aritmética — el uno indica la existencia de una condición, no una
  cantidad.

## Compuerta AND

El conector "y" del lenguaje. Es el **producto lógico**:

```
Z = A · B
```

> Una AND puede tener 2, 3, 4 o más entradas, igual que en matemática
> podemos tener A·B, A·B·C, A·B·C·D, etc.

| A | B | Z = A · B |
|---|---|-----------|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

Para que Z sea verdadero, A **y** B tienen que ser verdaderos. (La
coincidencia con la multiplicación numérica es solo eso, una
coincidencia.)

**Ejemplo.** Para aprobar la cursada hace falta aprobar los parciales
**y** los TPs; aprobar solo uno de los dos no alcanza.

- **Analogía con conjuntos:** el producto lógico es la **intersección**
  de A y B.
- **Analogía circuital:** dos interruptores A y B en **serie**. Los dos
  tienen que estar cerrados para que pase corriente.

> **Truco visual:** la OR es "redondita", la AND tiene un lado recto,
> como el palito de una I.

## Compuerta NOT

También llamada **inversora** o **complemento**. Se escribe con una
rayita arriba de la variable:

```
Z = Ā   (Z = "no A")
```

Una sola entrada, una sola salida. Cuando se agrega a la salida de una
AND o una OR, no se dibuja completa: se agrega solo un **circulito**, que
indica la inversión.

| A | Z = Ā |
|---|-------|
| 0 | 1 |
| 1 | 0 |

- **Analogía con conjuntos:** si A es el conjunto de los lápices rojos,
  el complemento de A es el de los lápices que **no** son rojos.
- **Ejemplo.** Sobre la alarma de incendio: para encender una luz de
  "está todo bien" cuando no hay incendio (entrada en cero), se invierte
  esa señal con una NOT — el cero se convierte en uno y enciende la luz.

## Compuerta XOR (o excluyente)

El "o" que usamos al decir "en las vacaciones voy a la playa o a la
montaña": las dos opciones se excluyen entre sí. Se representa así:

```
Z = A ⊕ B
```

| A | B | Z = A ⊕ B |
|---|---|-----------|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

Funciona como una OR salvo cuando las dos entradas son iguales: ahí la
salida nunca es verdadera.

## Lógica cableada

Con estas cuatro compuertas se puede armar cualquier circuito lógico —
esto se llama **lógica cableada**, y se usó durante décadas antes de que
se difundieran los procesadores programables (por ejemplo, el tablero de
una central eléctrica o las centrales telefónicas hasta los años 70,
implementadas con relés electromagnéticos en vez de semiconductores).

## Compuerta tri-state

No implementa ninguna operación del álgebra de Boole, pero se usa mucho
en electrónica. Tiene una entrada (A), una salida (Z) y una línea de
control:

- **Línea de control en 1:** Z = A. Funciona como un cable.
- **Línea de control en 0:** Z queda en el **tercer estado** — sin
  conexión, como si estuviera desconectada.

Se usa cuando una conexión debe existir o no según una señal externa,
una necesidad puramente electrónica, no una operación booleana.

## Las compuertas en chips reales

Con estas cinco compuertas se arma todo lo que viene en la unidad.
Algunos ejemplos de chips reales:

- Un chip de **seis inversores**: alimentado con la tensión VCC correcta
  (5 V, 3,3 V, etc.), un uno en la entrada da un cero en la salida y
  viceversa.
- Un chip de **cuatro OR de dos entradas**: VCC es la tensión máxima,
  GND (*ground*) es el cero.
- Un chip de **AND de cuatro entradas**: hace falta un uno en las cuatro
  entradas para tener un uno a la salida.

> En las hojas de datos se suele usar **low** y **high** en vez de cero y
> uno, para hablar del nivel lógico sin atarse a una tecnología
> particular.

---
[⬅ Volver al índice](README.md)
