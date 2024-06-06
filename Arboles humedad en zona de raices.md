# Arboles de prediccion de uso de suelo con humedad en zona de raices 

## arboles de 2015 humedad

para este proyecto se utilizaron datos del proyecto MERRA-2 de la nasa disponibles en el vinculo: [https://disc.gsfc.nasa.gov/datasets](https://disc.gsfc.nasa.gov/datasets?page=1). De estos datos, se utilizo la serie de tiempo de 2015 desde el mes de enero hasta el mes de diciembre del mismo año, extraidos como datos de mes con mes. 

Asi tambien se utilizaron datos cambio de uso de suelo desde lo historico (states), los que estan disponibles en: [https://luh.umd.edu](https://luh.umd.edu). En cuanto a estos datos unicamente se extrajo la capa de datos para 2015.

Primeramente se realizo un manejo de los datos, homologando los datos a partir de los centroides de los datos de MERRA-2. Posterior a esto, se calcularon los promedios por pixel a lo largo de la serie de tiempo, asi como la variacion y la autocorrelacion de los datos a partir de una correlacion de tipo (t)-(t-1). 

Por ultimo, se clasificaron los datos en cuantiles, los cuales se definieron de la siguiente manera: 
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
 
Apartir de esta calsificacion se realizo una base de datos que contenia las coordenadas de los centroides de cada pixel, junto con su respectiva clasificacion de medias, varianza y correlacion, en conjunto con los datos de cada tipo de uso de suelo del año 2015. 

Despues de esto se utilizo el siguiente codigo para la creacion del arbol de clasificacion: 

    df_arbol<- read.csv("merra_2015/data_arbol.csv")
    arbol_medias2015 <- rpart(Quantiles_medias~ 
                            nomanejo2015 + X2015.primf + X2015.primn +                                      X2015.secdf + X2015.secdn + X2015.urban +                                       X2015.c3ann + X2015.c4ann + X2015.c3per +                                       X2015.c4per + X2015.c3nfx + X2015.pastr +                                       X2015.range, data = df_arbol, method = "class")
    rpart.plot(arbol_medias2015)
        







![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_medias2015.png)

> Arbol de clasificacion con datos de MERRA-2 en 2015, en promedios de todo el año clasificado en quantiles y states de 2015.

La variable que mejor divide las clases de la cantidad de humedad en zona de raices es la vegetacion secundaria no forestal, si en el pixel esta la prescencia de esta vegetacion, se encuentra los datos que estan clasificados en el quantil 1, los cuales cuentan con una baja humedad. Posterior a esto estos datos del quantil 1 se dividen a partir de la prescencia o no prescencia de pastoreo en el pixel, pues las zonas que se utilizan para pastoreo se encuentran en los datos del quantil 2, los cuales tienen rangos de humedad de entre 0.7651528  y 0.8232305 en una escala donde 1 es el valor maximo de humedad, donde se encuentra indundado el suelo y 0 es el valor minimo de humedad, donde el suelo se encuentra seco. 

Por otra parte, al dividir los datos a partir de la prescenia o no prescencia de vegetacion secundaria no forestal, la parte que no cuenta con esta vegetacion es principalmente la del quantil 4, el cual es el quantil con mayor humedad, posterior a



![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_varianzas2015.png)

> Arbol de decision con datos de MERRA-2 en 2015, en varianzas de todo el año clasificado en quantiles y states de 2015.


![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_correlacion2015.png)

> Arbol de decision con datos de MERRA-2 en 2015, en correlacion de (t)-(t-1) de todo el año clasificado en quantiles y states de 1015.



