# Documentación del ejemplo 1
## b)

Dentro del archivo se encuentran 9 ejemplos de FreeRTOS. Si bien cada uno cumple una función diferente, todos comparten ciertas características propias del uso del sistema operativo.

![alt text](img_pto_1/setup_hardware.png)

A continuación se declaran las funciones que, durante la ejecución del programa, representan las tareas que ejecutará el sistema operativo.

![alt text](img_pto_1/declaracion_de_funciones.png)

La macro mainDELAY_LOOP_COUNT se utiliza posteriormente dentro del programa principal. Lo siguiente es definir estas tareas. 

![alt text](img_pto_1/task_1.png)

En la figura 3, la tarea cumple con el prototipo que debe tener es decir, retornar void y recibir un puntero a void. La tarea solamente enciende un LED y envía el mensaje de la variable “pcTaskName”: “Task 1 is running\r\n”. Por último realiza un delay con un ciclo for. Es un delay de la tarea, es decir, no es equivalente a invocar a la función vTaskDelay, que sí cede el control de CPU, hasta que expire ese timer. Entonces el rtos seguirá atendiendo a la tarea. En la figura 4 se ve la tarea 2.

![alt text](img_pto_1/task_2.png)


La tarea 2 es idéntica a la tarea 1, solamente que apaga el LED y envía el mensaje “Task 2 is running\r\n”. 

Antes de continuar con el ejemplo, se analiza el archivo de configuración del FreeRTOS, “FreeRTOSConfig.h” para saber en qué modo está funcionando el sistema operativo.

![alt text](img_pto_1/config_rtos.png)

El sistema está configurado en modo **preemptive**, se definen **8** prioridades diferentes (tener en cuenta que esto corresponde a todos los ejemplos, no solo al 1), el tick interrupe al sistema cada **1000 ticks**.

Continuando con el main del ejemplo, lo primero que se hace es invocar a la función de la imagen 1, que configura la placa para su uso. Luego se imprime el mensaje *\r\nExample 1 - Creating tasks\r\n*. A continuación, en la imagen 6, se crea la tarea 1 invocando a la función **xTaskCreate**.

![alt text](img_pto_1/task_create.png)





















