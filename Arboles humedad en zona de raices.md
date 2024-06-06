# Arboles de prediccion de uso de suelo con humedad en zona de raices 

## arboles de 2015 humedad

para este proyecto se utilizaron datos del proyecto MERRA-2 de la nasa disponibles en el vínculo: [https://disc.gsfc.nasa.gov/datasets](https://disc.gsfc.nasa.gov/datasets?page=1). De estos datos, se utilizó la serie de tiempo de 2015 desde el mes de enero hasta el mes de diciembre del mismo año, extraídos como datos de mes con mes. 

Así también se utilizaron datos cambio de uso de suelo desde lo histórico (states), los que están disponibles en: [https://luh.umd.edu](https://luh.umd.edu). En cuanto a estos datos únicamente se extrajo la capa de datos para 2015.

Primeramente, se realizó un manejo de los datos, homologando los datos a partir de los centroides de los datos de MERRA-2. Posterior a esto, se calcularon los promedios por pixel a lo largo de la serie de tiempo, así como la variación y la autocorrelación de los datos a partir de una correlación de tipo (t)-(t-1). 

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

La variable que mejor divide las clases de la cantidad de humedad en zona de raices es la vegetacion secundaria no forestal, si en el pixel predomina esta vegetacion, tanto como entonces la vegetacion secundaria no forestal clasifica al grupo Q1, el cual se define como el de menor humedad. Posterior a esto, el 46% de los datos totales es en los que se cumple lo anterior, de estos datos, si en el pixel se encuentra una proporcion menor a 39e-9 de pastoreo, se encuentra el grupo mas bajo de humedad Q1, por otra parte, si la proporcion de pastoreo es mayor a 39e-9, se clasifica al grupo Q2, el cual tiene una humedad promedio de 0.5, dentro de un rango donde 1 es la humedad maxima del suelo, situacion en la que el suelo se encuentra saturado y 0 siendo la condicion de suelo seco. 

Por otra parte el 54% de los datos, en los cuales la vegetacion secundaria forestal es menor al 165e-24, se clasifica al grupo Q4, el cual es el de mayor humedad, pues su rango va de 0.78798726 a 0.99998690 en terminos de humedad. Asi, si de estos datos, en el pixel se encuentra una proporcion mayor a 0.0013 de vegetacion secundaria dorestal, se encuentra clasificado al grupo Q3, el cual es el segundo grupo con mayor humedad. De esta manera, los pixeles que no cumplen con la condicion de tener una proporcion mayor de 0.0013 de vegetacion secundaria forestal, se claasifican como el grupo Q4, y de estos, los que cuentan con una proporcion mayor a 0.017 de ¿cordillera? se clasifican con el grupo Q1, mientras que los pixeles que no cumplen con esta condicon, se clasifican con el grupo Q4, los cuales se entienden como el 24% de los datos totales. 

Asi, el grupo Q4, abarca un total de 14,719 pixelesm de los cuales, 3,196 cuentan con algun tipo de manejo realizado por el ser humano, entre los que se encuentran las plantaciones de plantas de tipo c3 anuales, c4 anuales, c3 perenes, c4 peresnes, c3 fijadoras de carbono, zonas de pastoreo y de ¿cordillera?, asi como de zonas urbanas

![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_varianzas2015.png)

> Arbol de clasificación con datos de MERRA-2 en 2015, en varianzas de todo el año clasificado en quantiles y states de 2015.


![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_correlacion2015.png)

> Arbol de clasificación con datos de MERRA-2 en 2015, en correlacion de (t)-(t-1) de todo el año clasificado en quantiles y states de 1015.



