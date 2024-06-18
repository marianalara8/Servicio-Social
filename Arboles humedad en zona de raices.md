# Arboles de prediccion de uso de suelo con humedad en zona de raices 

## arboles de 2015 humedad

para este proyecto se utilizaron datos del proyecto MERRA-2 de la NASA disponibles en el vínculo: [https://disc.gsfc.nasa.gov/datasets](https://disc.gsfc.nasa.gov/datasets?page=1). En estos datos, se tiene disponible informacion de 1890 a 2024, de sistintas variables entre las cuales se encuentra la tasa de humedad en la zona de raices por pixel. Asi, se realizo una mineria de datos, obteniendo unicamente la serie de tiempo correspondiente al año 2015, con datos desde el mes de enero hasta el mes de diciembre del mismo año, extraídos como datos de mes con mes. 

Así también se utilizaron datos cambio de uso de suelo desde lo histórico (states), los que están disponibles en: [https://luh.umd.edu](https://luh.umd.edu). Lo que proporciona esta base de datos es un recuento historico de los usos del suelo desde el año 850, hasta el año de 2015. En cuanto a estos datos únicamente se minó la capa de datos correspondientes al año 2015.

Primeramente se homologaron los datos con respecto a las coordenadas de los centroides de los pixeles de la base de datos de humedad, para esto se utilizaron las paqueteria raster de R. Se realizo de la siguiente manera:

   Asignación de nombre al archivo
   filename <- "~/states_historic.nc" 
   names_states<-c("primf", "primn", "secdf", "secdn", "urban", "c3ann", "c4ann", "c3per", "c4per", "c3nfx", "pastr", "range")
   #Abrir cada capa por variable de uso de suelo para la banda 1166, correspondiente al año 2015
   primf_2015 <- raster(filename,varname="primf", band=1166)
   primn_2015 <- raster(filename,varname="primn", band=1166)
   secdf_2015 <- raster(filename,varname="secdf", band=1166) 
   secdn_2015 <- raster(filename,varname="secdn", band=1166)
   urban_2015 <- raster(filename,varname="urban", band=1166) 
   c3ann_2015 <- raster(filename,varname="c3ann", band=1166) 
   c4ann_2015 <- raster(filename,varname="c4ann", band=1166) 
   c3per_2015 <- raster(filename,varname="c3per", band=1166) 
   c4per_2015 <- raster(filename,varname="c4per", band=1166) 
   c3nfx_2015 <- raster(filename,varname="c3nfx", band=1166) 
   pastr_2015 <- raster(filename,varname="pastr", band=1166) 
   range_2015 <- raster(filename,varname="range", band=1166) 
   
   # Creacion de rasteer multivariable
   states2015 <- stack(primf_2015, primn_2015, secdf_2015, secdn_2015,             urban_2015, c3ann_2015, c4ann_2015, c3per_2015, c4per_2015, c3nfx_2015,         pastr_2015, range_2015)
   # Asignar nombre a cada variable
   names(states2015) <- paste("2015",names_states)
   
   # Abrir archivo ejemplo de humedad en zona de raices para obtener centroides
   archivo<- "~/MERRA2_400.tavgM_2d_lnd_Nx.201501.nc4.nc4"
   ras<- raster(archivo)


Primeramente, se realizo una vista minable, en la cual se homologaron los datos a partir de los centroides de los datos de MERRA-2. Posterior a esto, se calcularon los promedios por pixel a lo largo de la serie de tiempo, así como la variación y la autocorrelación de los datos a partir de una correlación de tipo (t)-(t-1). Lo cual se presenta en el siguiente codigo: 





Por último, se clasificaron los datos en cuantiles, los cuales se definieron de la siguiente manera: 
- Cuantiles de medias:
    - q1 (0.08615126 - 0.45906625)
    - q2 (0.45906625 - 0.61188439)
    - q3 (0.61188439 - 0.78798726)
    - q4 (0.78798726 - 0.99998690)
- Cuantiles de la varianza:
    - q1 (7.567547e-11 - 6.917665e-04)
    - q2 (6.917665e-04 - 2.102787e-03)
    - q3 (2.102787e-03 - 5.823124e-03)
    - q4 (5.823124e-03 - 7.610146e-02)
- Cuantiles de correlacion
    - q1 (-0.8201559 - 0.5326473)
    - q2 (0.5326473 - 0.6967764)
    - q3 (0.6967764 - 0.8093142)
    - q4 (0.8093142 - 0.9980967)
 
A partir de esta clasificación se realizó una base de datos que contiene las coordenadas de los centroides de cada pixel, junto con su respectiva clasificación de medias, varianza y correlación, en conjunto con los datos de cada tipo de uso de suelo del año 2015. Además de una última variable que es si no existe manejo antropogénico en el pixel, la cual es una suma de los usos de suelo; Vegetación primaria forestal, vegetación primaria no forestal, vegetación secundaria forestal y vegetación secundaria no forestal.  

Después de esto se utilizó el siguiente código para la creación del árbol de clasificación:
   
    df_arbol<- read.csv("merra_2015/data_arbol.csv")
    arbol_medias2015 <- rpart(Quantiles_medias~ 
                            nomanejo2015 + X2015.primf + X2015.primn +                                      X2015.secdf + X2015.secdn + X2015.urban +                                       X2015.c3ann + X2015.c4ann + X2015.c3per +                                       X2015.c4per + X2015.c3nfx + X2015.pastr +                                       X2015.range, data = df_arbol, method = "class")
    rpart.plot(arbol_medias2015)
        
![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_medias2015.png)

> Arbol de clasificacion con datos de MERRA-2 en 2015, en promedios de todo el año clasificado en quantiles y states de 2015.

La variable que mejor divide las clases de la cantidad de humedad en zona de raíces es la vegetación secundaria no forestal, si en el píxel predomina esta vegetación, tanto como entonces la vegetación secundaria no forestal clasifica al grupo Q1, el cual se define como el de menor humedad. Posterior a esto, el 46% de los datos totales es en los que se cumple lo anterior, de estos datos, si en el pixel se encuentra una proporción menor a 39e-9 de pastoreo, se encuentra el grupo más bajo de humedad Q1, por otra parte, si la proporción de pastoreo es mayor a 39e-9, se clasifica al grupo Q2, el cual tiene una humedad promedio de 0.5, dentro de un rango donde 1 es la humedad máxima del suelo, situación en la que el suelo se encuentra saturado y 0 siendo la condición de suelo seco. 

Por otra parte, el 54% de los datos, en los cuales la vegetación secundaria forestal es menor al 165e-24, se clasifica al grupo Q4, el cual es el de mayor humedad, pues su rango va de 0.78798726 a 0.99998690 en términos de humedad. Así, si de estos datos, en el píxel se encuentra una proporción mayor a 0.0013 de vegetación secundaria forestal, se encuentra clasificado al grupo Q3, el cual es el segundo grupo con mayor humedad. De esta manera, los pixeles que no cumplen con la condición de tener una proporción mayor de 0.0013 de vegetación secundaria forestal, se clasifican como el grupo Q4, y de estos, los que cuentan con una proporción mayor a 0.017 de ¿cordillera? se clasifican con el grupo Q1, mientras que los pixeles que no cumplen con esta condición se clasifican con el grupo Q4, los cuales se entienden como el 24% de los datos totales. 

Así, el grupo Q4, abarca un total de 14,719 pixeles de los cuales, 3,196 cuentan con algún tipo de manejo realizado por el ser humano, entre los que se encuentran las plantaciones de plantas de tipo c3 anuales, c4 anuales, c3 perennes, c4 perennes, c3 fijadoras de carbono, zonas de pastoreo y de ¿cordillera?, así como de zonas urbanas


![](https://github.com/marianalara8/Servicio-Social/blob/main/imagen_2024-06-06_023113723.png)
> Mapa de distribucion de los datos de medias de la humedad en zona de raices

![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_varianzas2015.png)

> Arbol de clasificación con datos de MERRA-2 en 2015, en varianzas de todo el año clasificado en quantiles y states de 2015.


![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_correlacion2015.png)

> Arbol de clasificación con datos de MERRA-2 en 2015, en correlacion de (t)-(t-1) de todo el año clasificado en quantiles y states de 1015.



