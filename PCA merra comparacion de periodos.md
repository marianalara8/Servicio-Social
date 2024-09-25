# PCA merra: comparacion de periodos

En este scrip, se describen los procedimientos que se realizaron para la obtencion de un analisis de Principal Componet Analisis (PCA) que permita comprobar o refutar la hipotesis de que existe una correlacion entre el uso del suelo y la humedad del suelo en la zona de raices.  

Para este proyecto se utilizaron datos del proyecto MERRA-2 de la NASA disponibles en el vínculo: https://disc.gsfc.nasa.gov/datasets. En estos datos, se tiene disponible información de 1890 a 2024, conteniendo distintas variables entre las cuales se encuentra la tasa de humedad en la zona de raíces por píxel, la cual se utilizo, los datos de esta se encuentra como valores que van de 0 a 1, donde 0 es un pixel sin humedad en la zona de raices y 1 representa un pixel con saturacion en la zona de raices.

Así, se realizó una minería de datos, obteniendo dos series de tiempo, la primera en un periodo de 1980 a 1995 y la segunda abarcando desde 2008 hasta 2023, con estos datos se realizaron dos PCA, los cuales se realizaron con el siguiente codigo:

    #cargar las librerias que se utilizaran
    library(FactoMineR)
    library(factoextra)
    library(raster)
    library(textshape)
  
    #cargar los datos
    datos_1980.1990 <-read.csv("~/datos_merra_1980-1990.csv")
    datos_2013.2023 <-read.csv("~/datos_merra_2013-2023.csv")
    #eliminar datos con NA para evitar problemas 
    datos_1980.1990<-na.omit(datos)
    datos_2013.2023<-na.omit(datos_2013.2023)

La dos bases de datos previamente cargadas, contienen los datos con coordenadas globales de humedad en suelo de raices para cada mes de la serie temporal de 1980 a 1990 y para la serie de 2013 a 2023. 

Posterior a cargar lo datos, se realizo el siguiente codigo para obtener los resultados del PCA

    pca_1980.1990 <- PCA(X= datos_merra_1980.1990, scale.unit = FALSE, graph = TRUE)
    pca_2013.2023 <- PCA(X= datos_merra_2013.2023, scale.unit = FALSE, graph = TRUE)

Al analizar los reusltados obtenidos se obtuvo que el primer y segundo ejenvector explican el ~90% de la varianza de los datos para ambos PCA, por ello se trabajara a partir de estos. 

    #extraer el primer ejenvector y segundo ejenvector
    u1_1980<- pca_1980.1990$svd$U[,1]
    u2_1980<- pca_1980.1990$svd$U[,2]
    u1_2013<- pca_2013.2023$svd$U[,1]
    u2_2013<- pca_2013.2023$svd$U[,2]

    #Crear dataframe con los dos primeros ejenvectores
    df_ejenvectores<- cbind(lon= datos_1980.1990$lon, lat=datos_1980.1990$lat, u1_1980,u2_1980,u1_2013,u2_2013)

Posterior a esto, se generaron distintos arhivos con formato raster, se genero un archivo para cada ejenvector: 



