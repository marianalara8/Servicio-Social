# Arboles de prediccion de uso de suelo con humedad en zona de raices 

## arboles 1985 states vs 2015 humedad

para este proyecto se utilizaron datos del proyecto MERRA-2 de la nasa disponibles en el vinculo: [https://disc.gsfc.nasa.gov/datasets](https://disc.gsfc.nasa.gov/datasets?page=1)

Asi tambien se utilizaron datos cambio de uso de suelo desde lo historico (states), los que estan disponibles en: [https://luh.umd.edu](https://luh.umd.edu)

Se utilizo el siguiente codigo para la creacion de los arboles: 

    df_arbol<- read.csv("C:/Users/Maria/Documents/servicio social/bases de datos/humedad y temp/humedad_root/merra_2015/data_arbol.csv")
 
        arbol_medias1985 <- rpart(Quantiles_medias~ 
                        manejo1985 + X1985.primf + X1985.primn + X1985.secdf +
                        X1985.secdn + X1985.urban + X1985.c3ann +  
                        X1985.c4ann + X1985.c3per + X1985.c4per + 
                        X1985.c3nfx + X1985.pastr + X1985.range,
                        data = df_arbol, method = "class")
        rpart.plot(arbol_medias1985)


![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_medias1985.png)

> Arbol de decision con datos de MERRA-2 en 2015, en promedios de todo el año clasificado en quantiles y states de 1985.


![](https://github.com/marianalara8/Servicio-Social/blob/main/arbol_varianzas1985.png)

> Arbol de decision con datos de MERRA-2 en 2015, en varianzas de todo el año clasificado en quantiles y states de 1985.


![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_correlacion1985.png)

> Arbol de decision con datos de MERRA-2 en 2015, en correlacion de (t)-(t-1) de todo el año clasificado en quantiles y states de 1985.



![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_medias2015.png)

> Arbol de clasificacion con datos de MERRA-2 en 2015, en promedios de todo el año clasificado en quantiles y states de 2015.

La variable que mejor divide las clases de la cantidad de humedad en zona de raices es la vegetacion secundaria no forestal, si en el pixel esta la prescencia de esta vegetacion, se encuentra los datos que estan clasificados en el quantil 1, los cuales cuentan con una baja humedad. Posterior a esto estos datos del quantil 1 se dividen a partir de la prescencia o no prescencia de pastoreo en el pixel, pues las zonas que se utilizan para pastoreo se encuentran en los datos del quantil 2, los cuales tienen rangos de humedad de entre 0.7651528  y 0.8232305 en una escala donde 1 es el valor maximo de humedad, donde se encuentra indundado el suelo y 0 es el valor minimo de humedad, donde el suelo se encuentra seco. 

Por otra parte, al dividir los datos a partir de la prescenia o no prescencia de vegetacion secundaria no forestal, la parte que no cuenta con esta vegetacion es principalmente la del quantil 4, el cual es el quantil con mayor humedad, posterior a



![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_varianzas2015.png)

> Arbol de decision con datos de MERRA-2 en 2015, en varianzas de todo el año clasificado en quantiles y states de 2015.


![](https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_correlacion2015.png)

> Arbol de decision con datos de MERRA-2 en 2015, en correlacion de (t)-(t-1) de todo el año clasificado en quantiles y states de 1015.



