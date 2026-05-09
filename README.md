## Autores

- **Angie Melissa Aguilar Alemán**
- **Magdaleno Gómez Díaz**
- **Sharon Dayana Blanco Piedra**



#  Análisis de Datos de Jugadores FIFA (2015–2024)

## ¿De qué trata este proyecto?

Este proyecto analiza información de más de **150,000 jugadores de fútbol** registrados en el videojuego FIFA, abarcando **10 temporadas** (de 2015 a 2024). A partir de estos datos públicos — que incluyen valor de mercado, salario, rendimiento, edad, posición y club de cada jugador — se construyó un sistema que permite responder preguntas de negocio como:

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

El proyecto se desarrolla en **6 fases**: (Por el momento llevamos dos concluidas)

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

---

## Datos utilizados

- **Fuente**: datasets públicos de EA Sports FIFA (SoFIFA), temporadas 2015 a 2024.
- **Volumen**: aproximadamente 150,000+ registros de jugadores a lo largo de 10 temporadas.
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


