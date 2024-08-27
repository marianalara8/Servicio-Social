# PCA merra: comparacion de periodos


Para este proyecto se utilizaron datos del proyecto MERRA-2 de la NASA disponibles en el vínculo: https://disc.gsfc.nasa.gov/datasets. En estos datos, se tiene disponible información de 1890 a 2024, conteniendo distintas variables entre las cuales se encuentra la tasa de humedad en la zona de raíces por píxel, la cual se utilizo, los datos de esta se encuentra como valores que van de 0 a 1, donde 0 es un pixel sin humedad en la zona de raices y 1 representa un pixel con saturacion en la zona de raices.

Así, se realizó una minería de datos, obteniendo dos series de tiempo, la primera en un periodo de 1980 a 1995 y la segunda abarcando desde 2008 hasta 2023, con estos datos se realizaron dos PCA, los cuales se realizaron con el siguiente codigo:

  #cargar las librerias que se utilizaran
  library(FactoMineR)
  library(factoextra)
  library(raster)
  library(textshape)
  
  #cargar los datos
  datos <-read.csv("~/datos_merra_1980-1995.csv")
  #eliminar datos con NA para evitar problemas 
  datos<-na.omit(datos)
  

