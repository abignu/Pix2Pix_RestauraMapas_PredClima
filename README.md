# Pix2Pix_RestauraMapas_PredClima
En este repositorio se recogen dos proyectos en los que se utiliza el modelo Pix2Pix de Deep Learning para restaurar mapas de temperaturas y para hacer predicciones en base a la temperatura que hace hoy.

Explicaré los dos proyectos de manera separada aunque usan la misma arquitectura y el mismo dataset. 

Antes de meternos en los dos proyectos es necesario explicar el procesamiento de datos que se hizo. Obtuve 3.5 GB de datos de la NOAA (https://www.noaa.gov) en los que venía recogidos datos meteorológicos registrados por diferentes estaciones meteorológicas en todo el mundo, en registros mensuales, en un perído que abarca desde 1840 hasta hoy en día. Al ser muchos datos tuve que hacer una limpieza masiva ya que había registros erroneos, algunos que no cuadraban con la fecha en la que se había registrado, etc. 

Para el proceso de limpieza utilicé C#. EL primer paso fue separar la variable de interés (temperatura máxima), esto redujo el dataset a 2 GB. Luego, al tener datos de muchas estaciones meteorológicas, procedí a coger la latitud y longitud de cada una de estas para graficar y ver en que zona había más datos (cada pixel es una estación):

Gráfico de todas las estaciones disponibles entre 1840 y 2019

![world](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/world.png)

Gráfico de estaciones acotado a USA entre 1840 y 2019

![usa](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/usa.png)

Como se vé, USA es la que más estaciones tiene, por lo que decidí restringir el dataset de las TMAX a las estaciones en USA. El dataset se redujo a 1.6 GB. Todo esto se hizo con C#. El siguiente paso fue separar las estaciones junto con su latitud y longitud, por año, mes y día y la temperatura asociada a ese día. Esto generó muchos registros en el dataset elevándolo a 6 GB. Este dataset se lo introdujo en una base de datos para manejarlo con mayor facilidad. Luego de subirlo, mediante 'queries', seguí limpiando el dataset. Quité más registros incoherentes, ajusté las temperaturas a un rango lógico ya que había muchas temperaturas que bien superaban la temperatura máxima record, o bien eran inferiores a la mínima histórica. Esto lo hice por el hecho de que a lo mejor se trató de una mala medida de ese día por lo que me restringí a 52ºC y -18ºC que son los registros históricos de USA. 

Una vez hecha toda la limpieza de datos, utilizando C#, se indexaron sólo los datos desde 1980 hasta 2019 y, se mapearon los valores de la TMAX dada por una estación a través de un color a un mapa para poder representar el gradiente de temperaturas. El resultado final es el siguiente (imagen 256x256):

Ejemplo de un día de verano

![verano](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/19890805-003504.png?raw=true)

Ejemplo de un día de invierno

![invierno](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/19891221-003642.png?raw=true)

Las líneas de colores a los costados son un código de colores que representan día y año. La barra horizontal representa los días (1 a 365) y la vertical el año. Esto, de alguna forma, para el primer proyecto, ayuda a la predicción del día siguiente. 

Una vez obtenido el dataset principal, se construyó el modelo de red neuronal a utilizar en TF 2.0. 

**Proyecto 1:**

El primer proyecto consistió en utilizar el modelo Pix2Pix para predecir el clima del día de mañana en base al mapa de temperaturas del día de hoy. De esta forma, el input era el mapa de hoy y el target el mapa de mañana. 

La motivación principal de utilizar Pix2Pix para predecir el clima es por la curiosidad de comprobar si esta estructura de redes neuronales, utilizada para transformar imágenes en imágenes, puede también adaptarse a inferir nuevos datos a partir de los existentes.
Es decir, constatar si el modelo puede generar una imagen real en *otra real* (o al menos cercana a ella) y no en una hipotética.

Ambas imágenes están en la misma carpeta ya que la target de una iteración es el input de la siguiente. Llevan un nombre que las identifica con el siguiente formato yyyymmdd-xxxxxx.png dónde los últimos seis dígitos son el número de imagen en el dataset. 

Se hizo un entrenamiento con 3200 imágenes. El 20% de eso se pasó a testeo. 

Los resultados fueron los siguientes:

![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/resultados_proyecto1/result1.jpg)
![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/resultados_proyecto1/result2.jpg)

En el repositorio se ha subido el dataset para que cada uno pueda usarlo a su gusto. En el notebook correspondiente, al final hay una celda ejecutable para que le cargues la imagen que quieras. Debe respetar el código de fechas (hay que acordarse de modificar las rutas de carpetas). 

Las conclusiones extraídas de este proyecto es que generó mapas predichos bastante ajustados a la realidad. Aunque consideramos que aumentando los EPOCHs y extendiendo el dataset de entrenamiento el resultado debería haber sido más fidedigno. 

**Proyecto 2:**

En el segundo proyecto se le dio una vuelta de tuerca con el objetivo de hacer que el output del proyecto 1 luzca de forma más parecida a lo que es un mapa de temperaturas máximas utilizados en meteorología. De esta forma, avanzando en mejoras del proyecto se puede lograr que los climatólogos utilicen este modelo para mejorar los mapas generados a partir de sus datos. 

Se cogieron 1000 imágenes del dataset y se les aplicó una 'acción' en photoshop en la que se retocó la imagen para dejarla lo más aceptable posible:

Ejemplo 1:

![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/19800627-000178-origen.png?raw=true)
![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/19800627-000178.png?raw=true)

Ejemplo 2:

![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/19800109-000008-origen.png?raw=true)
![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/19800109-000008.png?raw=true)

Claramente, el nuevo target será la imagen retocada. El input sigue siendo la imagen mostrada más arriba. 

En el repositorio 'origen' es el input y 'destino' el target. 

Se entrenó sobre 1000 imágenes (20% testeo). Los resultados fueron los siguientes:

![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/resultados_proyecto2/result1.jpg)
![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/resultados_proyecto2/result2.jpg)
![](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/resultados_proyecto2/result3.jpg)

Como vemos, logra recontruir la imagen de manera certera. Esto, sin lugar a dudas, es de mucha utilidad para diferentes aplicaciones de cara a la climatología y mapas satélitales en mal estado. 

Aquí les dejo un gif de los outputs recreados:

![alt text](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/gif_outputs.gif)

Este es otro gif a partir de unas imágenes con las que entrenó de enero de 1980:

![alt text](https://github.com/abignu/Pix2Pix_RestauraMapas_PredClima/blob/master/images/gif_invierno_img_corregidas.gif)


