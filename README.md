# Pix2Pix_RestauraMapas_PredClima
En este repositorio se recogen dos proyectos en los que se utiliza el modelo Pix2Pix de Deep Learning para restaurar mapas de temperaturas y para hacer predicciones en base a la temperatura que hace hoy.

Explicaré los dos proyectos de manera separada aunque usan la misma arquitectura y el mismo dataset. 

Antes de meternos en los dos proyectos es necesario explicar el procesamiento de datos que se hizo. Se obtuvieron 3.5 GB de datos de la NOAA (https://www.noaa.gov) en los que venía recogidos datos meteorológicos registrados por diferentes estaciones meteorológicas en todo el mundo en un perído que abarca desde 1840 hasta hoy en día. Al ser muchos datos se tuvo que hacer una limpieza masiva ya que había registros erroneos, algunos que no cuadraban con la fecha en la que se había registrado, etc. 

Para el proceso de limpieza se utilizó C# y Python. EL primer paso fue separar la variable de interés (temperatura máxima), esto redujo el dataset a 2 GB. Luego, al tener datos de muchas estaciones meteorológicas, procedimos a coger la latitud y longitud de cada una de estas para graficar y ver en que zona había más datos:

