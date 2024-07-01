# arboles de prediccion de cambios en el manejo forestal 

para este proyecto se utilizaron datos del proyecto MERRA-2 de la NASA disponibles en el vínculo: https://disc.gsfc.nasa.gov/datasets. En estos datos, se tiene disponible informacion de 1890 a 2024, de sistintas variables entre las cuales se encuentra la tasa de humedad en la zona de raices por pixel. Asi, se realizo una mineria de datos, obteniendo unicamente la serie de tiempo correspondiente al año 2015, con datos desde el mes de enero hasta el mes de diciembre del mismo año, extraídos como datos de mes con mes.

Así también se utilizaron datos cambio de uso de suelo desde lo histórico (states), los cuales están disponibles en: https://luh.umd.edu. Lo que proporciona esta base de datos es un recuento historico de los usos del suelo desde el año 850, hasta el año de 2015. En cuanto a estos datos se minaron las capas de datos correspondientes a los años 2015 y 1985.

Para este proyecto, primeramente se homologaron los datos con respecto a las coordenadas de los centroides de los pixeles de la base de datos de cambio de uso del suelo, para esto se utilizaron las paqueteria raster de R. Se realizo de la siguiente manera:

    require(ncdf4)
    library(raster)
    library(fields)
    library(sf)
    library(rpart)
    library(rpart.plot)
    
    #Cargar los datos:
    filename <- "~/states_historic.nc"
    names_states<-c("primf", "primn", "secdf", "secdn", "urban", "c3ann", "c4ann", "c3per", "c4per", "c3nfx", "pastr", "range")
    
    primf_1985 <- raster(filename,varname="primf", band=1136) 
    primn_1985 <- raster(filename,varname="primn", band=1136) 
    secdf_1985 <- raster(filename,varname="secdf", band=1136) 
    secdn_1985 <- raster(filename,varname="secdn", band=1136) 
    urban_1985 <- raster(filename,varname="urban", band=1136) 
    c3ann_1985 <- raster(filename,varname="c3ann", band=1136) 
    c4ann_1985 <- raster(filename,varname="c4ann", band=1136) 
    c3per_1985 <- raster(filename,varname="c3per", band=1136) 
    c4per_1985 <- raster(filename,varname="c4per", band=1136) 
    c3nfx_1985 <- raster(filename,varname="c3nfx", band=1136)
    pastr_1985 <- raster(filename,varname="pastr", band=1136)
    range_1985 <- raster(filename,varname="range", band=1136) 
    
    states1985 <- stack(primf_1985, primn_1985, secdf_1985, secdn_1985, urban_1985, c3ann_1985, c4ann_1985, c3per_1985, c4per_1985, c3nfx_1985, pastr_1985, range_1985)
    names(states1985) <- paste("1985",names_states)
    
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
    
    states2015 <- stack(primf_2015, primn_2015, secdf_2015, secdn_2015, urban_2015, c3ann_2015, c4ann_2015, c3per_2015, c4per_2015, c3nfx_2015, pastr_2015, range_2015)
    names(states2015) 

    archivo<- "~/states_historic.nc"
    state<- raster(filename,varname="primf", band = 1166)
    
    setwd("C:/Users/Maria/Documents/servicio social/bases de datos/humedad y temp/humedad_root/merra_2015")
    files2015<- list.files(pattern = ".nc4", full.names = FALSE)
    files.ras2015 <- lapply(files2015, raster)
    fechas2015<- substr(files2015, star=28, stop=33) #extraer fechas
    names(files.ras2015) <- paste('WROOT',fechas2015) 
    files.stack2015 <- stack(files.ras2015)

    #obtencion de centroides de states
    # Transformar en poligono 
    poly = rasterToPolygons(state) 
    # guardar los centroides en una base de datos
    centroids <- getSpPPolygonsLabptSlots(poly) 
    # extraer datos de merra en los centroides
    merra2015 =raster::extract(files.stack2015, centroids)
    #Extraer datos de states
    land1 =raster::extract(states1985, centroids)
    land2 =raster::extract(states2015, centroids)

Despues de extraer los datos, se obtuvu una base de datos para poder manipular 
    # creacion de matriz para trabajar
    data_merra2015 <- cbind(lon=centroids[,1], lat=centroids[,2], merra2015)
    data_states<- cbind(lon=centroids[,1], lat=centroids[,2], land1, land2)

    #trabajo con la database merra 2015
    M= NULL
    n= length(data_merra2015[,1]) 
    for(i in 1:n){
      M[i]= mean(data_merra2015[i,3:14]) 
    }

    #funcion que asigna cada dato dentro de un cuantil 
    quanti <- function(df){
      Q<-rep(0, length(df)) 
      q<- quantile(df, prob=c(0,0.25,0.5,0.75,1), na.rm=TRUE)
      i=1
      
      for (i in 1:length(df)) {
        if (is.na(df[i])){
          Q[i] = NA
        }
        else if (df[i]>= q[[1]] & df[i]<=q[[2]]) {
          Q[i] ='q1'
        }
        
        else if ( df[i]>q[[2]] & df[i]<=q[[3]] ){
          Q[i] ='q2'
        }
        else if ( df[i]>q[[3]] & df[i]<=q[[4]] ){
        Q[i] ='q3'
        }
        else if( df[i]>q[[4]] & df[i]<=q[[5]] ){
        Q[i] ='q4'
        }
      }
      }

Posterior a esto se asigno la variable no menejo, la cual engloba los usos de suelo forestales y no forestares primario y secundarios. Posterior a esto se realizo una resta por pixel, nombrandolo cambio en el manejo, este cambio puede presentarse positivo o negativo. En caso de encontrarse un valor positivo, significa que se gano cierta proporcion del pixel con cobertura vegetal, del mismo modo, los valores negativos, indican la proporcion de pardida de vegetacion en el pixel. 

    #clases de no manejo 
    nomanejo1985 <- land1[,1] + land1[,2] + land1[,3] + land [,4]
    nomanejo2015 <- land2[,1] + land2[,2] + land2[,3] + land2[,4]
    
    cambio_manejo= nomanejo1985 - nomanejo2015
    #los pixeles se clasificaron en cuantiles. 
    Quantiles_cambio <- quanti(cambio_manejo)
    
    data_arbol<- as.data.frame(cbind(lon=centroids[,1], lat=centroids[,2], Quantiles_cambio, 
                           cambio_manejo,medias_merra=M,  land1, land4))

### codigo de arboles

para la creacion de ar

    library(rpart)
    library(rpart.plot)
    
    arbol_cambios1985 <- rpart(Quantiles_cambio~ 
                                 X1985.primf + X1985.primn + X1985.secdf +
                                 X1985.secdn + X1985.urban + X1985.c3ann +  
                                 X1985.c4ann + X1985.c3per + X1985.c4per + 
                                 X1985.c3nfx + X1985.pastr + X1985.range,
                               data = data_arbol, method = "class")
    rpart.plot(arbol_cambios1985)
    
    arbol_cambios2015 <- rpart(Quantiles_cambio~ 
                                   X2015.primf + X2015.primn + X2015.secdf +
                                   X2015.secdn + X2015.urban + X2015.c3ann +  
                                   X2015.c4ann + X2015.c3per + X2015.c4per + 
                                   X2015.c3nfx + X2015.pastr + X2015.range, 
                                 data = data_arbol)
    rpart.plot(arbol_cambios2015)
    
    cambios_merra <- rpart(Quantiles_cambio ~ medias_merra,
                           data=data_arbol)
    rpart.plot(cambios_merra)
         


![](https://github.com/marianalara8/Servicio-Social/blob/main/cambio_1985.png)

> Arbol de clasificacion con datos de MERRA-2 en 2015, en promedios de todo el año clasificado en quantiles y states de 2015.


![](https://github.com/marianalara8/Servicio-Social/blob/main/cambios2015.png)

> Arbol de clasificacion con datos de MERRA-2 en 2015, en promedios de todo el año clasificado en quantiles y states de 2015.

![](https://github.com/marianalara8/Servicio-Social/blob/main/cambio_merra.png)

> Arbol de clasificacion con datos de MERRA-2 en 2015, en promedios de todo el año clasificado en quantiles y states de 2015.





