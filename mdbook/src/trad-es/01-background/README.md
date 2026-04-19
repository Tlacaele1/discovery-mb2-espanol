# Background

Estás a punto de escribir en Rust sobre el metal desnudo (bare-metal).  Quizás nunca hayas hecho algo como esto. Es *perfecto*, ¡Bienvenide a la aventura!

Deberíamos empezar respondiendo algunas dudas básicas que tal vez tengas:


* **¿Qué es un microcontrolador?**

  Un microcontrolador es un sistema en un chip (*System on a Chip, SoC*). Mientras tu computadora se conforma  de distintos componentes discretos tales como el procesador, la RAM, el almacenamiento, un puerto Ethernet, etc; un microcontrolador tiene todos esos componentes integrados en un pequeño chip o encapsulado (package). Esto posibilita crear sistemas con menos piezas.


* **¿Qué puedes hacer con un microcontrolador?**

  ¡Hartas cosas! Los microcontroladores son el núcleo de lo que llamamos "Sistemas *embebidos*". Estos sistemas están en todas partes, pero, normalmente no te fijas en ellos. Los sitemas embebidos controlan las máquinas que te lavan la ropa, o las que imprimen tus documentos, y hasta aquellas que te cocinan. Los sistemas embebidos mantienen los edificios donde vives y trabajas en una temperatura agradable y, además, controlan los componentes que hacen arrancar y frenar a los vehículos en los que te transportas.

  La mayoría de los sistemas embebidos operan sin intervención del usuario. Aún si estos muestran una interfaz de usuario, como las lavadoras, la mayoría de sus operaciones las hacen por sí mismos.

  A menudo este tipo de sistemas se emplean para *controlar* procesos físicos. Para que esto sea posible, los sistemas embebidos tienen uno  o más dispositivos que les comunican el estado del mundo exterior ("sensores"), y uno o más disposistivos que les perrmiten cambiar cosas ("actuadores"). Por ejemplo, un sistema de control climático en un edificio debería tener:
     
    - Sensores que midan la temperatura y humedad en distintos puntos del edificio.
    - Actuadores que controlen la velocidad de los ventiladores.
    - Actuadores que permitan calentar o enfriar el edificio (añadir o extraer calor). 

* **¿Cuándo debería usar un microcontrolador?**

  Muchos de los sistemas embebidos listados arriba podrían implementarse con una computadora corriendo Linux (con una "Raspberry Pi", por ejemplo). Entonces, ¿por qué usar un microcontrolador? Parece que hacerlo implicaría mayor dificultad al desarrollar un programa.

  Algunas razones para hacerlo podrían incluir:

  * *Costo:* un microcontrolador es mucho más barato que una computadora de propósito general. No sólo son más baratos, además, requieren menos componentes eléctricos externos para funcionar. Esto provoca que las Placas de Circuito Impreso (Printed Circuit Boards, PCB) sean más pequeñas y más baratas de diseñar y manufacturar.

  * *Consumo eléctrico:* la mayoría de los microcontroladores consumen una fracción de la potencia de un procesador completo. En aplicaciones que utilizan baterías esto hace una enorme diferencia.

  * *Responsividad:* para cumplir su propósito, algunos sistemas embebidos deben reaccionar siempre dentro de un lapso muy estricto (el sistema antibloqueo en los frenos de un coche, por ejemplo). Si el sistema no reacciona dentro del tiempo límite (*deadline*), podría ocurrir una falla catastrófica. Este plazo, o *deadline* para usar la terminología más aceptada, se denomina requerimento de "tiempo real estricto" ("hard real time"). Un sistema embebido sujeto a un deadline es llamado "sistema de tiempo real estricto" ("hard real-time system"). Una computadora de propósito general con un sistema operativo suele compartir sus recursos de procesamiento con distintos componentes de software. Esto provoca que no se pueda garantizar la ejecución de un programa con una restricción tan específica como un deadline.

  * *Confiabilidad:* en sistemas con pocos componentes (tanto de hardware como de softwarte), ¡hay menos cosas que pueden fallar!

  * **¿Cuándo *no* debería usar un microcontrolador?**
  
  A menudo los microcontroladores no son buenos con carga computacional pesado. Para mantener los costos y el consumo de potencia bajos, los recursos computacionales disponibles de los microcontroladores son limitados.

  Comúnmente, los microcontroladores pueden ejecutar menos instrucciones por segundo que los "grandes" procesadores. Sus partes más lentas podrían ejecutar "solo" unas pocas millones de instrucciones por segundo. Además, la cantidad de trabajo por instrucción es típicamente menor. Por lo general, los componentes de un microcontrolador son de "32 bits", pero los de "16 bits" no son raros: esto puede traducirse en más instrucciones para trabajar con los tipos de datos comunes de Rust. La mayoría de los microcontroladores tienen una caché pequeña o, simplemente no la tienen, lo que significa que las instrucciones sólo se pueden ejecutar tan rápido como puedan pasar de la memoria al procesador.

  Algunos microcontroladores no tienen soporte para operaciones con punto flotante (floating point). En esos dispositivos, hacer una simple suma de precisión con este tipo de números puede tomar cientos de ciclos de la CPU.

  Finalmente, los microcontroladores suelen tener memoria limitada. Los tamaños de memoria pueden ser tan reducidos como 16KB para las instrucciones del programa y 4KB para los datos, esto provoca que programar para este tipo de sistemas sea bastante retador. Dado que el costo de la memoria interna por unidad y el consumo de potencia se disparan al aumentar su tamaño, el procesador con el que estaremos trabajando aún tiene "solo" 512KB para el programa; menos que una "computadora real".

  * **¿Por qué usar Rust y no C?**

  Por fortuna, no tengo que convecerte aquí, pues, probablemente ya estés familiarizade con las diferencias entre Rust y C. Pero, hay un punto que me gustaría comentar: el manejo de paquetes. C no tiene un manejador de paquetes oficial ni una solución ampliamente aceptada para esto, mientras que Rust tiene Cargo. Esto hace el desarrollo *mucho* más sencillo. Y, en mi opinión, un manejo sencillo de paquetes fomenta la reutilización de código dado que las librerías pueden ser integradas fácilmente a una aplicación, lo que a su vez es bueno para las librerías, pues estas pueden tener más "pruebas en el campo de batalla".

  * **¿Por qué no debería usar Rust?**

  o, ¿por qué debería preferir C en lugar de Rust?

  El ecosistema de C es más maduro. Ya existen soluciones listas para usar para muchos problemas. Si necesitas controlar un proceso sensible al tiempo, puedes tomar uno de los sistemas operativos en tiempo real (Real Time Operating Systems, RTOS) comerciales existentes y resolver tu problema. No hay RTOS comerciales ni aptos para producción en Rust (al menos hasta la escritura de este texto) así que tendrías que escribir el tuyo propio o probar uno de los que están en desarrollo. Puedes encontrar una lista de ellos en el repositorio [Awesome Embedded Rust].

[Awesome Embedded Rust]: https://github.com/rust-embedded/awesome-embedded-rust#real-time-operating-system-rtos
