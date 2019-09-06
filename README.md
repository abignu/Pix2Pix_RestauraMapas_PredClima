# Pix2Pix_RestauraMapas_PredClima
En este repositorio se recogen dos proyectos en los que se utiliza el modelo Pix2Pix de Deep Learning para restaurar mapas de temperaturas y para hacer predicciones en base a la temperatura que hace hoy.

Explicaré los dos proyectos de manera separada aunque usan la misma arquitectura y el mismo dataset. 

Antes de meternos en los dos proyectos es necesario explicar el procesamiento de datos que se hizo. Se obtuvieron 3.5 GB de datos de la NOAA (https://www.noaa.gov) en los que venía recogidos datos meteorológicos registrados por diferentes estaciones meteorológicas en todo el mundo en un perído que abarca desde 1840 hasta hoy en día. Al ser muchos datos se tuvo que hacer una limpieza masiva ya que había registros erroneos, algunos que no cuadraban con la fecha en la que se había registrado, etc. 

Para el proceso de limpieza se utilizó C#. EL primer paso fue separar la variable de interés (temperatura máxima), esto redujo el dataset a 2 GB. Luego, al tener datos de muchas estaciones meteorológicas, procedimos a coger la latitud y longitud de cada una de estas para graficar y ver en que zona había más datos:

*insertar imagen de estaciones world.png* 

Como se ve, USA es la que más estaciones tiene, por lo que decidimos restringir el dataset de las TMAX a las estaciones en USA. El dataset se redujo a 1.6 GB. Todo esto se hizo con C#. El siguiente paso fue separar las estaciones junto con su latitud y longitud, por año, mes y día y la temperatura asociada a ese día. Esto generó muchos registros en el dataset elevándolo a 6 GB. Este dataset se lo introdujo en una base de datos para manejarlo con mayor facilidad. Luego de subirlo, mediante 'queries', se siguió limpiando el dataset. Quitamos más registros incoherentes, ajustamos las temperaturas a un rango lógico ya que había muchas temperaturas que se salían de la media. Esto lo hicimos por el hecho de que a lo mejor se trató de una mala medida de ese día por lo que nos restringimos a 52ºC y -18ºC que son los registros históricos de USA. 

Una vez hecha toda la limpieza de datos, utilizando C#, se descargaron datos desde 1980 hasta 2019 y, mapeamos el valor de la TMAX dada por una estación a través de un color a un mapa para poder representar el mapa de temperaturas. El resultado final es el siguiente (imagen 256x256):

*insertar una imagen ejemplo del dataset*

Las líneas de colores a los costados son un código de colores que representan día y mes del año. La barra horizontal representa los días y la vertical el mes. Esto, de alguna forma, para el primer proyecto ayuda a la predicción del día siguiente. 

Una vez obtenido el dataset principal, se construyó el modelo de red neuronal a utilizar en TF 2.0. 

**Proyecto 1:**

El primer proyecto consistió en utilizar el modelo Pix2Pix para predecir el clima de mañana en base al mapa de temperaturas de hoy. De esta forma, el input era el mapa de hoy y el target el mapa de mañana. 

Ambas imágenes están en la misma carpeta ya que la target de una iteración es el input de la siguiente. Llevan un nombre que las identifica con el siguiente formato yyyymmdd-xxxxxx.png dónde los últimos seis dígitos son el número de imagen en el dataset. 
