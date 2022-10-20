


<h1 align="center"> Universidad Nacional de Colombia <br>
Facultad de Ingeniería <br>
Depto. de Ing. Mecánica y Mecatrónica <br>
Robótica <br>
Laboratorio 4 - 4 Robótica- Cinemática Directa con robot Phantom x Pincher <br>
Miguel Angel Rincón Otálora <br>
Robinson Ordúz </h1>


## INTRODUCCIÓN
Este laboratorio se hace con el fin de aprender a ejecutar y supervisar la secuencia de movimientos del brazo robótico IRB140 
a través de un tablero eléctrico con pulsadores como entradas y luces piloto como salidas.
Con esta nueva característica el tablero eléctrico entra a ser parte del sistema robotizado junto con el brazo, el controlador,y 
el flexpendant; esta característica abre la posibilidad de no solo incluir pulsadores y pilotos sino diversos tipos de sensores y
actuadores dentro del sistema robotizado. 

## MATERIALES Y HERRAMIENTAS
- brazo robótico IRB140
- controlador ABB IRC5
- flexpendant
- herramienta de escritura con marcador borrable
- tablero inclinado

<h1 align="center"> Creación de entradas y salidas digitales en RobotStudio </h1>


Para crear las entradas y salidas digitales en RobotStudio en la pestaña de controlador, en el arbol de lado izquierdo se da clic en configuración y luego en I/O System, a continuación se da clic en Signal. 


![Pasos preliminares](https://user-images.githubusercontent.com/49238418/194683676-655eb5d0-1813-4713-a223-dc8530c88d47.png)

En esta sección se crean 4 señales nuevas, dos de entrada digital y otras dos de salida digital. Para esto se da clic derecho y luego se selecciona la opción Nuevo Signal, allí se asigna el nombre DI10_# para entradas y Do10_# para las salidas. A continuacion se asigna el tipo de señal, el cual evidentemente es Digital Imput para entradas y Digital Output para salidas. No se requiere de ninguna otra configuración adicional. Se da clic en Aceptar y se repite el proceso para las otras 3 señales.

![Creacion de señales](https://user-images.githubusercontent.com/49238418/194683951-6dbb039a-ab49-43bb-99f4-49e489c92bf4.png)

Las 4 señales deben verse como en la primera figura.

A continuación es necesario reiniciar el controlador para hacer los cambios, para ello se va a la pestaña Controlador y se da click en el boton Reiniciar, se debe esperar a que el proceso termine.

Para hacer uso de estas señales se va a la pestaña de simulación y se da click en el botón Simulador de E/S. Esta acción depliega un panel donde pueden verse todas las señales de entrada y salida del robot. Para facilitar la visualización de las señales creadas en Filtro se selecciona la opción Lista de usuarios y luego en Editar listas. 

![Panel de señales](https://user-images.githubusercontent.com/49238418/194684614-8dae9d41-411e-4072-bb6c-79f595391a8b.png)

Alli se crea una nueva lista, se seleccionan las cuatro señales y se da clic en aceptar, las 4 señales deben verse en el panel. 

![Editar listas](https://user-images.githubusercontent.com/49238418/194684645-c9a5efa3-a559-4ded-8ee3-77fdc835dc12.png)

Las señales ya estan creadas y pueden ser utilizadas.

<h1 align="center"> Uso de las señales creadas en un programa RAPID </h1>

A continuación se integran las señales en el programa RAPID anteriormente creado para que el robot IRB140 dibuje las iniciales RM. Para esto en la pestaña RAPID, en el arbol de la derecha se despliega el menú de RAPID y se da click en Module 1. De esta manera se puede ver el código del programa ya creado.

![Rapid](https://user-images.githubusercontent.com/49238418/194684930-2adcee93-c827-486f-a3ca-61fc88ffec7e.png)

En esta parte se introducen dos nuevos comandos de código, los cuales son:

```RAPID
WaitDI Señal, ValorEsperado;

```
Cando el puntero llega a esta línea, el comando WaitDI hace que la ejecución del programa permanezca en pausa hasta que la señal de entrada digital especificada tenga el valor esperado. Cuando esto ocurre, el progrmama simplemente continúa en ejecución. El segundo comando es:

```RAPID
SetDO Señal, NuevoValor;

```
Cuando el puntero llega a esta línea, el comando SetDO hace que la señal de salida digital especificada tenga un nuevo valor lógico, luego continúa con la ejecución del programa.

Se espera que el Robot ejecute la siguiente lista de pasos:

- Cuando se accione la primera señal de entrada, un pulsador del tablero de entradas y salidas, el actuador debe ir a posición de HOME y luego comenzar a dibujar en el tablero las iniciales RM como se hizo en el laboratorio 1.

- Una vez terminado, debe regresar a la posición de HOME y encender uno de los testigos del tablero, es decir, poner salida digital en estado lógico de encendido.

- A continuación, cuando se accione la segunda señal de entrada, otro pulsador, el actuador debe ir a la posición de MANTENIMIENTO, es decir, una posición definida por nosotros con el fin de facilitar el cambio de la herramienta en el plato.

- Una vez el actuador llegue a esta posición, el testigo que se encontraba encendido debe apagarse, es decir, poner la salida digital nuevamente en 0.

Esto se logra mediante la siguiente porción de código para la sección Main del programa RAPID.

```RAPID
        WaitDI DI10_1, 1;
        IrAHome;
        IrAIntermedio;
        Rexterna;
        IrAIntermedio;
        Rinterna;
        IrAIntermedio;
        M;
        IrAIntermedio;
        IrAHome;
        SetDO DO10_1, 1;
        
        WaitDI DI10_2, 1;
        IrAHome;
        IrAMantenimiento;
        SetDO DO10_1, 0;
```

Donde se encuentran las salidas y entradas definidas anteriormente. Los comandos se encuentran de tal manera que se cumple la secuencia esperada, se espera a que la señal DI10_1 esté en estado 1, a continuación se ejecutan las trayectorias para dibujar las iniciales RM igual que en el laboratorio anterior. Una vez termina, se pone la señal DO10_1 en estado lógico 1, se enciende el testigo. A continuación se espera a que la señal DI10_2 se ponga en 1 para ir a la posición de MANTENIMIENTO. Finalmente se pone la señal DO10_2 en estado lógico 0.

<h1 align="center"> Creación de la trayectoria IrAMantenimiento </h1>



MANTENIMIENTO es una posición absoluta como HOME e INTERMEDIO. Para determinarla lo que se hizo fue dentro del laboratorio de LabSIR, se movió uno de los actuadores hasta la posición mas cómoda con el uso del FlexPendant. Cuando esto se logró, se obtuvieron los siguientes ángulos para cada una de las articulacones.

![Posición mantenimiento](https://user-images.githubusercontent.com/49238418/194686037-322e7668-eb45-4a62-b0b3-396e498da457.png)
![FlexpendantAng](https://user-images.githubusercontent.com/49238418/194686072-4526d13a-d3e6-4a4b-8960-7a92fe5d8a06.png)

Los cuales son introducidos en una nueva posición absoluta en el controlador en RobotStudio, se genera la trayectoria para esta posición y a continuación se sincroniza el cambio con RAPID.

Con estos cambios realizados ya es posible simular el programa, activar entradas y visualizar salidas con ayuda del pánel de señales antes descrito.

<h1 align="center"> Uso del programa en el laboratorio LabSIR </h1>

Las señales definidas en el programa RAPID en RobotStudio no tienen los mismos nombres que en el cotrolador IRC 150 del laboratorio. Por tanto una vez cargado el programa en dicho controlador es necesario reemplazar estas señales por las reales. Para ello en el Program Editor del FlexPendant se selecciona cada una de estas en el programa y se podrá seleccionar la que corresponde en la realidad. Con ayuda de la sección Inputs and Outputs del FlexPendant es sencillo determinar cual pulsador y testigo corresponde a cual señal del tablero de entradas y salidas.

![SeñalFlex](https://user-images.githubusercontent.com/49238418/194686381-dddb0e74-c75f-4069-88d2-faea9308ee81.png)

A continuación se establece el WorkObject mediante el procedimiento de los 3 puntos explicado en el Laboratorio 1 y con estas configuraciones ya se puede ejecutar el programa y observar su funcionamiento en el robot real.







## CONCLUSIONES
- Desde el punto de vista de manipulación del brazo robótico, es conveniente tener un tablero eléctrico desde el que se pueden controlar 
ciertos parámetros y movimientos sin necesidad de usar el flexpendant que es mucho más susceptible a sufrir daños por golpes y caídas.
- La lectura y escritura de entradas y salidas digitales respectivamente, le permite al sistema robotizado monitorear su entorno y ejecutar 
diferentes rutinas de movimiento en función de ello.
