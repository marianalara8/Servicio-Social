# Arboles de prediccion de uso de suelo con humedad en zona de raices 

## arboles 1985 states vs 2015 humedad

para este proyecto se utilizaron datos del proyecto MERRA-2 de la nasa disponibles en el vinculo: [https://disc.gsfc.nasa.gov/datasets](https://disc.gsfc.nasa.gov/datasets?page=1)

Asi tambien se utilizaron datos cambio de uso de suelo desde lo historico, los que estan disponibles en: [https://luh.umd.edu](https://luh.umd.edu)

Se utilizo el siguiente codigo para la creacion de los arboles: 

    df_arbol<- read.csv("C:/Users/Maria/Documents/servicio social/bases de datos/humedad y temp/humedad_root/merra_2015/data_arbol.csv")
 
        arbol_medias1985 <- rpart(Quantiles_medias~ 
                        manejo1985 + X1985.primf + X1985.primn + X1985.secdf +
                        X1985.secdn + X1985.urban + X1985.c3ann +  
                        X1985.c4ann + X1985.c3per + X1985.c4per + 
                        X1985.c3nfx + X1985.pastr + X1985.range,
                        data = df_arbol, method = "class")
        rpart.plot(arbol_medias1985)


![]((https://github.com/marianalara8/Servicio-Social/blob/main/Arbol_medias1985.png))

> Follow your heart.
