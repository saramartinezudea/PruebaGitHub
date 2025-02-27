# Acompañamiento ciencia de datos
### Realizado por:  
### Carlos Estiben Castaño Aristizabal 
### Sara Valentina Martínez Gómez
------------
#### El presente trabajo se centra en la utilización de una base de datos obtenida de Datos Abiertos Colombia, la cual contiene información detallada sobre los precios de los medicamentos. A partir de estos datos, se han desarrollado códigos y aplicaciones estadísticas utilizando el lenguaje de programación Python, con el fin de facilitar su procesamiento y análisis. Este enfoque permite explorar de manera sistemática los conocimientos aprendidos en el curso acerca del uso de herramientas estadísticas y de programación que no sólo agiliza el manejo de grandes volúmenes de datos sino que también garantiza la precisión de los resultados. 
------------
#### Importar base de datos: 

```python
import pandas as pd
import requests
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.express as px
from sklearn.preprocessing import StandardScaler
from scipy import stats
import numpy as np

url = 'https://www.datos.gov.co/resource/3t73-n4q9.json'
response = requests.get(url)
data = response.json()
df = pd.DataFrame(data)

```
###### Inicialmente, se importan las bibliotecas esenciales como pandas para manipulación de datos, request para obtener datos desde un servidor, y herramientas de visualización y gráficos como matplotlib, seaborn y plotly. También se incluyen bibliotecas para reprocesamiento de datos (StandardScaler) y análisis estadístico (scipy.stats). Luego el código obtiene los datos desde un servidor público que es datos Colombia en formato JSON, utilizando requests.get(), y los convierte en un diccionario de Python. Estos datos se transforman en un DataFrame de pandas, estructura tabular que facilita su manipulación y análisis. 

###### Este código tiene como propósito principal ser el punto de partida para un proceso de análisis de datos. 

#### Analisis exploratorio inicial: 

```python
# Análisis exploratorio inicial

print("=== Estructura del dataset ===")
print(f"Filas: {df.shape[0]}, Columnas: {df.shape[1]}\n")
print("Primeras 5 filas:")
display(df.head())
```
![image](https://github.com/user-attachments/assets/138e2893-a173-4a20-b0f6-14e7912be8e4)

###### Seguidamente se realiza un análisis exploratorio inicial. Se muestra que el dataframe tiene 1000 filas y 9 columnas. Lo que indica que hay 1000 registros (filas) y 9 variables o características (columnas) en los datos, lo que permite entender la dimensión de los datos. Luego se muestran las primeras 5 filas del dataframe, lo que proporciona una vista preliminar o un ejemplo de cómo están organizados los datos y que tipo de información contienen. Este vistazo inicial es fundamental para familiarizarse con el contenido por ejemplo contiene información sobre medicamentos incluyendo variables como: principio activo (ingrediente farmacéutico activo),  unidad de dispensación (Entidad física en donde está contenida la dosis del medicamento), concentración (Cantidad de principio activo que contiene un medicamento), unidad base (mg o ml), nombre comercial (Nombre que identifica el medicamento de un determinado laboratorio farmacéutico), fabricante (Empresas autorizadas por la administración competente para fabricar medicamentos),  precio por tableta (precio equivalente por 1 forma farmacéutica) , factores precio y número factor (número de dosis).  

#### Obtener información
```python
print("\nInformación del dataset:")
display(df.info())

print("\nEstadísticas descriptivas:")
display(df.describe(include='all'))
```
![image](https://github.com/user-attachments/assets/2162654b-512d-4256-a151-88187b5d4bbb)

###### En este paso, para obtener información más detallada se utilizaron dos funciones claves df.info() y df.describe(include='all'). Primero, df.info() proporcionó información general sobre la estructura, donde de igual forma como el código anterior muestra 1000 filas (registros) y 9 columnas (variables), pero todas de tipo object (texto). Además indica que no hay valores faltantes, ya que todas las columnas tienen 1000 entradas no nulas, pero además sugiere que algunas columnas, como precio por tableta y número factor, se pueden convertir a variables numéricas ya que sus datos están en ese formato. Luego, df.describe(include='all') ofreció estadísticas descriptivas, revelando detalles como el número de valores únicos en cada columna, el valor más frecuente (top) y su frecuencia (freq).

###### Destacando entonces que la columna unidad de dispensación tiene 18 valores únicos, siendo “tableta” el más común (406 veces), mientras que precio por tableta tiene 979 valores únicos, lo que indica una alta variabilidad en los precios de los medicamentos.

#### Convertir precio tableta y numero factor a numerico
```python
# Convertir precio_por_tableta a numérico
df['precio_por_tableta'] = pd.to_numeric(df['precio_por_tableta'].astype(str).str.replace(',', '.'), errors='coerce')
df['numerofactor'] = pd.to_numeric(df['numerofactor'].astype(str).str.replace(',', '.'), errors='coerce')
```
###### En este paso, se realizó la conversión de dos columnas precio_por_tableta y numerofactor, a  variables de tipo numérico para poder realizar un análisis cuantitativo. La columna precio_por_tableta se transformó a un número decimal  (float64), reemplazando las comas por puntos para asegurar la correcta interpretación de los decimales. La columna numerofactor se convirtió a un número entero (int64).

#### Informacion y estadisticas
```python
print("\nInformación del dataset:")
display(df.info())

print("\nEstadísticas descriptivas:")
display(df.describe(include='all'))
```
![image](https://github.com/user-attachments/assets/0baf530f-661a-45e8-841e-29d374721c57)
###### Seguido a la conversión, se obtuvieron estadísticas descriptivas que revelan que el precio por tableta tiene una media de aproximadamente 42.502,93$ con una desviación estándar muy alta de 312.805,80 lo que indica una gran variabilidad en los precios. El valor mínimo es de 0,42$, mientras que el máximo alcanza 8.096.667, mostrando una amplia dispersión de los datos, se puede destacar que los datos revelan una gran disparidad en los precios de los medicamentos en Colombia ya que hay la existencia de medicamentos extremadamente costosos junto con otros mucho más accesibles. Esta variabilidad indica que, mientras algunos medicamentos son asequibles para la mayoría de la población, otros presentan precios significativamente elevados, lo que podría limitar su acceso para sectores de menores ingresos.

###### En cuanto a numerofactor, la media es de 2.08, con una desviación estándar de 0.67, desviación baja ya que el número de dosis oscila entre 1 y 3.

#### Los 8 medicamentos mas costosos
```python
# Top 8 medicamentos más caros
top_costosos = df.sort_values('precio_por_tableta', ascending=False).head(10)
plt.figure(figsize=(14, 7))
sns.barplot(x='nombre_comercial', y='precio_por_tableta', data=top_costosos)
plt.title('Top 8 medicamentos más caros')
plt.xlabel('Nombre comercial')
plt.ylabel('Precio por tableta',)
plt.show()
```
![image](https://github.com/user-attachments/assets/82cc73b7-7077-4ba4-89c2-3d6f55fdca64)
```python
# Top 8 medicamentos más caros
top_costosos = df.sort_values('precio_por_tableta', ascending=False).head(10)
plt.figure(figsize=(14, 7))
sns.barplot(x='principio_activo', y='precio_por_tableta', data=top_costosos)
plt.title('Top 8 medicamentos más caros')
plt.xlabel('Principio activo')
plt.ylabel('Precio por tableta',)
plt.show()
```
![image](https://github.com/user-attachments/assets/42561174-eab8-4dcd-852c-9c89755866fa)
###### Los códigos siguientes permitieron identificar los 8 medicamentos más costosos en Colombia tanto por su nombre comercial como por su principio activo, utilizando un código de barras para visualizar los resultados. En cuanto a sus ejes en el eje X está la variable de principio activo o nombre comercial y en el eje Y se representa el precio por tableta. Si comparamos las alturas de cada barra determinamos que el precio más alto por tableta (8 millones de pesos) es el Meta-iodobenzyl guanidina (Iobenguane) que destaca por encima de los demás, hay una tendencia decreciente entre los valores y diferencias entre los medicamentos más costosos ya que van desde los 8 millones de pesos hasta 1 millón de pesos. 

###### En términos generales los medicamentos más caros incluyen  Meta-iodobenzyl guanidina (Iobenguane), Herceptin (Trastuzumab), Metalyse (Tenecteplasa), Daxim (Levosimendan), Curosurf (Fosfolípidos), Dysport (Toxina Botulínica), Remicade (Infliximab) y Benefix (Factor IX de coagulación recombinante). Estos medicamentos son utilizados para tratar condiciones médicas complejas y especializadas, como:


###### Iobenguane: Es un radiofármaco que se usa en el diagnóstico y tratamiento de tumores neuroendocrinos, como el feocromocitoma y el neuroblastoma.
###### Trastuzumab: Es un medicamento biotecnológico utilizado en el tratamiento del cáncer de mama HER2 positivo, un tipo de cáncer agresivo.
###### Tenecteplasa: Es un activador del plasminógeno empleado en emergencias médicas para disolver coágulos en casos de infarto agudo de miocardio o accidente cerebrovascular.
###### Levosimendan: Es un sensibilizador del calcio usado en el manejo de la insuficiencia cardíaca aguda, especialmente en pacientes con bajo gasto cardíaco.
###### Fosfolípidos: Componente principal del Curosurf se usa para tratar o prevenir el Síndrome de Distrés Respiratorio (SDR) en recién nacidos. 
###### Toxina Botulínica: Además de su uso cosmético, se emplea en condiciones médicas como distonías, espasticidad y migrañas crónicas.
###### Infliximab: Es un anticuerpo monoclonal denominado medicamento biológico  utilizado en enfermedades autoinmunes como la enfermedad de Crohn, la colitis ulcerosa y la artritis reumatoide.
###### Factor IX de coagulación recombinante: Esencial para el tratamiento de la hemofilia B, un trastorno hemorrágico hereditario.

###### El alto costo de estos medicamentos se debe a que son productos especializados, muchos de ellos biotecnológicos, que requieren de procesos de fabricación complejos y costosos. Además algunos de ellos están destinados a tratar enfermedades o condiciones de salud raras, lo que justifica su elevado precio. Sin embargo, esta situación también plantea un desafío en términos de acceso y equidad en el sistema de salud, ya que muchos pacientes podrían enfrentar dificultades para obtener estos tratamientos debido a su alto costo. 

#### Cantidad de medicamentos por fabricante
```python
# Análisis por fabricante
fabricantes = df['fabricante'].value_counts().head(10)
plt.figure(figsize=(14, 7))
fabricantes.plot(kind='bar')
plt.title('Top 10 fabricantes por cantidad de medicamentos')
plt.xlabel('Fabricante')
plt.ylabel('Cantidad')
plt.show()
```
![image](https://github.com/user-attachments/assets/5b2152f8-1e84-4564-b0de-f7656600c706)
###### El código anterior genera un gráfico de barras que muestra los 10 principales fabricantes de medicamentos en Colombia (eje X) según la cantidad de medicamentos que producen (eje Y). El gráfico de barras muestra que Genfar es el fabricante con la mayor cantidad de medicamentos, seguido por Tecnoquímicas y Pfizer. Esto indica que estos fabricantes tienen una presencia significativa en el mercado colombiano de medicamentos.

###### La diferencia en la altura de las barras sugiere variaciones en la producción o distribución entre los fabricantes. Por ejemplo Genfar tiene una cantidad notablemente mayor de medicamentos ya que oscila entre 60 a 70 en comparación con Tecnofar que es de 20 y aparece en el décimo lugar. Este análisis puede ser útil para identificar líderes en la industria.

#### Precio promedio por fabircante los caros 
```python
# Precio promedio mas caros por fabricante
precio_fabricante = df.groupby('fabricante')['precio_por_tableta'].mean().sort_values(ascending=False).head(10)
precio_fabricante.plot(kind='bar', figsize=(14, 7))
plt.title('Top 10 fabricantes por precio promedio')
plt.xlabel('Fabricante')
plt.ylabel('Precio promedio por tableta')
plt.show()
```
![image](https://github.com/user-attachments/assets/e0231831-bbc1-4f15-96c3-bc1f315a6d39)
###### Este código se utilizó para analizar y visualizar cuáles son los 10 fabricantes que tienen el precio promedio más alto por tableta. Se agrupa el DataFrame por la columna 'fabricante' y se calcula la media de la columna 'precio_por_tableta' para cada fabricante. Esto permite conocer el precio promedio de las tabletas ofrecidas por cada fabricante. Luego, se ordenan estos promedios de forma descendente (de mayor a menor) y se seleccionan los 10 fabricantes con los precios promedio más altos. Finalmente, se genera un gráfico de barras en el que se muestra visualmente el precio promedio por tableta para cada uno de estos 10 fabricantes.

#### Precio promedio por fabircante los economicos
```python
# Precio promedio por fabricante (los 10 más baratos)
precio_fabricante = df.groupby('fabricante')['precio_por_tableta'].mean().sort_values(ascending=True).head(10)
precio_fabricante.plot(kind='bar', figsize=(14, 7))
plt.title('Top 10 fabricantes por precio promedio más bajo')
plt.xlabel('Fabricante')
plt.ylabel('Precio promedio por tableta')
plt.show()
```
![image](https://github.com/user-attachments/assets/f6498775-efbd-4709-8031-6586b86d2601)
###### Con este código hacemos lo contrario al anterior, se empleó para analizar y visualizar cuáles son los 10 fabricantes que tienen el precio promedio más bajo por tableta, se realizó el mismo procedimiento pero ya se ordenó los promedios de forma ascendente tomando los primeros 10 que serian los de menor precio y igual al anterior se genera un gráfico de barras en el que se muestra visualmente el precio promedio por tableta para cada uno de estos 10 fabricantes. 

#### Principios activos mas comunes
```python
# Análisis de principios activos
principios_activos = df['principio_activo'].value_counts().head(10)
plt.figure(figsize=(14, 7))
principios_activos.plot(kind='barh')
plt.title('Principios activos más comunes')
plt.xlabel('Cantidad')
plt.ylabel('Principio activo')
plt.show()
```
![image](https://github.com/user-attachments/assets/b6257af8-c666-4c94-acde-058979b8f9f6)
###### Este código se utilizó para identificar y visualizar cuáles son los principios activos que aparecen con mayor frecuencia en la base de datos. Primero lo que se hizo fue contar la frecuencia de cada principio activo con df['principio_activo'].value_counts() ya que este calcula cuántas veces aparece cada principio activo en la columna principio_activo. Luego se seleccionan los 10 más frecuentes con el método .head(10) que extrae únicamente los primeros 10 principios activos más frecuentes. Y por último se crea un gráfico de barras lo que facilita la lectura de los nombres de los principios activos.

###### Esto puede ayudar a identificar tendencias de uso de ciertos medicamentos, patrones de consumo, áreas terapéuticas más comunes o posibles nichos de investigación en el mercado farmacéutico.

#### Precio vs factor precio
```python
# Precio vs Factor de precio
plt.figure(figsize=(14, 7))
sns.boxplot(x='factoresprecio', y='precio_por_tableta', data=df)
plt.title('Relación entre factor de precio y precio por tableta')
plt.yscale('log')  # Escala logarítmica para mejor visualización
plt.show()

```
![image](https://github.com/user-attachments/assets/ce529502-c78e-40df-b0d3-eded3f79d290)

###### Este código lo empleamos para crear un boxplot (gráfico de cajas) para analizar la distribución de la columna precio_por_tableta en función de la categoría factoresprecio. Esto nos permite la visualización de la distribución y valores atípicos, ya que el boxplot muestra la mediana, los cuartiles (25% y 75%) y los valores atípicos (puntos fuera de los bigotes) para cada categoría en factoresprecio. Esto permite comparar cómo se distribuyen los precios en cada grupo. Luego quisimos poner en escala logarítmica con la instrucción plt.yscale('log') ya que ayuda a manejar la gran variación en los precios, haciendo que las diferencias entre valores altos y bajos sean más claras y menos distorsionadas en el gráfico.

###### Al comparar las cajas para cada factor de precio, esperábamos ver si ciertos grupos tienden a tener precios más altos o más bajos, identificar si hay mucha dispersión o si la mayoría de los valores están concentrados en un rango específico.

#### Deteccion de outliers
```python
# Detección de outliers usando Z-score
from scipy import stats

# Ensure 'precio_por_tableta' is numeric
df['precio_por_tableta'] = pd.to_numeric(df['precio_por_tableta'], errors='coerce')

# Calculate z-scores
z_scores = stats.zscore(df['precio_por_tableta'].dropna())

# Get indices of outliers using .to_numpy().nonzero()
outlier_indices = (z_scores > 3).to_numpy().nonzero()[0]

outliers = df.iloc[outlier_indices]
print("\nOutliers en precios:")
display(outliers[['nombre_comercial', 'precio_por_tableta', 'fabricante']])
```
![image](https://github.com/user-attachments/assets/85e31069-fa0a-4119-972f-13e64ded77a5)
###### Por último este código lo empleamos para identificar valores atípicos (outliers) en la columna precio_por_tableta usando el método del Z-score. Donde primero se asegura de que la columna precio_por_tableta contenga datos numéricos. Si hay valores que no se pueden convertir, se reemplazan por NaN. Luego se calculan los Z-scores para los valores de precio_por_tableta (excluyendo los NaN). El Z-score indica cuántas desviaciones estándar se encuentra cada valor respecto a la media. Se identifican aquellos valores cuyo Z-score es mayor a 3 (un criterio común para detectar outliers). Se obtienen los índices de estos valores usando .to_numpy().nonzero()[0]. Con los índices identificados, se extraen las filas correspondientes del DataFrame original y se muestran columnas relevantes (nombre_comercial, precio_por_tableta, fabricante).

###### Esto se podría emplear para identificar registros con precios por tableta que se desvían significativamente del promedio, lo cual puede indicar errores de registro o casos especiales. Así mismo al detectar estos outliers, se pueden revisar y corregir datos incorrectos o analizar si corresponden a condiciones particulares y por último ayuda a comprender la distribución de precios y a tomar decisiones informadas en términos de estrategias de precios y segmentación de productos.

## Análisis de resultados
###### Tras analizar la base de datos sobre los precios de los medicamentos en Colombia, se pudo observar los ocho medicamentos más costosos empleados en el país los cuales son Meta-iodobenzyl guanidina (Iobenguane), Herceptin (Trastuzumab), Metalyse (Tenecteplasa), Daxim (Levosimendan), Curosurf (Fosfolípidos), Dysport (Toxina Botulínica), Remicade (Infliximab) y Benefix (Factor IX de coagulación recombinante). Las posibles razones para que los precios de estos medicamentos estén entre uno y ocho millones de pesos son resultado de una combinación de factores tecnológicos, económicos, regulatorios y sociales. Por ejemplo la investigación y desarrollo de medicamentos biotecnológicos como lo son algunos de estos como Trastuzumab (Herceptin) y el Infliximab (Remicade), desarrollados mediante procesos complejos requieren inversiones multimillonarias. Estos costos se recuperan mediante precios altos, especialmente durante los años de exclusividad de patente, lo cual limita la competencia reduciendo la competencia en precios, sin embargo es de esperarse ya que en el caso de el Trastuzumab, usado en cáncer de mama HER2+, tardó más de una década en desarrollarse, con ensayos clínicos costosos.
###### Así mismo estos biotecnológicos requieren cultivos celulares y procesos de purificación estrictos, para el caso de el Factor IX (Benefix) y la Toxina Botulínica (Dysport) dependen de sistemas de producción biológica únicos, lo que limita la competencia, situación que genera la posibilidad para la industria de mantener los precios elevados.

###### Otra posible razón para que los precios de estos medicamentos sean tan elevados es que algunos de estos son para enfermedades raras o poblaciones muy pequeñas, lo cual va a repercutir de dos formas, la primera es que el desarrollo de estos productos va requerir los mismos pasos que los demás medicamentos, es decir que su desarrollo es costoso, pero a diferencia de la mayoría de medicamentos estos son dirigidos a pocas personas, por lo que se hace necesario fijar precios altos para recuperar inversiones ante pocos pacientes, y por otro lado la baja tasa que presentan estas enfermedades reduce el incentivo para competidores, generando monopolios en estos medicamentos.

###### Adicional a lo anterior, en Colombia no se producen estos medicamento, se deben importar lo cual debido aranceles, fletes y seguros, así como a cadenas de frío que siguen por ejemplo los tecnológicos hacen que sean más costosos, además debido a que son importados si los precios se fijan en dólares, la devaluación del peso colombiano encarece estos medicamentos cada dia mas. Todo esto sumado a que Colombia ha sido lenta en aprobar biosimilares. Esto permite a las farmacéuticas originales mantener precios altos.

###### Luego al observar cuales son los laboratorios con mayor cantidad de productos observamos que no son ninguno de los que elaboran los medicamentos anteriores, por el contrario estas casas farmacéuticas que son: Genfar, tecnoquimicas, pfizer, lafrancol, Sanofi Aventis, Procaps, Glaxosmithkline, Laproff, La Sante y Tecnofar suelen tener catálogos extensos que abarcan múltiples líneas terapéuticas (analgésicos, antibióticos, antihipertensivos, etc.), así mismo sus productos son de los mas baratos y comerciales que se manejan, lo cual les permite tener esta gran cantidad de medicamentos, ya que como se ve en la gráfica cuentan con entre 20 y 70 medicamentos, además de tener una gran variedad de líneas a lo que se puede deber que estos sean los que mayor portafolio tienen es que estos laboratorios son muy reconocidas en el sector de genéricos, lo cual les permite tener un catálogo grande a precios competitivos y una alta rotación en el mercado, ya que la gran demanda de genéricos (por ser más económicos) fomenta que estas compañías mantengan o amplíen su oferta. En contraste con lo anterior estos medicamentos al ser de mayor rotación y tener un mercado de genéricos también son mas baratos, por lo que puede ser una base para que el Ministerio de Salud o las EPS pueden usar estos datos para monitorear la disponibilidad de medicamentos esenciales y regular los precios o promover la competencia y también tratar de llevarlo a medicamentos de mayor costo.Por otra parte las farmacéuticas pueden analizar a la competencia para ver en qué segmentos hay saturación de productos y dónde hay oportunidades de expansión.

###### Al evaluar cuales eran los laboratorios que en promedio vendían los medicamentos más costosos, concuerdan justamente con los fabricantes de los medicamentos tratados al inicio, lo cuales son biotecnológicos o son especializados, a su vez al evaluar cuales eran los laboratorios que en promedio venden los medicamentos más baratos, son aquellos que fabrican mayor cantidad de genéricos y medicamentos esenciales, a su vez que tienen un portafolio de productos más amplio que los que venden más costosos, por lo que se observa una tendencia en la industria farmacéutica de vender diferentes medicamentos del dia a dia en cantidad y con un gran portafolio de una forma barata o vender medicamentos de alto costo siendo casi el único producto del portafolio ofrecido.

###### Por un lado, la innovación y la especialización en medicamentos de nicho justifican precios altos, respaldados por patentes y por la complejidad de su desarrollo. Por otro, la competencia en genéricos y esenciales impulsa una estrategia de alto volumen y bajos márgenes, beneficiando la disponibilidad de medicamentos para la población general, por lo que a almenos medicamentos de alto costo pero con alta demanda se les debería motivar la creación de genéricos, ya que los medicamentos de alto costo pueden limitar el acceso a quienes no estén cubiertos por seguros o por el sistema de salud, lo que genera brechas en la atención, mientras que los medicamentos genéricos y esenciales a bajo costo facilitan el acceso de la población a tratamientos comunes, fomentando la equidad en salud. Por otra parte el costo de los medicamentos especializados puede suponer un gran gasto para las EPS, aseguradoras o el mismo Estado, impactando la sostenibilidad financiera si no hay controles de precios o negociaciones centralizadas, mientras que la producción y oferta de genéricos favorece la reducción del gasto farmacéutico, equilibrando la balanza para el sistema de salud. Por esto incentivar para el desarrollo de ciertos tipos de genéricos podría ser beneficioso en general para la salud de los colombianos como para el sistema de salud, así mismo es un oportunidad para la industria farmacéutica de ampliar el portafolio de productos.

###### En otro segmento se analizó los principios activos más comunes y usados en el país, de los cuales se obtuvo: Levotiroxina Sódica (hipotiroidismo)

###### Su alto consumo sugiere que los trastornos de la tiroides (especialmente hipotiroidismo) son relativamente frecuentes.
###### Puede indicar factores genéticos, ambientales o de diagnóstico oportuno; también refleja la importancia de un tratamiento crónico y constante.
###### Atorvastatina (hipercolesterolemia / riesgo cardiovascular), Ibuprofeno, Acetaminofén, Tramadol (analgésicos / antiinflamatorios), Sildenafil (disfunción eréctil o PAH), Metoclopramida (antiemético / proquinético), Desloratadina, Cetirizina (antihistamínicos) y Clotrimazol (antifúngico), tras observar estos datos se podría decir que una razón es que sean tan usados es que se consiguen fácilmente a precios relativamente bajos y en presentaciones genéricas, sin embargo hay que tener mucho cuidado y debería ser una problemática a observar por entes de salud ya que alto consumo de analgésicos y antihistamínicos puede reflejar una cultura de automedicación, donde las personas se tratan síntomas comunes sin acudir necesariamente a un médico, esto puede conllevar riesgos de uso inadecuado o abuso, y resalta la necesidad de mayor educación en salud y regulación.

###### Así mismo el uso de estos medicamentos puede dar indicios de diferentes cualidades en la población colombiana, como lo puede ser que hay una gran demanda de medicamentos para enfermedades crónicas, ya que el uso elevado de estatinas (atorvastatina) y hormonas tiroideas (levotiroxina) sugiere que las enfermedades crónicas metabólicas, cardiovasculares, endocrinas son un problema relevante en la población. Por otra parte, la demanda de analgésicos (ibuprofeno, acetaminofén, tramadol) apunta a que el dolor es una de las principales razones de automedicación. Finalmente se puede hablar de un envejecimiento de la población, ya que el aumento en el uso de sildenafil puede estar relacionado con el envejecimiento de la población masculina a tratar la disfunción eréctil, además se es claro que cada día la mala alimentación y el sedentarismo crecen desbordadamente, causas de hipercolesterolemia, hipertensión y obesidad, lo cual eleva el consumo de medicamentos crónicos.

###### Estos datos sugieren una necesidad de prevención y educación, es decir fomentar hábitos saludables (ejercicio, dieta equilibrada) para reducir la prevalencia de dislipidemias y otras enfermedades crónicas y realizar campañas de concienciación sobre el uso racional de analgésicos y antihistamínicos para evitar la automedicación. Asi mismo si bien es delicado el mal uso que se le pueden dar a estos medicamentos es claro que son una necesidad y que dan grandes beneficios a la sociedad colombiana por lo que asegurar la accesibilidad a estos también es muy importante, es decir que sigue ratificando lo importante de promover la competencia de genéricos para mantener costos asequibles.













