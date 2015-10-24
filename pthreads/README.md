
# PTHREADS

En este repositorio encontrará los codigos de los ejercicios sobre Hilos.

## Contenido de este repositorio

Este repositorio contiene los codigos del taller sobre hilos propuesto en la materia sistemas distribuidos. A continuación se procederá a responder las preguntas acerca de cada uno de ellos.

3s-00.c

3s-01.c

3s-02.c

3s-03.c

3s-05.c



## Introducción Una suave - Asignación
Retire las instrucciones relacionadas con pthread_join y explicar:
¿Cuál es el comportamiento del programa?

Para contestar esta pregunta, primero debemos conocer qué función cumple pthread_join, Esta función, de modo similar a como ocurre con la llamada al sistema wait, detiene el hilo que la invoca hasta que un hilo determinado termina y toma el valor retornado este.

Dentro de nuestro código esta función se expresa de la siguiente manera:

if (pthread_join(inc_x_thread, NULL)) {
fprintf(stderr, "Error joining thread\n");
return 2;
}
En el código se quiere decir, que se espera al hilo inc_x_thread hasta que termine su ejecución. Si eliminamos esta condición damos por hecho que no deseamos esperar el cálculo de :

pthread_create(&inc_x_thread, NULL, inc_x, &x)

Y continuamos en la secuencia. Lo que al final nos arroja:

x: 0, y: 0
y increment finished
x: 0, y: 100
x increment finished

Si es inesperado, ¿qué te parece eso?

El resultado de este algoritmo no es inesperado, ya que contamos con que no vamos a esperar el resultado que arroje el método inc_x, el cual da el fin a la ejecución del método inc_x_thread.

Pruebe el programa varias veces.
Todas las ejecuciones eran iguales?

Se probó el algoritmo y todas las veces que este fue ejecutado, arrojaba el mismo resultado. En conclusión, el resultado no es variante ya que el cálculo de :

void *inc_x(void *x_void_ptr) {
int *x_ptr = (int *) x_void_ptr;
while (++(*x_ptr) < 100);
printf("x increment finished\n");
return NULL;
}

Es posterior a la finalización del programa principal. main.

# 3s-00.c - Asignación
Añadir las sentencias de código adecuadas que le permiten determinar la cantidad de tiempo que el programa pasa durante:
Vector de inicialización.
Contar el número de 3s en un vector dado.

Running 3s-00*** 3s-00 ***
Initializing vector... Vector initialized!
Count by threads 50006321
Double check 50006321
Time: Vector de Inicialización 15418.857422
Time: Contar El Número de 3s en vector un dado 12200.593750

Para obtener este resultado se creó una variable clock_t t0, y esta se inicializa al mismo tiempo que las demás (t1 y t2), el tiempo que recorre el algoritmo se mide de la siguiente manera:

t0 = clock();
printf("Running 3s-00");
printf("*** 3s-00 ***\n");
printf("Initializing vector... ");
fflush(stdout);
initialize_vector();
printf("Vector initialized!\n");
fflush(stdout);
t1 = clock();
count_3s();
t2 = clock();

Los tiempo de t0 van desde el tiempo inicial en cero (0), t1 hasta cuando termina el método initialize_vector(); y luego finalizamos con el tiempo en t2 al finalizar el método count_3s();

# 3s-01.c - Asignación
Incluya las frases de código utilizadas para estimar la cantidad de tiempo necesario para cada hilo para contar el número de 3s en una matriz dada.
Responde las siguientes preguntas
¿Cuántos elementos del vector grande tiene?

El vector posee el tamaño de: VECTOR_SIZE 1000000000 (1.000.000.000 Mil Millones)

El programa es correcto? ¿Qué hay de malo en ello? ¿Qué valor se pone en el programa y es el valor esperado?

Primero, al descargar el programa y ejecutar se generan dos Warning, primero es el RETURN del contador de 3s en el vector y el segundo es el valor que se le asigna al método *count3s_thread(void *arg), al momento de crear los hilos.

La funcion del programa es que el tome los 8 hilos designados, MAX_THREADS 8, cada hilo debe tomar una porcion a leer del vector, 1.000.000.000 Mil Millones / 8 hilos.

Por lo tanto cada hilo debe leer aproximadamente 125.000.000 posiciones y pero los errores que se observan se debe a que el paso de mensajes no es sincronizado. El primer error se observa en el momento que se crea el hilo y el mensaje de lectura no es posterior a la creación. 

Otro error es que no se contabilizan los hallazgos de 3s en los vectores, ya que la variable count++ se reinicia en cada hilo, y solo trae la final. Estos dos valores deben de coincidir.

[3s-01] Count by threads 66020 //Cuenta a traves del metodo count3s_thread()
[3s-01] Double check 50010498 //Cuenta a través del método initialize_vector(), éste es fiable ya que se contabiliza al momento de la creación.

*** 3s-01 ***
Initializing vector... Vector initialized!
[3s-01] Thread created!
[3s-01] Thread created!
	Thread [0] starts [0] length [125000000]
	Thread [1] starts [125000000] length [125000000]
[3s-01] Thread created!
	Thread [2] starts [250000000] length [125000000]
	Thread [3] starts [375000000] length [125000000]
[3s-01] Thread created!
	Thread [4] starts [500000000] length [125000000]
[3s-01] Thread created!
	Thread [5] starts [625000000] length [125000000]
[3s-01] Thread created!
[3s-01] Thread created!
	Thread [6] starts [750000000] length [125000000]
	Thread [7] starts [875000000] length [125000000]
[3s-01] Thread created!
[3s-01] Count by threads 66020
[3s-01] Double check 50010498
[[3s-01] Elapsed time 25.452999

# 3s-02.c - Asignación
Incluya las frases de código utilizadas para estimar la cantidad de tiempo necesario para cada hilo para contar el número de 3s en una matriz dada.

Antes de inicializar la distribución a cada uno de los hilos, se toma el tiempo t1

t1 = clock();
while (i < max_threads) {
...
}
t2 = clock();

Luego de terminar la asignación a los hilos, se toma el tiempo t2. de esta manera se consulta el tiempo total, para consultar el tiempo que gasta cada hilo, creamos un arreglo de clock’s.

clock_t clocks[MAX_THREADS];

Cada reloj, toma el tiempo al finalizar la ejecución satisfactoria de cada hilo. clocks[i] = clock();

¿Qué está mal con este código esta vez?

El error del paso de mensajes continua, pero la suma del total de hallazgos mejora, ya que se utiliza el pthread_join() para determinar que cada hilo finalice correctamente la lectura e interactúe con el acumulador count.

*** 3s-02 ***
Initializing vector... Vector initialized!
[3s-02] Thread created!
[3s-02] Thread created!
[3s-02] Thread created!
[3s-02] Thread created!
[3s-02] Thread created!
[3s-02] Thread created!
[3s-02] Thread created!
[3s-02] Thread created!
	Thread [2] starts [250000000] length [125000000]
	Thread [3] starts [375000000] length [125000000]
	Thread [4] starts [500000000] length [125000000]
	Thread [5] starts [625000000] length [125000000]
	Thread [6] starts [750000000] length [125000000]
	Thread [7] starts [875000000] length [125000000]
	Thread [0] starts [0] length [125000000]
	Thread [1] starts [125000000] length [125000000]
Thread [0] exited with status [8427577344], time [15605.951172]
Thread [1] exited with status [8428113920], time [15606.548828]
Thread [2] exited with status [8428650496], time [15606.617188]
Thread [3] exited with status [8429187072], time [15606.665039]
Thread [4] exited with status [8429723648], time [15606.703125]
Thread [5] exited with status [8430260224], time [15606.747070]
Thread [6] exited with status [8430796800], time [15606.779297]
Thread [7] exited with status [8431333376], time [15606.806641]
[3s-02] Count by threads 
.
44911086
[3s-02] Double check 50009083
[[3s-02] Elapsed time 15606.810547

# 3s-03.c - Asignación
Identificar por qué este programa está haciendo bien.

Este programa funciona correctamente, lo que permite realizar este cálculo de manera correcta es la inclusión del semáforo pthread_mutex. Este bloque el hilo y permite que calcule lo necesario hasta que termine, de esta manera la concurrencia es simulada y además es fiable.

Incluya las frases de código utilizadas para estimar la cantidad de tiempo necesario para cada hilo para contar el número de 3s en un segmento del array.
¿Cuánto tiempo llevó a obtener el recuento total de 3s en toda el array.

El tiempo total hasta la ejecución del último hilo es de [11331.391602], tiempo reducido a un 33% con respecto al cálculo anterior.

*** 3s-03 ***
Initializing vector... Vector initialized!
	Thread [0] starts [0] length [125000000]
[3s-03] Thread created!
	Thread [1] starts [125000000] length [125000000]
[3s-03] Thread created!
	Thread [2] starts [250000000] length [125000000]
[3s-03] Thread created!
	Thread [3] starts [375000000] length [125000000]
[3s-03] Thread created!
	Thread [4] starts [500000000] length [125000000]
[3s-03] Thread created!
	Thread [5] starts [625000000] length [125000000]
[3s-03] Thread created!
[3s-03] Thread created!
	Thread [6] starts [750000000] length [125000000]
	Thread [7] starts [875000000] length [125000000]
[3s-03] Thread created!
Thread [0] exited with status [8532172800], time [1943.567017]
Thread [1] exited with status [8532709376], time [3663.635010]
Thread [2] exited with status [8533245952], time [4968.418945]
Thread [3] exited with status [8533782528], time [6347.942871]
Thread [4] exited with status [8534319104], time [7617.126953]
Thread [5] exited with status [8534855680], time [8864.399414]
Thread [6] exited with status [8535392256], time [10094.634766]
Thread [7] exited with status [8535928832], time [11331.391602]
[3s-03] Count by threads 49993532
[3s-03] Double check 49993532
[[3s-03] Elapsed time 11331.415039

# 3s-04.c - Asignación
¿Cuál es la diferencia entre 3s-03.c y 3s-04.c?

Se diferencian en el que 3s-03.c se bloquea y desbloquee el hilo al finalizar el for, el for lee los hallazgos de los 3s en el array.

En cambio el 3s-04.c cada vez que encuentra un hallazgo bloquea y desbloque el hilo.

Comparar el tiempo transcurrido por hilo durante el proceso de conteo y el tiempo total que todas las discusiones tomaron para contar el número de 3s en toda la matriz. Ejecutar todos los programas (3s-03 y 3s-04) tres veces y calcular el tiempo promedio por programa. Presente sus resultados y explicarlos.

A simple vista y sin necesidad de graficar, el programa 3s-04 consume muchos más recursos, ya que su ejecución de las sentencias pthread_mutex_lock() y pthread_mutex_unlock(), n veces en el que se encuentra el 3 dentro del arreglo.

Programa
3s-03
Programa
3s-04
Thread [0]


1943.567017
1754.125478
1244.963989
252185.81250
249541.12312
247740.32812
Thread [1]


3663.635010
2541.896521
2495.892090
252186.40625
250120.51234
247812.81250
Thread [2]


4968.418945
3984.263211
3750.946045
252186.45312
250120.51365
247812.84375
Thread [3]


6347.942871
5984.541210
4968.658203
252186.46875
250120.51452
247812.85937
Thread [4]


7617.126953
7361.811211
6314.037598
252186.50000
250120.51561
247812.87500
Thread [5]


8864.399414
8101.231453
7560.609863
252186.51562
250120.51654
247812.90625
Thread [6]


10094.634766
9251.326621
8831.837891
252186.53125
250120.51789
247812.92187
Thread [7]


11331.391602
10723.345545
10104.056641
252186.53125
250120.51954
247812.93750

# 3s-05.c - Asignación
¿Dónde reside el éxito de este programa?

Esta ejecución es perfecta, utiliza una variable local dentro de cada hilo para hacer el cálculo de los hallazgos de las 3s. pero lleva un acumulador global que si es protegido en el acceso a través del semaforo pthread_mutex.

pthread_mutex_lock(&mutex);
count = count + private_count[id];
pthread_mutex_unlock(&mutex);

Compare esto ejecución del programa y comparar su rendimiento con casos anteriores y escribir sus observaciones.

El rendimiento también se observa en la reducción de los hilos para realizar la operación, esta vez se utilizaron 4 hilos, MAX_THREADS 4, estos redujo el tiempo de procesamiento y aumento la disponibilidad de procesamiento.

Igualmente el resultado fue perfecto en este caso, ya que se utiliza un acumulador global, y se evita, como en los programas anteriores, tener una variable única global, para acumularse en cada for, los cuales se ejecutaban dentro de los 8 hilos disponibles.

*** 3s-05 ***
Initializing vector... Vector initialized!
[3s-05] Thread created!
	Thread [0] starts [0] length [250000000]
[3s-05] Thread created!
	Thread [1] starts [250000000] length [250000000]
[3s-05] Thread created!
	Thread [2] starts [500000000] length [250000000]
[3s-05] Thread created!
	Thread [3] starts [750000000] length [250000000]
Thread [0] exited with status [8348925952]
Thread [1] exited with status [8349462528]
Thread [2] exited with status [8349999104]
Thread [3] exited with status [8350535680]
[3s-05] Count by threads 50000849
[3s-05] Double check 50000849
[[3s-05] Elapsed time 14617.175781
