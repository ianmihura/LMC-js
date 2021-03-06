# Computadoras
¿Cuál es la computadora más básica que existe? Muchos pensarán en cosas como [Raspberry Pi](https://www.raspberrypi.org/) o [Arduino](https://www.arduino.cc/), y con toda la razón: caben en un bolsillo. Aunque son computadoras muy chiquitas, son muy potentes—tienen enchufes de HDMI, ethernet, varios USB y hasta memoria expandible con SD. Serán quizás las computadoras útiles más pequeñas, pero no las más básicas. Para pensar en la computadora más básica hay que despojarlas aún más, llevarlas a los fundamentos; ¿qué es una computadora?

Una computadora es un sistema que almacena y procesa datos, de acorde a instrucciones. Nada más. La electrónica y los chips son relativamente supérfulos; no olvidemos que antes existía el trabajo de "computador", quien computaba cuentas (típicamente jóvenes universitarios buscando su primer trabajo). Aún así, no llamaría a una persona la computadora más *básica* ya que las personas somos máquinas altamente complejas. No, estamos buscando el sistema más básico, más simple, despojado de todo lo que no sea esencial. Buscamos modelos de computadoras, quizás abstractos, pero que computen lo necesario.

La Computadora del Pequeño Hombre (Little Man Computer - LMC) es uno de estos modelos de computadora minimalista, sin aplicaciones ni gráficos, sólo una unidad que procesa y almacena datos. Tiene su propio *idioma* de instrucciones para escribir programas, pero carece de abstracciones como *variables* o *funciones*, a las que estamos acostumbrados los programadores. Realmente parece más binario que `python` o `C` (aunque, las instrucciones de LMC son en decimal). Al final de esta nota hay una implementación de un LMC para que puedan investigar! Durante la nota se enseñará a usarla, asique adelante: es una forma simple de acercarse al estudio de la arquitectura de computadoras, y en específico a la arquitectura Von Neumann.

### Arquitecturas - Turing y Lambda
Si hubieran pensado en una [*Turing machine*](https://en.wikipedia.org/wiki/Turing_machine) cuando buscabamos la computadora más básica, no se hubieran equivocado. Mucha gente conoce las *turing machines*, gracias a la fama de Allan Turing. En una frase: una *turing machine* es un modelo abstracto de computadora, que funciona manipulando símbolos en una cinta de acorde a una tabla de referencia. Pero, a pesar de ser Turing el padre de la computación y su *turing machine* un fundamento básico en la computación, nuestras computadoras de uso diario no estan armadas en base a esa arquitectura. ¿Cómo puede ser? Las Turing machines son conceptos teóricos, que sirven para entender y describir el dominio de problemas computables (lo que se conoce como *turning completo*). Es decir, son un modelo abstracto; no tienen como objetivo armar una máquina eficiente para ningún uso.

Otra respuesta relativamente válida era el [cálculo lambda](https://en.wikipedia.org/wiki/Lambda_calculus). Las *turing machines* son, de algún modo, similares al cálculo lambda, en que son otro método para modelar la computabilidad de funciones. Y aunque sean abismalemnte distintos en su implementación, el cálculo lambda y las *turing machines* describen lo mismo y son ambas igual de poderosas. Es decir, cualquier programa que se pueda escribir en uno, se puede emular en el otro, y se puede ejecutar en una computadora Von Neumann. Pero, al ser más abstractas, y en especial al carecer de estado interno, la programación lambda y las turing machines son menos "eficientes" para programar y menos intuitivas para enteder que las computadoras Von Neumann. Es más, la programación de assembly en una arquitecturas Von Neumann se asemeja más a programas imperativos, como el famoso C.

### Von Neumann
Se dice que, si Turing es el padre de la computacion, John Von Neuman es el padrino talentoso, práctico y carismático. Un gran personaje de la historia de la computación, prodigio pero extrovertido, interesado en matemática, computación y hasta física cuántica (formó parte del proyecto Manhattan en el 40). Inventó el famoso algorítmo *merge sort* para el ordenamiento de listas, y trabajó en el comité de científicos en la creación de la [ENIAC](https://en.wikipedia.org/wiki/ENIAC), la primera computadora digital de propósito general, y su sucesora, la [EDVAC](https://en.wikipedia.org/wiki/EDVAC).

Durante el desarrollo de la EDVAC, Von Neumann escribió su famoso ["Reporte de la EDVAC - primer borrador"](https://sites.google.com/site/michaeldgodfrey/vonneumann/vnedvac.pdf?attredirects=0&d=1). En él, describe una arquitectura de computadora diferente, donde los datos y la memoria de la máquina se almacenan internamente, en el mismo espacio. Hay que tener en cuenta que antes de la ENIAC y la EDVAC, las computadoras no guardaban datos internos, y si lo hacían, eran sólo variables que se desconfiguraban al reiniciar la máquina. Los programas eran simepre externos, codificados en tarjetas perforadas o cintas al estio de las turing machines. Este concepto de "estado interno" prometía más eficiencia y permitía programas más dinámicos que podían cambiar su propio código, aunque esto último luego pasó de moda, por sus posibles problemas.

Una arquitectura Von Neumann básicamente consiste en un almacén de memoria (Store), un control de flujo del programa (control, o counter), una unidad aritmética (acumulador) y dispositivos de entrada y salida de datos. Un detalle, hoy en día la unidad de control y la aritmética están incluídos todos dentro del CPU.
```
    in  >>  Store  >>  out
            ↑   ↑ 
    (control)   (unidad aritmética 
                / acumulador)
```
Hay mucho que decir sobre esta arquitectura, y los invito a seguir investigando. Aquí nos basta con saber que todos estos componentes forman parte de la arquitectura LMC.

### Arquitectura LMC
La arquitectura del LMC fue creada por el Dr. Stuart Madnick en 1965, con fines educativos. La creó en base a la arquitectura Von Neumann, sabiendo que todas las computadoras modernas estaban (y probablemente estarían) también construidas a partir de esta arquitectura, entonces entender un LMC es un primer paso hacia entender la mecánica de cualquier computadora moderna.

Bien, comencemos pensando a la computadora como una habitación, en la cual hay varias cajas y un pequeño hombre encerrado dentro. En un lado de la habitación hay 100 cajas llamadas Memoria (Store). Cada una de estas (cada mailbox) tiene:
* Una dirección (0 - 99)
* Memoria interna (000 - 999)

Luego, del otro lado de la habitación hay 2 cajas, llamadas *Input* y *Output*.
En el centro de la habitación hay una mesa (también llamada workbench) con 2 cajas más: llamadas *Acumulador* y *Contador*.
* Acumulador (000-999): el número de trabajo central. Viene acompañado de 2 funciones: *suma* y *resta*, y el pequño hombre también puede reeplazar el valor del Acumulador cuando así se lo indique.
* Contador (0-99): el número que usa el pequeño hombre para determinar el flujo del programa. Normalmente, en cada ciclo (loop) se agrega (1) al Contador, haciendo que el programa avance hacia adelante. Como excepción, hay 3 distintas instrucciones que permiten realizar saltos no lineales (condicionados y no condicionados). Esto se realiza reemplazando el valor del Contador por algún otro, entonces, cuando el pequeño hombre mire el Contador en el siguiente ciclo, la instrucción que ejecutará será la de esa dirección. Por ejemplo, restablecer el Contador a cero hace que el programa comience nuevamente desde el principio.

Las cajas de Memoria contienen sólo un número interno, que puede ser interpretado tanto como instrucción o como dato, y a simple vista no es posible diferenciarlos. Es más, el pequeño hombre **no los diferencia**. Esto es así por diseño, y en su momento era considerado una buena idea, aunque hoy se desfavorece ya que pude llevar a muchos problemas o ataques como [Buffer overflow](https://en.wikipedia.org/wiki/Buffer_overflow). Por lo tanto hay que tener cuidado. Una buena costumbre es separar "áreas", una para datos y otra para el programa, y asegurarse de detener el programa antes de alcanzar una dirección de memoria para que no sea interpretada como una instrucción. 

Entonces, para utilizar la LMC el programador carga el programa en las cajas de Memoria, carga el Input, e indica al pequeño hombre que comience a trabajar. Las dos siguientes secciones describen más técnicamente esto.

### Ciclo (loop)
Para ejecutar un programa, el pequeño hombre debe realizar los siguientes pasos:
1. Revisa el Contador; este contiene la dirección de la próxima instrucción.
2. Busca el valor dentro de la caja con esa dirección.
3. Incrementa el contador del programa (+1).
4. Decodifica la instrucción, según lo que corresponda:
   Recupera los datos (LDA, IN), o bien,
   Edita el Acumulador (ADD, SUB), o bien,
   Edita el Contador (BR, BRZ, BRP), o bien,
   Almacena los datos (STO, OUT), o bien,
   Se detiene (HLT).
5. Repite el cilco (si no se detuvo).

### Instrucciones

Se permite programar de 3 formas distintas: *codigo máquna* (en base decimal), *código alias* (el más legible y fácil de usar) y *codigo index* o *assembly* (punto medio entre ambos). Cabe destacar que la máquina sólo lee código máquina, y el resto son *syntactic sugar* para legibilidad.

| Código Máquina | Código Index | Descripción | 
|---------|---------|---------| 
| 1xx | ADD xx | Agrega el valor de la dirección xx al Accumulador (sin cambiar el valor de la memoria). | 
| 2xx | SUB xx | Resta el valor de la dirección xx al Accumulador (sin cambiar el valor de la memoria). | 
| 3xx | STO xx | (STORE) Reemplaza el valor de la memoria xx por el valor actual del Accumulador | 
| 5xx | LDA xx | (LOAD) Reemplaza el valor del Accumulador por el valor de la memoria xx | 
| 6xx | BR xx <br> BRA xx | (BREAK) Reemplaza el valor del Contador por el valor xx | 
| 7xx | BRZ xx | (BREAK IF ZERO) Si el Accumulador es igual (o menor que) zero, reemplaza el valor del Contador por el valor xx - sin editar el valor del Accumulador. | 
| 8xx | BRP xx | (BREAK IF POSITIVE) Si el Accumulador es mayor a zero, reemplaza el valor del Contador por el valor xx - sin editar el valor del Accumulador. | 
| 901 | IN xx | Reemplaza el valor del Accumulador por el primer valor en la caja de Inputs, quita ese valor del *stack* de Inputs. | 
| 902 | OUT xx | Agrega el valor actual del Accumulador a la caja de Outputs. | 
| 000 | HLT xx <br> COB xx | (HALT) Detiene el programa. | 
| xxx | DAT xxx | Dato; espacio de memoria simple. |

Un ejemplo en el mundo real: las [instrucciones de la EVM: *Ethereum Virtual Machine*](https://github.com/djrtwo/evm-opcode-gas-costs/blob/master/opcode-gas-costs_EIP-150_revision-1e18248_2017-04-12.csv)—prestar atención a las primeras 2 columnas, y la última.

### Ejemplos
Aquí hay algunos programas. El primero toma 2 Inputs y devuelve la suma de ambos.
```
# assembly          | # máquina
                    | 
    in              |   901
    sto 99          |   399
    in              |   901
    add 99          |   199
    out             |   902
```
El segundo toma un número y devuelve una cuenta regresiva hasta zero. Está escrito usando tags (código alias) con su traducción. Es la más fácil de usar y leer ya que se usan tags en vez del número de la memoria.
```
# assembly (alias)  | # máquina
                    | 
    in              |   901
    out             |   902
LP  brz END         |   706
    sub ONE         |   207
    out             |   902
    br  LP          |   602
END hlt             |   0
ONE dat 1           |   1
```

### Implementación
*A programar!*

A continuación van a encontrar una interfaz (bastante simple) de una implementación en `javascript` de un LMC. Están invitados a usarla! Tengan en cuenta que:
* La máquina no está definida para números negativos, aunque se puedan operar, y la instrucción BRZ los incluya.
* Los programas pueden estar escritos en código assembly o código máquina, pero la máquina sólo funciona con código máquina. Recomiendo programar con código assembly que es más intuitivo (chequear la sintáxis de *alias* en el ejemplo Quine).
* Hay que separar cada instrucción de la memoria, y cada valor del Input, con un `Enter`
* Los programas pueden tener máximo 100 entradas / renglones (incluyendo datos), e indefinidos Inputs
* Para cambiar de versión de traducción (index - alias) hay un switch bajo el botón `More`

Algunas ideas para programar:
* Multiplicación
* Potencia de 2
* Potencia de 3
* División con y sin resto
* Máximo o mínimo de 2 números
* Máximo o mínimo de 3 números

### Código Fuente
*Para developers*

Para seguir indagando, pueden abrir la consola con `F12` y buscar el código fuente. Tengan en cuenta que está en un iframe, y no en la ruta principal del sitio. Están bienvenidos a usarlo, editarlo y detenerse en cada punto para seguir su razonamiento. Para ejecutarla por consola, 1) carguen el array `Memory` con el programa a ejecutar (en código máquina); 2) carguen el array `Inputs` 3) ejecuten la función `Startup()`. El output se guarda en el array `Outputs`.

El código también está en [este repo](https://github.com/ianmihura/LMC-js). Si tenés alguna duda, comentario, o simplemente querés charlar sobre el tema, no dudes en contactarnos! Yo soy Ian, y estoy disponible a mi mail [mihura.ian@gmail.com](mailto:mihura.ian@gmail.com)