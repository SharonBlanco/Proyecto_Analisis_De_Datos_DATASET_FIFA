## Autores

- **Angie Melissa Aguilar Alemán**
- **Magdaleno Gómez Díaz**
- **Sharon Dayana Blanco Piedra**



#  Análisis de Datos de Jugadores FIFA (2015–2024)

## ¿De qué trata este proyecto?

Este proyecto analiza información de **214 805 registros de jugadores de fútbol** en el videojuego FIFA, abarcando **10 temporadas** (de 2015 a 2024). A partir de estos datos públicos — que incluyen valor de mercado, salario, rendimiento, edad, posición y club de cada jugador — se construyó un sistema que permite responder preguntas de negocio como:

- ¿Cuáles son los clubes con jugadores más valiosos cada temporada?
- ¿Qué ligas y regiones del mundo concentran más valor de mercado?
- ¿Cómo varía el rendimiento de un jugador según su edad y posición?
- ¿Qué clubes son más eficientes financieramente (valor alto vs. salario bajo)?

---

## ¿Por qué es útil?

Aunque los datos provienen de un videojuego, reflejan estimaciones reales del mercado futbolístico. Este tipo de análisis es relevante para:

- **Clubes y directivos deportivos** que buscan identificar talento joven con alto potencial de crecimiento.
- **Analistas financieros del deporte** que evalúan la eficiencia de inversión en plantillas.
- **Ligas y federaciones** que necesitan entender la distribución de valor por región o competencia.

---

## ¿Cómo se organizó el trabajo?

El proyecto se desarrolla en **5 fases**: 

### Fase I — Preparación y limpieza de datos

En esta fase se tomaron los datos crudos (archivos CSV de cada temporada) y se realizó un proceso riguroso para dejarlos listos para el análisis:

1. **Unificación**: se combinaron los archivos individuales de cada temporada (2015 a 2024) en un solo conjunto de datos consistente.
2. **Limpieza**: se eliminaron registros duplicados, se corrigieron nombres de clubes escritos de forma distinta entre años (por ejemplo, "Paris SG" y "Paris Saint-Germain"), y se removieron datos fuera de rangos lógicos.
3. **Imputación de valores faltantes**: cuando un jugador del 2024 tenía datos incompletos, se completaron usando su historial de temporadas anteriores. Los valores numéricos faltantes restantes se rellenaron con la mediana, una técnica estadística que evita distorsiones por valores extremos.
4. **Creación de indicadores derivados**: se generaron variables nuevas que facilitan el análisis, entre ellas:
   - **Grupo de Edad**: clasifica a cada jugador como *Joven* (menor de 21), en su *Plenitud* (21–28) o *Veterano* (mayor de 28).
   - **Grupo de Valor**: categoriza el valor de mercado en Bajo, Medio o Alto.
   - **Brecha de Potencial**: mide cuánto le falta a un jugador para alcanzar su máximo rendimiento posible.
   - **Eficiencia Financiera**: indica cuántas veces el valor de mercado de un jugador supera su salario (a mayor número, más rentable es el jugador para su club).
5. **Controles de calidad**: se implementaron reglas de seguridad para marcar datos sospechosos, como edades fuera de rango o salarios inusualmente altos para jugadores de bajo valor.

### Fase II — Modelo de datos y análisis

En esta fase se diseñó la estructura analítica y se cargaron los datos en una base de datos relacional (MySQL):

1. **Diseño del modelo dimensional**: se creó un esquema tipo estrella con **4 dimensiones** (Club, Temporada, Grupo de Edad y Posición) y **5 tablas de hechos**, cada una diseñada para responder una pregunta de negocio específica.
2. **Carga automatizada**: se construyó un *pipeline* (proceso automático) que carga los datos temporada por temporada en la base de datos, facilitando actualizaciones futuras.
3. **Consultas y hallazgos**: se ejecutaron 5 consultas analíticas con sus respectivas interpretaciones. Entre los hallazgos más relevantes:
   - Los grandes clubes europeos (Real Madrid, Barcelona, Manchester City, PSG, Bayern München) dominan consistentemente el top 10 de valor de mercado promedio por jugador.
   - La Premier League inglesa lidera en valor total de mercado acumulado entre todas las ligas del mundo.
   - Los jugadores veteranos (mayores de 28 años) muestran los ratings de rendimiento más altos, especialmente en posiciones ofensivas.
   - Existe una fuerte concentración del margen financiero (valor − salario) en un grupo reducido de clubes élite.
4. **Optimización**: se aplicaron técnicas de indexación en la base de datos para mejorar los tiempos de consulta.


### Fase III — Análisis exploratorio de datos (EDA)

En esta fase se realizó un análisis exploratorio exhaustivo del dataset limpio, cubriendo distribuciones individuales, relaciones entre variables, detección de anomalías y rankings agregados de negocio:

1. **Análisis univariante**: se calcularon medidas de tendencia central y dispersión (media, mediana, moda, cuartiles, desviación estándar y sesgo) para las variables clave —edad, overall, potencial, valor de mercado, salario, además de dos variables elegidas por el equipo: *Brecha de Potencial* y *pace*—. Entre los hallazgos:
   - La edad está sesgada a la derecha: hay muchos más jugadores jóvenes que veteranos.
   - Valor de mercado y salario muestran una fuerte asimetría positiva (la mayoría gana/vale poco, mientras una minoría alcanza cifras extremas), lo que justificó usar escala logarítmica para su visualización.
   - La Brecha de Potencial tiene moda en 0 (la mayoría de jugadores ya está cerca de su techo), pero existen jóvenes promesas con un margen de hasta +28 puntos.
   - A nivel categórico, los mediocampistas y defensas son los grupos de posición más numerosos, el grupo de edad "Prime" domina con el 55% de los registros, y aproximadamente el 77% de los jugadores son diestros.

2. **Análisis bivariante y multivariante**: se construyó una matriz de correlación y se cruzaron variables numéricas con categóricas para identificar relaciones relevantes:
   - Valor de mercado y salario están altamente correlacionados (r = 0.78), mientras que edad y Brecha de Potencial muestran una correlación inversa fuerte (r = −0.84).
   - El overall se relaciona con el valor de mercado de forma exponencial: a mayor rating, el valor crece desproporcionadamente.
   - Los delanteros tienen el valor mediano más alto entre todas las posiciones, y el salario mediano aumenta consistentemente con la edad.
   - En el cruce multivariante (posición × grupo de edad), la combinación de jugadores ofensivos en su etapa "Prime" alcanza el mayor valor de mercado promedio.

3. **Detección de anomalías**: se aplicaron dos técnicas complementarias:
   - **Z-score** (umbral |z| > 3) sobre edad y salario, para identificar registros con valores estadísticamente extremos respecto al promedio del dataset.
   - **Rango Intercuartílico (IQR)** sobre la Brecha de Potencial, para detectar a los jóvenes con mayor margen de crecimiento proyectado.
   - Adicionalmente, se identificaron casos de negocio relevantes: jugadores con salario alto pero rating bajo (overall < 70), señalando posibles inconsistencias entre compensación y rendimiento.

4. **Análisis global y rankings de negocio**: se generaron rankings agregados sobre todo el dataset para responder preguntas estratégicas:
   - Top 10 jugadores por potencial máximo histórico.
   - Top 10 países por valor de mercado total generado.
   - Clubes con mejor ratio valor/salario (mayor eficiencia financiera).
   - Top 10 jóvenes (menores de 21 años) con rating superior a 80, identificando talento emergente de élite.
   - Potencial medio por grupo de edad, confirmando que los jugadores jóvenes concentran el mayor margen de crecimiento proyectado.
  
### Fase IV — Análisis predictivo y prescriptivo

En esta fase se construyeron modelos de machine learning para predecir variables de negocio y se diseñaron herramientas prescriptivas para apoyar decisiones reales de scouting, fichajes y gestión de plantilla:

1. **Regresión lineal — Valor de mercado**: se identificaron las 5 variables más correlacionadas con el valor de mercado (en escala logarítmica): overall (0.90), potencial (0.81), salario (0.71), passing (0.58) y dribbling (0.52). Tras un análisis de PCA que reveló alta redundancia entre las variables de rendimiento, el modelo final se entrenó únicamente con **overall y edad**, alcanzando un **R² de 0.89** y un **RMSE de 0.45** en escala logarítmica. La variable salario se excluyó deliberadamente para evitar endogeneidad, dado que valor y salario se determinan de forma simultánea en el mercado futbolístico.

2. **Regresión lineal — Salario**: siguiendo la misma lógica, se entrenó un modelo independiente para predecir el salario usando overall, potencial, passing, dribbling y edad (excluyendo el valor de mercado por la misma razón de endogeneidad). El modelo alcanzó un **R² de 0.58** y un **RMSE de 0.83**, con overall como predictor dominante.

3. **Clasificación — Alto Potencial**: se construyó una variable objetivo binaria (potencial > 85) y se compararon dos algoritmos bajo un fuerte desbalance de clases (apenas 1% de la clase positiva):
   - **Regresión logística**: accuracy 96%, recall 87%, precisión 17%, F1 0.29. Prioriza detectar la mayor cantidad posible de futuras promesas, a costa de muchos falsos positivos.
   - **Random Forest**: accuracy 99%, precisión 88%, recall 51%, F1 0.65. Logra un mejor balance general y fue seleccionado como el modelo más adecuado, validado también con un AUC superior (0.987 vs 0.973) en la curva ROC.

4. **Clustering — Tipos de jugador (K-Means + PCA)**: usando edad, potencial, valor y salario (transformados logarítmicamente), se aplicó K-Means con k=4 (determinado por el método del codo y validado con Silhouette Score) para segmentar a los jugadores en cuatro perfiles:
   - **Jóvenes de bajo potencial**: los más jóvenes, con menor rendimiento y valor.
   - **Estrellas establecidas**: jugadores en su etapa prime, con los valores más altos en todas las métricas.
   - **Veteranos consolidados**: mayor edad, potencial ya alcanzado y valor en descenso.
   - **Talento joven con proyección**: jóvenes con potencial y valor notablemente superiores al primer grupo, las futuras estrellas.
   
   Se utilizó PCA para reducir las 4 variables a 2 componentes y visualizar los clusters en un plano, confirmando una separación clara entre los perfiles.

5. **Análisis prescriptivo**: se diseñaron tres herramientas de apoyo a la decisión, replicadas como dashboards interactivos:
   - **Mercado de Transferencias**: dado un presupuesto, identifica jugadores jóvenes con alto potencial cuyo valor por punto de rating está por debajo de la media de su propio cohorte (los "subvalorados" dentro de su segmento).
   - **Construcción de Equipo**: arma un equipo de 11 jugadores maximizando el rating combinado dentro de un presupuesto, comparando un enfoque **Greedy** (secuencial, con look-ahead de presupuesto) contra un enfoque de **Programación Lineal** (linprog), el cual obtuvo un equipo de mayor calidad (950 vs 881 puntos de rating) y menor costo.
   - **Vender o No**: evalúa si conviene vender a un jugador comparando su valor por rating, salario y brecha de potencial contra el mercado de jugadores similares (mismo rango de overall y edad).
   - **Recomendación de contratación**: identifica, para cada posición (defensor, mediocampista, delantero), los jugadores con mejor relación overall/valor de mercado (eficiencia), filtrando por un overall mínimo para garantizar nivel competitivo real.
---

## Datos utilizados

- **Fuente**: datasets públicos de EA Sports FIFA (SoFIFA), temporadas 2015 a 2024.
- **Volumen**: 214 805 registros de jugadores a lo largo de 10 temporadas.
- **Variables principales**: nombre del jugador, edad, nacionalidad, club, liga, posición, valor de mercado (EUR), salario (EUR), y atributos de rendimiento (ritmo, disparo, pase, regate, defensa, físico, entre otros).

---

## Tecnologías utilizadas

| Herramienta | Para qué se usó |
|---|---|
| Python | Lenguaje principal de programación para todo el análisis |
| Pandas | Manipulación y limpieza de datos tabulares |
| Matplotlib y Seaborn | Creación de gráficos y visualizaciones |
| Scikit-learn | Imputación estadística de valores faltantes |
| MySQL | Base de datos relacional para almacenar el modelo dimensional |
| Google Colab | Entorno de ejecución en la nube |
| Google Drive | Almacenamiento de archivos del proyecto |

---


