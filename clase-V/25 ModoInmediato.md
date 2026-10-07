# 25 Modo Inmediato

[⬅ Volver al índice](README.md)

En el **modo de direccionamiento inmediato**, el **dato asociado** de la
instrucción es **el propio dato a operar**. Ya no es una dirección de
memoria: es el dato.

## Cómo se escribe

En ensamblador:

```
MOV AX, 3000
```

Sin corchetes, lo que escribimos es un **dato**; con corchetes es una
**dirección**:

```
MOV AX, 3000      ; modo inmediato: 3000 es el dato
MOV AX, [3000]    ; modo directo: 3000 es la dirección donde está el dato
```

## Cómo se ve en memoria

Seguimos con el programa de la clase anterior, cargado con `A` a partir de
la dirección 1000. El comando mantiene la última dirección usada, así que
si lo invocamos sin argumentos continúa en la siguiente instrucción libre:

| Dirección | Bytes         | Instrucción      | Modo      |
| --------- | ------------- | ---------------- | --------- |
| 1000      | `A1 00 20`    | `MOV AX, [2000]` | directo   |
| 1003      | `A3 04 20`    | `MOV [2004], AX` | directo   |
| 1006      | `03 06 02 20` | `ADD AX, [2002]` | directo   |
| 100A      | `B8 00 30`    | `MOV AX, 3000`   | inmediato |
| 100D      | `05 00 30`    | `ADD AX, 3000`   | inmediato |

Comparemos las dos formas de `MOV` hacia AX:

| Instrucción      | Código de operación | Dato asociado | El dato asociado es…         |
| ---------------- | ------------------- | ------------- | ---------------------------- |
| `MOV AX, [2000]` | `A1`                | `00 20` (2000) | una **dirección** de memoria |
| `MOV AX, 3000`   | `B8`                | `00 30` (3000) | el **dato** mismo            |

- El código de operación `B8` es el que indica "mover a AX, en modo de
  direccionamiento inmediato". En binario es `1011 1000`; lo escribimos
  en hexadecimal para acordarnos, porque nadie recuerda una cadena de
  ceros y unos.
- El dato 3000 se guarda como `00 30`: primero el byte de la unidad.
- `ADD AX, 3000` se codifica como `05 00 30`: otro código de operación
  (`05`) con el mismo tipo de dato asociado.

Con `U 1000 100D` se ve que lo escrito antes quedó igual y que en 100A
aparece `B8`, en 100B `00` y en 100C `30`.

## Ejecución paso a paso

Para ver la diferencia cargamos datos en la memoria y dejamos el **IP**
apuntando a 1000. Recordar que el IP siempre contiene la dirección de
memoria de la próxima instrucción.

Datos: 1111 en la dirección 2000, 2222 en la 2002 y 3333 en la 3000. La
dirección 3000 no se usa en ningún momento: el modo inmediato no la
consulta; la cargamos solo para que haya un dato ahí.

| Instrucción      | Qué ocurre                                                                    | Resultado               |
| ---------------- | ----------------------------------------------------------------------------- | ----------------------- |
| `MOV AX, [2000]` | Se copia a AX el contenido de la dirección 2000.                              | AX = 1111               |
| `MOV [2004], AX` | Se copia AX a la dirección 2004, pisando lo que había (`CCCC`).               | [2004] = 1111           |
| `ADD AX, [2002]` | Directo: se suma a AX el **contenido** de la dirección 2002.                  | AX = 1111 + 2222 = 3333 |
| `MOV AX, 3000`   | Inmediato: no se accede a memoria; AX pasa a valer el dato de la instrucción. | AX = 3000               |
| `ADD AX, 3000`   | Inmediato: se suma a AX el dato 3000.                                         | AX = 3000 + 3000 = 6000 |

Observaciones:

- En `MOV AX, 3000` no importa lo que haya en memoria; lo que se copia a
  AX es el 3000 que está dentro de la instrucción. AX queda pisado por
  3000.
- `MOV` en realidad es una **copia**: el origen no se borra, solo se
  copia su valor al destino.
- La instrucción se ejecuta con `T`; después de cada una el IP pasa a
  apuntar a la siguiente.
- Lo que aparece en memoria después de la última instrucción es basura
  que ya estaba ahí.

## Uso típico en programas de alto nivel

Casi nunca vamos a programar en ensamblador, pero sí lo hacemos
indirectamente cuando compilamos un programa en un lenguaje de alto nivel
(por ejemplo C). Ahí se dan ciertas pautas que, sin ser obligatorias,
ocurren en general.

- **Variables → modo directo.** El compilador le asigna a cada variable
  un lugar de la memoria (por ejemplo, la variable `P` en la dirección
  2000). Las instrucciones en lenguaje máquina no dicen `P`, dicen
  `2000`. Por eso el modo directo se suele usar para acceder a
  **variables**.
- **Constantes → modo inmediato.** Si en alto nivel escribimos
  `minutos = horas * 60`, el 60 es una constante: el compilador lo mete
  en el programa mediante una instrucción en modo inmediato, por ejemplo
  `MOV AX, 003C` (`3C` hexadecimal es `0011 1100` en binario: 32 + 16 +
  8 + 4 = 60).

La diferencia importante:

| Elemento   | Dónde queda                                    | ¿Se puede modificar?                          |
| ---------- | ---------------------------------------------- | --------------------------------------------- |
| Variable   | Área de datos de la memoria                    | Sí: las distintas instrucciones la modifican. |
| Constante  | Dentro de la instrucción (código del programa) | No: el código del programa solo se carga.     |

Son usos distintos de las instrucciones.

---

[⬅ Volver al índice](README.md)
