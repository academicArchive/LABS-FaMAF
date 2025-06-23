# Analisis del trafico de red bajo diferentes estrategias de enrutamiento
## Informe Lab4 2025 - Redes y Sistemas Distribuidos.

## RESUMEN 
En este proyecto nos enfocamos en el analisis y dearrollo de estrategias de enrutamiento dentro de la capa de red. La red analisada y desarrollada tiene topologia [anillo](https://es.wikipedia.org/wiki/Red_en_anillo). <br>
Para realizar este trabajo utilizamos Omnet++, una bibloteca con un marco de simulación en C++ destinado principalmente a la creación de simuladores de red, nosotros lo utilizamos para poder generar estos modelos de red que posteriormente analizamos. <br><br>
En este lab desarrollamos un modelo de enrutamiento a partir de uno ya constituido, analizamos ambos modelos y determinamos mediante valores resultados y graficas que nuestro modelo desarrollado es mas eficiente a la hora de ejecutar la simulacion que el entregado inicialmente.<br>
A continuacion puede verse todo el analisis.

## Integrantes
  - Guerrero Diego
  - Gonzalez Juan Pablo
  - Madero Ismael
  - Pellegrino Milena

## Índice

1. [INTRODUCCIÓN](#introducción)
   - [Problema](#problema)
   - [Esquema de nuestra red](#el-esquema-de-nuestra-red)
   - [Simulación discreta](#simulación-discreta)

2. [Análisis de red inicial](#análisis-de-red-inicial)
   - [Caso 1](#caso-1)
     - [¿Qué métricas se obtienen?](#qué-métricas-se-obtienen)
     - [¿Cómo es el uso de los recursos de la red? ¿Se puede mejorar?](#cómo-es-el-uso-de-los-recursos-de-la-red-se-puede-mejorar)
   - [Caso 2](#caso-2)
     - [Explore y determine a partir de qué valor de interArrivalTime se puede garantizar un equilibrio o estabilidad en la red. Justifique.](#explore-y-determine-a-partir-de-qué-valor-de-interarrivaltime-se-puede-garantizar-un-equilibrio-o-estabilidad-en-la-red-justifique)

3. [Tarea Diseño](#tarea-diseño)
   - [Aclaraciones](#aclaraciones)
   - [Ejemplo del funcionamiento del algoritmo](#ejemplo-del-funcionamiento-del-algoritmo)
   - [Problema a la vista](#problema-a-la-vista)
   - [Solución encontrada](#solución-encontrada)

4. [Análisis del algoritmo diseñado](#análisis-del-algoritmo-diseñado)
   - [CASO 1](#caso-1)
   - [CASO 2](#caso-2)

5. [Resultados](#resultados)

6. [Discusión](#discusión)
   - [Logros](#logros)
   - [Mejoras](#mejoras)

7. [Referencias](#referencias)

## INTRODUCCION 
Se nos proporciono una red funcional para que testeemos y determinemos si dicha red cumple ser **equilibrada**, donde nosotros definimos qué significa que una red sea **equilibrada**.
### Problema
El **enrutamiento**, es la seleccion de rutas para el envio de paquetes dentro de una red, en nuestro caso los caminos posibles son las conexiones entre nodos.<br>
Nosotros debemos analizar y determinar que enrutamiento seria mas apropiado para una red determinada.<br>
Dicho problema ya fue trabajado previamente con distintos algoritmos que se encargan de esta problematica, algunos de ellos son: 
- [Algoritmo de vector de distancia](https://es.wikipedia.org/wiki/Vector_de_distancias)
- [Algoritmo de estado de enlace](https://es.wikipedia.org/wiki/Estado_de_enlace)
- [Algoritmo de Dijkstra](https://es.wikipedia.org/wiki/Algoritmo_de_Dijkstra)
- [Algoritmo de Bellman-Ford](https://es.wikipedia.org/wiki/Algoritmo_de_Bellman-Ford) 
- [Enrutamiento por inundación](https://es.wikipedia.org/wiki/Inundaci%C3%B3n_de_red)

Nosotros decidimos seguir una idea similar al algoritmo de estado de enlace, basandonos en mensajes tipados para compartir informacion entre nodos, ignorando las listas de nodos.
### El esquema de nuestra red
Una red es un conjunto de nodos conectados entre si que facilitan el envio de paquetes.

Nodos: Miembros de la red que crean, envian y reciben paquetes. En su composicion tienen:
- App: Encargada de crear y recibir paquetes.
- Net: Encargada del reenvio de paquetes (Tanto a App como a Links).
- Links: Canal de comunicacion con otros nodos (Tienen cola y todo lo que reciben lo envian a Net).

Nodos de nuestra red:<br>
![Node[0]](img/Node.png)
 
Como dijimos anteriormente es topologia de anillo conformada de la siguiente manera:<br>
![anillo](img/Red.png)<br>


### Simulación discreta
Para los análisis de los experimentos (tanto del caso 1, como del caso 2) realizamos simulaciones discretas, es decir, los eventos ocurren en momentos específicos y el tiempo ocurre en pasos discretos, nosotros utilizamos Omnet++. Hacerlo de esta manera nos permite evaluar el desempeño de una red sin la necesidad de tenerla físicamente.

Se nos entrego una red inicial la cual consta de enrutamiento basado en envio de paquetes sentido agujas del reloj, donde el Node[0] y el Node[2] envian paquetes con destino a Node[5].<br>
Debemos analizar mediante vectores generados durante la simulacion, dicha red.


## Analisis de red inicial.



Empezaremos definiendo que es una red equilibrada, para eso dicha red debe cumplir:

1. No sobrecargar nodos si existe otra posibilidad.
2. Enviar paquetes por caminos cortos.

Dicho esto podremos concluir que una red equilibrada **envia paquetes por el camino más corto con menos congestion**.


### Caso 1 
- Node[0] y Node[2] producen paquetes con destino a Node[5].

Ejemplo de ejecucion:<br>A la izq ejecucion del Node[0], a la derecha ejecucion de red, notar que estos ejemplos no son sincrónicos.<br>Los paquetes rojos son producidos por Node[0] y los azules por Node[2]:<br>
![Case1](img/Sample1.gif) ![Sample](img/Case1Sample.gif)

#### ¿Qué métricas se obtienen? 

**Ejecutando esta red durante 200 segundos podemos ver lo sig:**

Promedio de paquetes en colas de salida:<br>
![Buffers](img/Case1Buffers.png)

Puede apreciarse: 
1. La ausencia de las colas de Node[3], Node[4], y Node[5].
2. Node[0] es el de mayor carga.
3. Node[2] tiene ligeramente mayor carga que los nodos del punto 4.
4. Node[1], Node[6], y Node[7] son los que menor carga tienen.

Razonamiento:
1. Esto se debe a que no se envian paquetes por dichos nodos.
2. Envia tanto los paquetes recibidos desde Node[1] como los generados.
3. Unicamente envia los paquetes que genera.
4. Solo envian paquetes recibidos.

Observacion:
Los paquetes se generan mas rapido de lo que se envian, por eso el Node[2] y Node[0] tienen mayor congestion en sus colas.

Conteo de paquetes que pasaron por modulo net:<br>
![Buffers2](img/Case1Buffers2.png)

Puede verse que el Node[2] con único rol generador produjo aproximadamente un total de 189 paquetes, se estima una cantidad de produccion similar en el Node[0] por lo que se observa logico que la carga sea aproximadamente mayor al doble que del Node[2] puesto que ademas de producir, recibe los producidos por Node[2].<br>
Ademas puede verse la cantidad de paquetes que recibio el Node[5], una cantidad ligeramente mayor a lo producido por los generadores.<br>
En los demas nodos se ve un valor similar de manejo de paquetes pues estos solo reenvian.

Valores de los delays segun llegan:<br>
![Delays](img/Case1Delays.png)

Se aprecia que los delays aumentan a medida que ocurre la ejecucion, esto ocurre pues hay sobrecongestionamiento producido en el Nodo[0], lo que genera cada vez mayores delays.

Cantidad de saltos:<br>
![Hops](img/Case1Hops.png)

En esta grafica se ven que hay dos tipos de paquetes, los que llegan con 3 hops y los que llegan con 5. Producidos por Node[2] y Node[0] respectivamente. Se observan dominantes los paquetes de 3 Hops, esto indica que llegan aproximadamente un 17% mas paquetes producidos por Node[0]

#### ¿Cómo es el uso de los recursos de la red? ¿Se puede mejorar?

El uso de los recursos es sobreexigido, pues las colas no dan abasto a la produccion. Se puede mejorar modificando el algoritmo de envio de paquetes.

### Caso 2
- Todos los nodos envian a Node[5]

Ejemplo de ejecucion:<br>
![Case2](img/Case2Sample.gif)

Luego de 200 segs de ejecucion se recolectaron los sig. datos:

Promedio de paquetes en colas de salida:<br>
![Buffers](img/Case2Buffers.png)

Conteo de paquetes que pasaron por modulo net:<br>
![Buffers2](img/Case2Buffers2.png)


Valores de los delays segun llegan:<br>
![Delays](img/Case2Delays.png)

Cantidad de saltos:<br>
![Hops](img/Case2Hops.png)

Se nota bastante similar al Caso 1 exceptuando los delays que son bastante mayores en este ultimo caso.<br>
Ademas puede observarse que los Nodos mas lejanos en sentido contrario a las agujas del reloj son quienes menos paquetes efectivos envian.

#### Explore y determine a partir de qué valor de interArrivalTime se puede garantizar un equilibrio o estabilidad en la red. Justifique.

Testeando varios valores para el interArrival el primero que vi que garantiza un cierto equilibro es **exponential(8)**, pues las graficas para este caso son las siguientes:<br>
![exp(8)Buffers](img/Exponential(8)Buffers.png)<br>
![exp(8)Buffers2](img/Exponential(8)Buffers2.png)<br>
![exp(8)Delays](img/Exponential(8)Delays.png)<br>
![exp(8)Hops](img/Exponential(8)Hops.png)<br>
Considero este valor como equilibrado pues no hay sobrecarga en la mayoria de colas a excepcion del Node[6] de todos modos la cantidad de paquetes que recibio el Node[5] es muy similar a la cantidad de paquetes que pasaron por el Node[6].<br>
Ademas los delays se mantienen en los mismos valores aproximadamente, y no son crecientes.<br>
Se recibe una cantidad similar de paquetes de todos los nodos.<br>
Aunque la cantidad recibida por Node[5] ronda al rededor de 150 cuando en el caso de exponential(0.1) era de alrededor de 190, lo que marca una contra en favor de no tener una red saturada y recibir paquetes de todos los nodos.

## Tarea Diseño


Se nos solicitó diseñar una estrategia de enrutamiento, cabe aclarar que por el tiempo del laboratorio no elegimos un algoritmo muy complejo como por ejemplo el protocolo de vector de distancia o el enrutamiento de estado de enlace vistos en el teórico, nuestra idea del algoritmo fue la siguiente:
- En el primer momento donde no se tiene un paquete de feedback, y todavía no sabemos cual es la mejor ruta, lo que hacemos es enviar los paquetes en el sentido de la aguja del reloj (esto lo hacemos para no tener paquetes esperando en el buffer)
- Los nodos generadores comienzan con un paquete de “testeo" (Todos de manera sincrónica).
- El paquete de testeo es enviado a ambos vecinos del nodo (esto es así, porque sabemos de antemano que es una red con topología anillo, es nuestro único supuesto y lo único que sabemos con respecto a la red).
- El receptor envía un paquete de feedback al nodo generador indicando cuál fue la mejor ruta.
- El paquete feedback se envía al nodo generador por la ruta óptima (Esto es clave).
- A partir de saber por donde llega el feedback, el nodo generador comienza con el envío de los datos por la ruta óptima.
- Envía “x” cantidad de paquetes por esa ruta.
- Después de los "x" paquetes vuelve a enviar un paquete de testeo para determinar si esa ruta sigue siendo la óptima (ya que puede cambiar debido al congestionamiento).
- El "x" que nosotros elegimos (sin mucho criterio), es 30 (RESET_TESTING)(Se setea como constante en [Net.cc](Net.cc)).

### Aclaraciones:
- El tamaño tanto de los paquetes *test* como *feedBack* es la mitad que los paquetes generados por la capa de **App**.
- Los paquetes *test* y *feedBack* se generan en **Net** layer.

### EJEMPLO DEL FUNCIONAMIENTO DEL ALGORITMO:

**Características:**
- Nodos generadores: node[0] y node[2]
- Nodo receptor: node[5]

**PASO 1:**
Los nodos generadores, comienzan enviando los paquetes en sentido de las agujas del reloj. Mientras tanto, node[0] y node[2] envían ese paquete “testeo” para ver cuál es la ruta más corta/menos congestionada con respecto al receptor. <br>
![PASO_1](img/PASOS/PASO_1.png)

**PASO 2:**
El node[5] envía el paquete FeedBack a cada uno de los nodos generadores utilizando la ruta con menos delay. <br>
![PASO_1](img/PASOS/PASO_2.png)

**PASO 3:**
Cuando llega el paquete FeedBack indicando la ruta correspondiente, node[0] y node[2] comienzan a enviar los datos generados por medio de la “ruta óptima”. La idea es llevar un contador con los paquetes enviados.<br>
![PASO_1](img/PASOS/PASO_3.png)

**PASO 4:**
Una vez que se enviaron X cantidad de paquetes, se vuelve a enviar otro paquete de testeo desde los nodos generados por ambas rutas, para la reevaluación (no se deja de enviar paquetes de datos). Ya que la ruta más corta puede no ser la mejor opción, sino que también importa la congestión. <br>
![PASO_1](img/PASOS/PASO_4.png)

**PASO 5:**
Go to PASO 2.

### Problema a la vista:
Nuestro algoritmo de enrutamiento tiene una maravillosa caracteristica, es **dinámico**, esto quiere decir que cada cierto tiempo se vuelve a ejecutar la evaluación de ruta óptima. Si bien esto a priori parece buenisimo, detrás tiene una debilidad a tener en cuenta si usamos una simulación para probarlo: <br>

Supongamos que tenemos 2 nodos cualquiera generando paquetes y enviandolos ambos en el mismo sentido (horario o anti-horario) (ambos comienzan la simulación con 0 paquetes enviados). La descripción de nuestro algoritmo actualmente esconde cierta **"sincronización"** en la reevaluación de rutas de los nodos generadores, esto significa que al momento que estos 2 nodos reevaluen la mejor ruta es **altamente probable** que ambos elijan el sentido contrario puesto que aunque quizá sea el camino más largo, es el camino con menor congestión. Si ambos nodos cambian el sentido de envio de paquetes luego de la reevaluación esto volvería a generar congestión en el sentido elegido. <br>
En conclusión tenemos que si bien, el hecho de que el algoritmo sea dinámico es algo bueno. Nos encontramos con una ***sincronización involuntaria*** de reevaluación entre nodos es algo contraproducente.

### Solución encontrada:
La solución de este problema que puede manchar la eficiencia de nuestro algoritmo de enrutamiento no es trivial. Se trata de **forzar una desincronización de evaluación entre nodos**, esto no fue algo tan fácil de que se nos ocurra pero se logra de la siguiente manera:

- Todos los nodos generadores mandan un paquete test apenas arranca la simulación.
- Sin embargo cada nodo se inicializa con una cantidad distinta de paquetes enviados.
- Cantidad de paquetes enviados = (((indice del nodo) % 10) / 10) * RESET_TESTING.
- Entonces por ejemplo, los nodos con indice 1, 11, 21, 31, van a comenzar con un 10% de los paquetes necesarios para la reevaluación.
- Esto genera que todos los nodos hagan una evaluación sincronizada (la primera) pero luego se desincronizan para no tener el problema anteriormente mencionado.


## Análisis del algoritmo diseñado:

### CASO 1:
Adjunto algunas imágenes que pueden resultar relevantes para llegar a conclusines.

![IMAGEN](img/CASO%201/queue_charge.png)
Podemos notar que el nodo 5 sigue siendo evidentemente el nodo con más carga lo cuál nos parece razonable puesto que es el destino de los nodos generadores. <br>
![IMAGEN](img/CASO%201/jumps.png)
Este gráfico nos muestra y evidencia que la mayoría de las veces, la ruta más óptima es la más corta (sin embaro no siempre es así). <br>
![IMAGEN](img/CASO%201/delay.png)
El algoritmo que implementamos hizo reducir significantemente el delay.
![IMAGEN](img/CASO%201/delays_test.png)
En este gráfico podemos ver que poco tiempo después de la reevaluación de ruta óptima, el delay de los paquetes tiene una notable bajada, lo cuál indica que nuestro algoritmo tiene sentido y está bien implementado. Luego el delay comienza a aumentar lo cuál es lógico debido a la congestión.

### CASO 2:
Adjunto algunas imágenes que pueden resultar relevantes para llegar a conclusiones.
![IMAGEN](img/CASO%202/queue_charge.png)
Este gráfico refleja equilibrio. Los nodos tienen una carga relativamente equitativa.
![IMAGEN](img/CASO%202/jumps.png)
Este gráfico refleja que muchas veces, el camino más corto es el más rápido también. Llegaron muchos paquetes de un solo salto, es decir, de los nodos vecinos al receptor.
![IMAGEN](img/CASO%202/delay.png)
En este caso, no vemos tanta diferencia entre los valores de delay entre el algoritmo original y el algoritmo que diseñamos.
![IMAGEN](img/CASO%202/delays_test.png)
Acá podemos ver los eventos de test junto con el delay, lo que nos interesa obvservar es que los test no se ejecutan todos simultaneamente sino que están repartidos, lo cuál es importante.

## Resultados:
Luego de analizar ambas redes, podemos concluir una mejora en el rendimiento general de la red con el algoritmo de enrutamiento que diseñamos, ya que; **como vimos en el analisis de la red inicial:**<br>
Viendo ambos casos podemos concluir que no es una red equilibrada, pues sobrecarga mucho las colas y los nodos no envian paquetes por los caminos mas cortos, lo que contradice nuestra definicion de red equilibrada.<br>
Ademas para equilibrarla se retrasa mucho la produccion de paquetes como vimos en el caso de interarrivaltime modificado.<br>
Puede mejorarse este algoritmo de enrutamiento si se opta por buscar rutas mas cortas.<br>
Por ejemplo, en el caso 1, los paquetes producidos por el Node[2] podrian ir por el Node[3], Node[4], Node[5].<br>
De esta manera se aprovecha un camino no utilizado previamente que se encuentra libre y ademas las cantidad de Hops seria menor, pasando de 5 a 3.<br><br>
**Ahora, como vimos en el analisis del algoritmo diseñado:**<br>
En el caso 1:<br>
- Una carga en las colas similar en todos los nodos. 
- Una cantidad mayor de paquetes que llegaron con menos saltos esto indica que se suele elegir la ruta mas corta, a excepcion de los momentos en que se detecta congestion.  
- Un delay que si bien aumenta es controlado periodicamente por los paquetes de feedback.<br>

En el caso 2:<br>
- Una carga en las colas similar en todos los nodos, con mayor frecuencia en los que estan en distancia media al nodo destino.
- Se ve una relacion entre menor cantidad de saltos y cantidad de paquetes efectivos, es decir, se reciben mas paquetes de los que tienen menos saltos que de aquellos que se encuentran mas distantes del nodo destino.
- En este caso el delay es ligeramente mayor al de la red inicial, esto se debe a que no solo todos los nodos estan generando paquetes, sino que ademas por el sistema de reenrutamiento se elige cambiar de camino para evitar congestion, lo que genera mas delay.<br>

Por lo tanto concluimos que logramos desarrollar un algoritmo de enrutamiento para una red con topologia anillo, con el objetivo de mejorar el rendimiento general de la red, aunque no es [perfecto](#discusion) y hay varios aspectos que se pueden mejorar, en resumen, funciona mejor en redes menos congestionadas que en el de red inicial dado.

## Discusion:

**Logros:**
- Conseguimos entender y dominar el framework OMNET++, al menos con lo que respecta a este lab.
- Mejoramos el algoritmo y nos mantuvimos fieles a la decision de una red equilibrada.
- Nos familiarizamos con el uso de collab para generar graficos y hacer analisis. Junto con esto pudimos determinar estadisticas apropiadas para analisis de red.

**Mejoras:**
- Dado que diseñamos el algoritmo pensando en una topologia de anillo, este mismo no es aplicable para redes con topologias diferentes.
- Podria considerarse que los paquetes feedback y test contengan mas informacion para permitir a cada nodo conocer todos sus vecinos y elegir la ruta mas optima.
- En algunos momentos como en el caso 2 el diseño con feedback empeora el funcionamiento de red, por lo tanto podria buscarse evitar enviar estos feedback innecesarios.
- En redes supercongestionadas los paquetes test tardan mucho en llegar y provocan congestion, por lo que se podria implementar un TTL en el paquete para ser eliminado cuando pase un determinado tiempo. 
- La cantidad de paquetes enviados hasta reenvar un nuevo paquete feedback podria analizarse mejor para ver cual seria la cantidad optica (ademas a medida que se ejecuta la red podriamos implementar que esta cantidad se vaya modificando, es decir que no sea una cantidad fija, esto con el objetivo de no sobresaturar la red con paquetes de feedback y testeo). 
- De igual manera podria reveer el tamaño de estos paquetes, (en nuestra algoritmom actual el tamaño es la mitad de los paquetes de datos, pero podrían ser menores).

## Referencias:
- [Protocolo de vector de distancia](https://famaf.aulavirtual.unc.edu.ar/pluginfile.php/53955/mod_resource/content/5/La%20Capa%20de%20Red%20-%20Enrutamiento%20de%20Estado%20de%20Enlace.pdf)
- [Como graficar en collab](https://colab.research.google.com/drive/1wOr1jP7-s076qyJl7gxgQ63cqiEYJ8g0?usp=sharing)
- [Manual de Omnet++](https://doc.omnetpp.org/omnetpp5/manual/)
- [Graficas en Omnet++ con Python y Notebooks](https://www.youtube.com/watch?v=yL1gf04E2_E)
- [Omnet++](https://omnetpp.org/)
- [Estructura de un informe](https://www.youtube.com/watch?v=yq8zjLZABe0)


Las graficas fueron generadas usando el sig. link de collab: [lab4_analisis](https://colab.research.google.com/drive/1AdbyTvdN3MwR7wmiGdik1NOO2emff0Gs?usp=sharing)

