# 03 Configuraciones Sencillas

[⬅ Volver al índice](README.md)

Ya vimos las [cinco compuertas](02%20Compuertas.md) base: OR, AND, NOT,
XOR y tri-state. Ahora vamos a mirar esas mismas compuertas desde otro
ángulo: usando una de sus entradas como **línea de control** (LC).

## Compuerta AND como línea de control

Tomamos una AND de dos entradas y renombramos una de ellas como línea de
control, sin cambiar el circuito.

**LC = 0:** Z siempre es 0, sin importar A.
**LC = 1:** Z = A.

Es decir, la línea de control decide si Z copia a A o queda en cero. Se
parece a la compuerta tri-state, pero con una diferencia: en la
tri-state, LC = 0 deja a Z **desconectada**; acá, LC = 0 pone a Z en
**cero**.

**Ejemplo.** Si A es un sensor que va cambiando de valor, la línea de
control decide en qué momento "miramos" ese valor a la salida.

## Compuerta OR como línea de control

Mismo ejercicio con una OR:

**LC = 0:** Z = A.
**LC = 1:** Z = 1 siempre.

Comportamiento inverso al de la AND: acá Z copia a A cuando LC es cero, y
queda fija en uno cuando LC es uno.

## Compuerta EXOR como línea de control

**LC = 0:** Z = A (se comporta como un cable).
**LC = 1:** Z = Ā (se comporta como una NOT).

## Aplicación: convertir un sumador en restador

En Sistemas de Computación I vimos que la resta se resuelve sumando el
complemento de uno de los operandos. Si antes de que un operando entre al
sumador lo hacemos pasar por una EXOR por bit (todas con la misma línea
de control):

- **Sumando** (LC = 0): cada EXOR deja pasar el bit sin cambios.
- **Restando** (LC = 1): cada EXOR invierte el bit, entregando el
  operando complementado.

Así, poniendo una EXOR por cada bit de la ALU (4, 8, 16, 32...) y
controlándolas todas con la misma línea de control, un sumador se
convierte en sumador/restador según el valor de esa línea.

---
[⬅ Volver al índice](README.md)
