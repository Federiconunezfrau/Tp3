# Documentación del ejemplo 1
## b)

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

'#define xTaskCreate( pvTaskCode, pcName, usStackDepth, pvParameters, uxPriority, pxCreatedTask ) xTaskGenericCreate( ( pvTaskCode ), ( pcName ), ( usStackDepth ), ( pvParameters ), ( uxPriority ), ( pxCreatedTask ), ( NULL ), ( NULL ) )'

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