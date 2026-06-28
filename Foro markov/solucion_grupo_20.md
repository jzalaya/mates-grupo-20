# Foro evaluable: Cadenas de Markov y Tensores
### Solución del Grupo 20

**Asignatura:** 02MIAR, Matemáticas para la IA · Máster Universitario en Inteligencia Artificial (VIU)

**Integrantes:** Ana María Cochiorca Dragomir, Ricardo Germes Serrano, Carlos Jurado Zalaya, Jorge Ramilo Plata

---

## 1. Ideas y conceptos

**1.1. ¿Qué es un proceso estocástico, qué tipos hay y qué aplicaciones tienen?**

Un proceso estocástico es una familia de variables aleatorias $\{X_t\}_{t\in T}$ definidas sobre un mismo espacio de probabilidad y que toman valores en un conjunto común, el espacio de estados. La idea intuitiva es sencilla: en lugar de describir un sistema por un valor fijo, lo describimos por cómo va cambiando bajo incertidumbre a medida que avanza el índice $t$, que casi siempre es el tiempo.

La clasificación habitual atiende a dos ejes. Según el espacio de estados, el proceso es discreto si los valores posibles son contables (por ejemplo, el número de clientes en una cola) o continuo si forman un intervalo (la posición de una partícula). Según el parámetro temporal, hablamos de tiempo discreto cuando $t = 0, 1, 2, \dots$ (la cotización de cierre de una acción día a día) o de tiempo continuo cuando $t \ge 0$ varía sin saltos (el voltaje de una señal con ruido). A esto se añaden propiedades estructurales que dan lugar a familias con nombre propio: los que cumplen la propiedad de Markov, los estacionarios, los de incrementos independientes como el proceso de Poisson o el movimiento browniano, o los martingalas.

Sus aplicaciones son transversales. Aparecen en finanzas para modelar precios y riesgo, en física para difusión y mecánica estadística, en teoría de colas y logística, en telecomunicaciones para tratar señal y ruido, en biología de poblaciones y genética, y en buena parte de la IA actual, desde los modelos de lenguaje hasta el aprendizaje por refuerzo.

**1.2. ¿De qué tipo de proceso estocástico son las cadenas de Markov? Descríbalas brevemente.**

Una cadena de Markov es un proceso estocástico de espacio de estados discreto y, en su versión más común, de tiempo discreto. Su rasgo distintivo es la propiedad de Markov: la probabilidad del próximo estado, conocida toda la historia, depende solo del estado actual y no del camino seguido para llegar a él. En símbolos,

$$P(X_{n+1}=j \mid X_n=i, X_{n-1}, \dots, X_0) = P(X_{n+1}=j \mid X_n=i).$$

Es lo que solemos resumir diciendo que el proceso "no tiene memoria" más allá del presente. Si además las probabilidades de transición no cambian con el paso $n$, la cadena es homogénea y queda completamente determinada por dos ingredientes: una distribución inicial y la matriz de transición que recoge las probabilidades de pasar de un estado a otro en un paso.

**1.3. ¿Qué aplicaciones tienen en IA los Procesos de Decisión de Markov (MDP)?**

Un MDP amplía una cadena de Markov añadiendo decisiones y recompensas. Se describe con la tupla $(S, A, P, R, \gamma)$: en cada estado el agente elige una acción que condiciona la transición y recibe una recompensa, y el objetivo es encontrar una política que maximice la recompensa acumulada esperada, normalmente descontada por el factor $\gamma$. Esa formalización es justo el marco teórico del aprendizaje por refuerzo, de ahí su peso en IA.

Los ejemplos típicos van desde la robótica de control y navegación hasta los agentes que juegan (el caso de AlphaGo o los entornos de Atari), la conducción autónoma, los sistemas de recomendación y de pujas publicitarias, la gestión de diálogos en asistentes o el control de inventarios y precios dinámicos. Para resolverlos o aproximarlos se usan algoritmos como iteración de valor, iteración de política o, cuando el modelo no se conoce, métodos de aprendizaje como Q-learning y los basados en gradiente de política.

**1.4. ¿Qué es una matriz estocástica (o de transición) y qué aplicación tiene en los procesos de Markov?**

Es una matriz cuadrada $P$ cuyas entradas son no negativas y cuyas filas suman exactamente 1. La entrada $p_{ij}$ se interpreta como la probabilidad de pasar del estado $i$ al estado $j$ en un paso, y la condición sobre las filas no es un capricho: desde un estado cualquiera el sistema tiene que ir a parar a algún estado del espacio, incluido el de quedarse donde está, así que esas probabilidades deben repartir toda la masa.

Su utilidad es que concentra toda la dinámica de un paso de la cadena. Las transiciones a varios pasos se obtienen elevando la matriz a una potencia, ya que la entrada $(i,j)$ de $P^k$ es la probabilidad de ir de $i$ a $j$ en $k$ pasos (esto es lo que afirman las ecuaciones de Chapman-Kolmogorov). En la práctica, $P$ es la pieza que hace avanzar el vector de estado mediante $v_{t+1} = v_t\,P$.

> Nota de convenio: aquí trabajamos con la convención por filas (filas que suman 1 y vector de estado por la izquierda, $v_t P$). Existe la convención traspuesta, por columnas, igual de válida; conviene fijar una y ser coherente.

**1.5. ¿Qué es el vector de estado de un proceso de Markov y qué representa?**

El vector de estado $v_t$ es un vector de probabilidad cuya componente $i$-ésima es la probabilidad de que el sistema se encuentre en el estado $i$ en el instante $t$. Por ser una distribución, sus componentes son no negativas y suman 1. Representa, por tanto, nuestro conocimiento del sistema en ese momento, no como una certeza sino como un reparto de probabilidad entre los estados posibles. Su evolución es directa a partir de la matriz de transición: $v_{t+1} = v_t\,P$, y aplicándolo de forma reiterada, $v_n = v_0\,P^{\,n}$.

**1.6. ¿Qué es el estado estacionario de una cadena de Markov finita, cómo se calcula y qué significa?**

El estado estacionario es una distribución de probabilidad $\pi$ que no cambia al aplicarle la matriz de transición, es decir, que cumple $\pi P = \pi$ con $\pi_i \ge 0$ y $\sum_i \pi_i = 1$. Visto de otro modo, $\pi$ es un autovector por la izquierda de $P$ asociado al autovalor 1. En una cadena finita, irreducible y aperiódica (lo que se suele llamar regular) esa distribución es única y además es la distribución límite: $v_n$ tiende a $\pi$ sea cual sea el estado inicial $v_0$.

Para calcularlo se resuelve el sistema lineal que sale de $\pi P = \pi$, esto es $\pi(P - I) = 0$, junto con la normalización $\sum_i \pi_i = 1$. Esa normalización es necesaria, y no sobra, porque las ecuaciones de $\pi(P-I)=0$ son linealmente dependientes (cada fila de $P$ suma 1, lo que hace que el sistema tenga infinitas soluciones proporcionales); la condición de que las componentes sumen 1 selecciona entre ellas la única que es una distribución. Su significado es el comportamiento del sistema a largo plazo: la fracción de tiempo que, en promedio, pasa en cada estado una vez que se olvida de dónde partió.

---

## 2. Modelo del clima: generalidades

Consideramos un modelo con tres estados: (1) soleado, (2) nublado y (3) lluvioso.

**2.1. ¿Pueden las filas de la matriz $Q$ ser filas de una matriz de transición?**

$$Q = \begin{pmatrix} 0.2 & 0.5 & 0.4 \\ 0.1 & 0.5 & 0.3 \\ 0.7 & 0.5 & -0.2 \end{pmatrix}$$

Para que una fila sirva como fila de una matriz de transición tiene que cumplir dos cosas a la vez: que todas sus entradas estén en $[0,1]$ y que sumen 1. Como la pregunta es sobre las filas, conviene revisarlas una a una:

- Fila 1: $0.2 + 0.5 + 0.4 = 1.1$. Se pasa de 1, así que no vale.
- Fila 2: $0.1 + 0.5 + 0.3 = 0.9$. Se queda corta, le falta probabilidad para llegar a 1, así que tampoco vale.
- Fila 3: $0.7 + 0.5 - 0.2 = 1.0$. La suma cuadra, pero contiene la entrada $-0.2$, y una probabilidad no puede ser negativa, de modo que queda descartada igualmente.

Ninguna de las tres filas es admisible, y por tanto $Q$ no puede ser una matriz de transición. Como comprobación adicional, tampoco lo sería bajo el convenio traspuesto: su segunda columna suma $0.5 + 0.5 + 0.5 = 1.5$, con lo que no es estocástica ni por filas ni por columnas.

**2.2. ¿Pueden $u_0$ o $w_0$ ser el vector de estado actual de un proceso así?**

$$u_0 = (0.3,\, 0.6,\, 0.7), \qquad w_0 = (0.2,\, 0.1,\, 0.5)$$

Un vector de estado es una distribución de probabilidad, así que sus componentes deben ser no negativas y sumar 1. En ambos casos las entradas son no negativas, de modo que todo se decide en la suma:

- $u_0$: $0.3 + 0.6 + 0.7 = 1.6 > 1$. Asigna más probabilidad de la que existe, así que no es un vector de estado válido.
- $w_0$: $0.2 + 0.1 + 0.5 = 0.8 < 1$. Deja sin repartir un $0.2$ de probabilidad, por lo que tampoco describe una distribución completa.

En consecuencia, ninguno de los dos puede ser el vector de estado actual del proceso.

---

## 3. Modelo del clima: ejercicio

*[Pendiente: aportación del resto del grupo.]*

---

## 4. Tensores: resumen

**Artículo:** S. Laue, M. Mitterreiter y J. Giesen, *A Simple and Efficient Tensor Calculus for Machine Learning* (AAAI 2020; arXiv:2010.03313).

El artículo trata el cálculo de derivadas de expresiones tensoriales, una tarea central en aprendizaje automático en la que la eficiencia depende de cómo se representen esas expresiones. Ya existía un método para calcular derivadas de orden superior, como Jacobianos y Hessianos, mucho más rápido que el estado del arte, pero estaba escrito en notación de Ricci, que distingue índices superiores e inferiores. El inconveniente es que esa notación no encaja en los frameworks de diferenciación automática habituales (TensorFlow, PyTorch, autograd, JAX), que usan la notación de Einstein, más simple.

Los autores muestran que la notación de Ricci no es necesaria para ser eficiente y proponen un algoritmo, demostrado correcto, que logra la misma eficiencia con notación de Einstein. De este modo, esas mejoras se pueden integrar en las herramientas actuales sin cambiar su representación interna de los tensores.

Describen la diferenciación automática en modo directo (forward) y en modo inverso (reverse), y el modo cross-country, que combina ambos y puede ser más eficiente. El método está implementado en su herramienta MatrixCalculus.org.
