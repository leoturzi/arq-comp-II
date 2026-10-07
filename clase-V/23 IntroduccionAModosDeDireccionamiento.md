# 23 Introducción a los Modos de Direccionamiento

[⬅ Volver al índice](README.md)

Arrancamos el tema de las instrucciones: cómo se estructuran y de qué
formas pueden acceder a los datos. Antes, un repaso de lo que ya
tenemos construido.

## Repaso: lo que ya tenemos

- **Lógica combinacional**: unión de circuitos lógicos tal que, ante una
  configuración de entrada, el circuito llega siempre a una salida
  particular y única. Con ella armamos la **UAL**, nuestra construcción
  máxima en este tipo de lógica.
- **Circuitos biestables** (flip-flops): ahí aparece el concepto de
  memoria. Construimos una memoria RAM y una memoria ROM.
- **Registros**, armados con flip-flops maestro-esclavo.
- **Datapath** (cómo organizar registros entre sí para intercambiar
  datos) y repaso de la configuración de la **unidad de control**.

Con memorias, registros, UAL, datapath y unidad de control ya estamos en
condiciones de armar un modelo de procesador capaz de ejecutar
instrucciones. Falta ver las instrucciones en sí.

## Cómo se aborda un procesador nuevo

Supongamos que entramos a una fábrica de lavarropas y nos piden que, al
apretar el botón, se prenda la luz verde en vez de la roja. Es muy
probable que el programa del lavarropas esté en lenguaje máquina. Para
entender un procesador desconocido y poder modificar su programa hay que
hacer siempre dos pasos:

1. **Noción de la arquitectura**: qué registros tiene, de cuántos bits
   es cada uno, qué función cumple cada uno y cómo se relaciona con el
   bus de direcciones.
2. **Set de instrucciones**: no se puede comprender sin el paso 1.

Las instrucciones son numerosas, así que hay dos formas de abordarlas:

- **Por su función**: transferencia de información, operaciones de cada
  tipo, saltos, etc.
- **Por la forma en que acceden a los datos**: esto es lo que vamos a
  ver hoy, y son los **modos de direccionamiento**.

## Estructura de una instrucción

En lenguaje máquina una instrucción ocupa entre una y cinco posiciones de
memoria. Tiene dos elementos:

- **Código de operación**: le indica al procesador qué hacer.
- **Dato asociado**: le indica con qué tiene que hacer lo que tiene que
  hacer.

## Los procesadores de Sistemas de Computación I

En Sistemas de Computación I trabajamos con dos procesadores:

- Un **8086 / 80286**, el procesador inicial de la familia x86 (de la
  que descienden los procesadores Intel y compatibles de hoy).
- Un **modelo de procesador** propio.

En ambos las instrucciones eran similares. Ejemplo en el debugger (el
`debug` de DOS): para mover un dato de memoria al registro AX se cargaba
en memoria:

```
A1 00 20
```

- `A1` es el código de operación: "andá a la memoria, tomá un dato y
  llevalo a AX".
- `00 20` es el dato asociado: la dirección de memoria 2000 (en
  hexadecimal), con el byte menos significativo primero.

En el modelo de procesador pasaba lo mismo: una instrucción con código de
operación (`0111`) y un dato asociado que era una dirección de memoria.

Lo que hay en memoria son bits; si los escribimos en hexadecimal es solo
porque es una forma más cómoda de leerlos.

## El debugger y DOSBox

El `debug` es un programa de la época de DOS que permite depurar
programas. No usamos un ensamblador común porque el debugger ejecuta
**una instrucción por vez**, lo cual ayuda a aprender cómo funcionan las
instrucciones.

- Con él escribimos instrucciones y programas en la memoria de un
  procesador emulado (8086 u 80286) y los ejecutamos.
- DOS tenía una interfaz alfanumérica: todo se hacía escribiendo en el
  teclado el nombre del programa a ejecutar. El **prompt** de DOS es
  equivalente a la consola de Windows actual (`command`).
- Un sistema operativo de 64 bits no puede trabajar a la vez con 64 y 16
  bits, así que el `debug` no corre en sistemas de 64 bits (sí corría en
  todos los de 32). Por eso se usa **DOSBox**, un emulador de DOS.
  Conviene cargarlo desde el archivo que pasa el profesor, para saber
  que está limpio.
- El comando `?` muestra la ayuda con todos los comandos.

Comandos que vamos usando:

| Comando | Qué hace                                                         |
| ------- | ---------------------------------------------------------------- |
| `E`     | Escribe bytes en memoria a partir de una dirección (*enter*).    |
| `A`     | Ensambla (*assemble*): carga instrucciones en mnemónico.         |
| `U`     | Desensambla (*unassemble*): interpreta lo que hay en memoria.    |
| `R`     | Muestra los registros (y la próxima instrucción a ejecutar).     |
| `T`     | Ejecuta una sola instrucción (*trace*).                          |

Con `E` y una dirección (por ejemplo `E 1000`) el debugger muestra lo que
hay en esa dirección y permite escribir un nuevo byte; con la barra
espaciadora se avanza al siguiente. Así se escribía el programa en
Sistemas de Computación I, byte por byte en hexadecimal.

## Lenguaje mnemónico y ensamblador

Escribir programas en códigos hexadecimales obligaría a memorizar
infinidad de códigos. Por eso se usa el **lenguaje mnemónico**: un código
que, al pronunciarlo, suena parecido a lo que la instrucción hace.
En vez de decirle al procesador `A1 00 20`, escribimos:

```
MOV AX, [2000]
```

- El movimiento siempre va **de derecha a izquierda**: a la derecha el
  origen y a la izquierda el destino.
- Los **corchetes** indican "el contenido de" la dirección de memoria
  (escrita en hexadecimal). Acá, el contenido de la dirección 2000 se
  mueve a AX.
- La sintaxis está pautada: si escribimos `MB` en vez de `MOV`, no lo
  entiende y da error.

Más ejemplos:

```
ADD AX, [2002]    ; suma a AX el contenido de la dirección 2002
SUB AX, [2004]    ; resta de AX el contenido de la dirección 2004
MOV [2006], AX    ; guarda AX en la dirección 2006
```

En `ADD` y `SUB` el resultado se guarda en AX, porque el destino es
siempre el de la izquierda.

Para pasar cada instrucción mnemónica a su formato en lenguaje máquina
(código de operación + dato asociado) hace falta un software: el
**ensamblador** (*assembler*). Se suele decir "trabajamos en assembler"
cuando en realidad el assembler es el programa que traduce el lenguaje
mnemónico al código que entiende el procesador.

## El programa de Sistemas de Computación I

Este es el programa del trabajo práctico de Sistemas de Computación I:

```
R = P + Q - T
```

donde 2000, 2002, 2004 y 2006 son las direcciones de P, Q, T y R
respectivamente (en el trabajo práctico se usaban otras direcciones). El
programa se carga a partir de la dirección 1000.

La primera instrucción se escribió a mano con `E`: ocupa 3 bytes (1000,
1001 y 1002), así que para ensamblar las siguientes con `A` hay que
posicionarse en 1003 con `A 1003`.

| Dirección | Bytes         | Instrucción      |
| --------- | ------------- | ---------------- |
| 1000      | `A1 00 20`    | `MOV AX, [2000]` |
| 1003      | `03 06 02 20` | `ADD AX, [2002]` |
| 1007      | `2B 06 04 20` | `SUB AX, [2004]` |
| 100B      | `A3 06 20`    | `MOV [2006], AX` |

Si nos equivocamos al escribir una instrucción, se la pisa volviendo a
ensamblar en esa dirección.

Para ver qué quedó en memoria:

- Con `E` se recorre byte por byte lo que hay a partir de una dirección.
- Con `U 1000 100E` el debugger interpreta lo que hay entre 1000 y 100E y
  muestra, instrucción por instrucción, sus bytes y cómo los interpreta.

## Cómo se guardan los datos de más de 8 bits

Dato de 16 bits: en el primer byte va el byte que tiene la unidad
(menos significativo) y en el segundo el que le sigue a la izquierda. Por
eso la dirección 2000 aparece como `00 20` dentro de la instrucción, y un
dato `CC00` aparece en memoria como `00 CC`.

Con 32 bits es igual: primero el byte de la unidad, después el que le
sigue a la izquierda, y así sucesivamente.

Se hace así porque es más fácil de alinear en el bus de datos: sabemos
que el primer byte es el que va por la línea **D0**, la que contiene la
unidad.

El debugger, en cambio, al mostrar el dato en un registro o con `U` lo
"da vuelta" y lo presenta en el orden en que los humanos escribimos los
números (`CC00`).

## Ejecución paso a paso

1. Con `R` vemos los registros. El **IP** está apuntando a `0100`, pero
   nuestro programa empieza en 1000: hay que modificar el IP para que
   apunte a la primera instrucción; si no, el debugger no ejecuta lo que
   cargamos.
2. Con el IP en 1000, `R` muestra la próxima instrucción a ejecutar:
   `A1 00 20`, interpretada como `MOV AX, [2000]`. En la dirección 2000
   hay un dato `CC00` (en memoria, `00 CC`).
3. Con `T` ejecutamos solo esa instrucción: el procesador va a la
   dirección 2000, toma el dato y lo guarda en AX. Resultado: AX = `CC00`.

## Modo de direccionamiento directo

En esta instrucción, y en todas las que usamos en Sistemas de
Computación I, el dato asociado (2000, 2002, 2004, 2006) era una
**dirección de memoria**: ir a esa dirección, tomar el dato y operar con
él.

Este modo de direccionamiento, en el que el dato asociado es la
dirección de memoria donde está el dato, se llama **modo de
direccionamiento directo**.

---

[⬅ Volver al índice](README.md)
