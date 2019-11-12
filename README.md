# 1.b)
## Documentación del ejemplo 1

Dentro del archivo se encuentran 9 ejemplos de FreeRTOS. Si bien cada uno cumple una función diferente, todos comparten ciertas características propias del uso del sistema operativo.

<p align="center">
  <img src="img_pto_1/setup_hardware.png"/>
</p>

<p align="center">
  Figura 1: Función donde se configura para el primer uso, a la placa.
</p>

A continuación se declaran las funciones que, durante la ejecución del programa, representan las tareas que ejecutará el sistema operativo.

<p align="center">
  <img src="img_pto_1/declaracion_de_funciones.png"/>
</p>

<p align="center">
  Figura 2: Declaración de las funciones que representan las tareas usadas en el programa.
</p>

La macro mainDELAY_LOOP_COUNT se utiliza posteriormente dentro del programa principal. Lo siguiente es definir estas tareas. 

<p align="center">
  <img src="img_pto_1/task_1.png"/>
</p>

<p align="center">
  Figura 3: Definición de la tarea 1.
</p>

En la figura 3, la tarea cumple con el prototipo que debe tener es decir, retornar void y recibir un puntero a void. La tarea solamente enciende un LED y envía el mensaje de la variable “pcTaskName”: “Task 1 is running\r\n”. Por último realiza un delay con un ciclo for. Es un delay de la tarea, es decir, no es equivalente a invocar a la función vTaskDelay, que sí cede el control de CPU, hasta que expire ese timer. Entonces el rtos seguirá atendiendo a la tarea, es decir, seguirá en estado Ready. En la figura 4 se ve la tarea 2.

<p align="center">
  <img src="img_pto_1/task_2.png"/>
</p>

<p align="center">
  Figura 4: Definición de la tarea 2.
</p>

La tarea 2 es idéntica a la tarea 1, solamente que apaga el LED y envía el mensaje “Task 2 is running\r\n”. 

Antes de continuar con el ejemplo, se analiza el archivo de configuración del FreeRTOS, “FreeRTOSConfig.h” para saber en qué modo está funcionando el sistema operativo.

<p align="center">
  <img src="img_pto_1/config_rtos.png"/>
</p>

<p align="center">
  Figura 5: Captura del archivo FreeRTOSConfig.h, donde se puede ver cómo se configura al sistema operativo.
</p>

El sistema está configurado en modo **preemptive**, se definen **8** prioridades diferentes (tener en cuenta que esto corresponde a todos los ejemplos, no solo al 1), el tick interrupe al sistema cada **1000 ticks**.

Continuando con el main del ejemplo, lo primero que se hace es invocar a la función de la imagen 1, que configura la placa para su uso. Luego se imprime el mensaje *\r\nExample 1 - Creating tasks\r\n*. A continuación, en la imagen 6, se crea la tarea 1 invocando a la función **xTaskCreate**.

<p align="center">
  <img src="img_pto_1/task_create.png"/>
</p>

<p align="center">
  Figura 6: Invocación de la función xTaskCreate, para la creación de las tareas 1 y 2. 
</p>

La función xTaskCreate es la encargada de crear la tarea correspondiente para que luego sea utilizada por el sistema operativo FreeRTOS. Esta recibe un puntero a la función asociada a la tarea, una string con el nombre de dicha tarea, el tamaño del stack en RAM que se le va a asignar a la tarea, los parámetros que utiliza la función, la prioridad de la misma y el handler de la tarea. Para la asignación de memoria esta se indica en cantidad de "palabras". En el caso de esta tarea, la memoria asignada es de 128 palabras de 16 bits, según se puede ver en la figura 5, donde se observa la definición de la macro *configMINIMAL_STACK_SIZE* que se le pasa a la tarea 1.

La función **xTaskCreate** se encuentra definida en el archivo *task.h* y se lo hace de la forma:

`#define xTaskCreate( pvTaskCode, pcName, usStackDepth, pvParameters, uxPriority, pxCreatedTask ) xTaskGenericCreate( ( pvTaskCode ), ( pcName ), ( usStackDepth ), ( pvParameters ), ( uxPriority ), ( pxCreatedTask ), ( NULL ), ( NULL ) )`

Es decir que es otra forma de invocar a la función **xTaskGenericCreate**. Esta se encuentra definida en el archivo *task.c*.

Al invocar a xTaskCreate, esta recibe como paráemtros una variable del tipo *TaskFunction_t* la cual se encuentra definida en el archivo *projdefs.h* y corresponde a otra forma de llamar a un puntero a void. Esta variable es el puntero a la función que ejecutará la tarea correspondiente. La variable de asignación de recursos es del tipo *UBaseType_t*. Esta se encuentra definida en el archivo *portmacro.h* y es otra forma de llamar a un **unsigned long**. Es decir que el tamaño de stack asignado a las tareas depende del tipo de arquitectura, es decir, qué considere la misma como unsigned long.

Para el caso de la prioridad asignada, se observa que a la tarea 1 se le asigna la prioridad *tskIDLE_PRIORITY + 1UL*. La tarea **IDLE** corresponde a la tarea de menor prioridad. Entonces se le está asignando a la tarea 1 una prioridad igual a la de la tarea IDLE + 1.

En la figura 6 también se muestra la creación de la tarea 2. Se puede ver que a esta se le asigna la misma prioridad que a la tarea 1. Como la macro *configUSE_PREEMPTION* vale 1, luego el sistema se configura en modo preemptive para tareas de diferentes prioridad y round-robin para tareas de igual prioridad. Es decir que las tareas 1 y 2 se van a ejecutar una seguida de la otra con un tiempo preestablecido e igual para ambas.

Para finalizar con el programa del ejemplo 1, se invoca a la función **vTaskStartScheduler**. La misma crea la tarea IDLE además de inicializar la ejecución de las tareas, estableciendo el orden de ejecución de las mismas. El resto del programa se puede ver en la figura 7.


<p align="center">
  <img src="img_pto_1/task_scheduler.png"/>
</p>

<p align="center">
  Figura 7: Resto del programa utilizado en el ejemplo 1 de FreeRTOS.
</p>

El **while(1)** solo se crea porque el programa va a estar gestionando e invocando a las diferentes tareas, es decir, no se realiza nada dentro del main.

# 2.b)
## Documentación del ejemplo 9

El ejemplo 9 plantea un codigo con el objeto de borrar un task en tiempo de ejecucion.
El codigo comienza definiendo tres punteros a char de entorno global (o tipo string) con los mensajes que posteriormente seran enviados por UART para ser vistos por el usuario.
Uno imprime el titulo del codigo por unica vez y los otros cuando se llama al task correspondiente.
Luego se definen los prototipos de los task utilizados en el codigo.
Por ultimo se genera la variable global xTask2Handle. Dicha variable en el fondo es un puntero a void. Dicha variable se explicara cuando se utilize.
Se observa el codigo en la figura X

<p align="center">
  <img src="img_pto_2/ex_9/1.PNG"/>
</p>
<p align="center">
  Figura X: Variable globales y definicion iniciales
</p>

A continuacion se explicaran los task del codigo.

La funcion vTask1 representa uno de los task (tareas) del codigo.
Cuando la misma es llamada define un puntero a char que lo carga con el parametro de entrada pvParameters
Basicamente como el prototipo de la funcion esta definido como un puntero a void este se debe castear explicitamente dentro de la funcion.
Luego el task entra en un loop infinito dado por el while
Dentro del while prendera un led, avisara por UART que tipo de task es y lo mas importante, creara un task nuevo.
El task nuevo tendra prioridad 2 y ademas se le pasara como parametro la direccion de la variable global definida anteriomente xTask2Handle.
Esta variable es un 'handler' (en castellano un manipulador) del task creado, basicamene es un puntero al task2.
Luego el task1 termina bloqueandose por 100ms.


<p align="center">
  <img src="img_pto_2/ex_9/2.PNG"/>
</p>
<p align="center">
  Figura X: Task 1
</p>


Por ende cuando se crea el task2 este se creara con prioridad mas alta y encima task1 estara bloqueado por tiempo. 
Este conjunto de eventos hara que se ejecute task2 completamente antes de volver a ejecutar nuevamente el task1.

La funcion creada vTask2 comienza similar a vTask1. Primero carga el parametro con el string y apaga el led que prendio vTask1.
Luego saca por UART el nombre del task y un mensaje que se esta por borrar ella misma.
Para borrarse utiliza vTaskDelete(xTask2Handle). A dicha funcion se le debe pasar como argumento un puntero al Task (el tipo de dato definido como 
 TaskHandle) para que se borre del scheduler.

<p align="center">
  <img src="img_pto_2/ex_9/3.PNG"/>
</p>
<p align="center">
  Figura X: Task 2
</p>

Por ultimo el main simplemente inicializa el hardware, imprime por UART el mensaje que esta en el main.
Luego solo crea el vTask1 con prioridad menor y sin ningun handler para luego inicializar el Scheduler y quedarse en un loop infinito
esperando ser cortado por los task.

<p align="center">
  <img src="img_pto_2/ex_9/4.PNG"/>
</p>
<p align="center">
  Figura X: Main ejemplo 9
</p>