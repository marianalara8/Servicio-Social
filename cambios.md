# arboles de prediccion de cambios en el manejo forestal 

para este proyecto se utilizaron datos del proyecto MERRA-2 de la NASA disponibles en el vínculo: https://disc.gsfc.nasa.gov/datasets. En estos datos, se tiene disponible informacion de 1890 a 2024, de sistintas variables entre las cuales se encuentra la tasa de humedad en la zona de raices por pixel. Asi, se realizo una mineria de datos, obteniendo unicamente la serie de tiempo correspondiente al año 2015, con datos desde el mes de enero hasta el mes de diciembre del mismo año, extraídos como datos de mes con mes.

Así también se utilizaron datos cambio de uso de suelo desde lo histórico (states), los que están disponibles en: https://luh.umd.edu. Lo que proporciona esta base de datos es un recuento historico de los usos del suelo desde el año 850, hasta el año de 2015. En cuanto a estos datos se minaron la capa de datos correspondientes a los años 2015 y 1985.

Primeramente se homologaron los datos con respecto a las coordenadas de los centroides de los pixeles de la base de datos de cambio de uso del suelo, para esto se utilizaron las paqueteria raster de R. Se realizo de la siguiente manera:

    require(ncdf4)
    library(raster)
    library(fields)
    library(sf)
    library(ggplot2)
    library(viridis)
    library(rpart)
    library(rpart.plot)
    
    filename <- "C:/Users/Maria/Documents/servicio social/bases de datos/states_historic.nc"
    states_nc <- nc_open(filename)
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

    archivo<- "C:/Users/Maria/Documents/servicio social/bases de datos/states_historic.nc"
    state<- raster(filename,varname="primf", band = 1166)
    
    setwd("C:/Users/Maria/Documents/servicio social/bases de datos/humedad y temp/humedad_root/merra_2015")
    files2015<- list.files(pattern = ".nc4", full.names = FALSE)
    files.ras2015 <- lapply(files2015, raster)
    fechas2015<- substr(files2015, star=28, stop=33) #extraer fechas
    names(files.ras2015) <- paste('WROOT',fechas2015) 
    files.stack2015 <- stack(files.ras2015)
    
   

    # Transform into polygon
    poly = rasterToPolygons(state) 
    # Add centroids to database
    centroids <- getSpPPolygonsLabptSlots(poly) #centroides de states
    # save it into GeoJSON of merra
    merra = raster::extract(files.stack, centroids)
    merra2015 =raster::extract(files.stack2015, centroids)
    #Extract of states
    land1 =raster::extract(states1985, centroids)
    land2 =raster::extract(states1995, centroids)
    land3 =raster::extract(states2005, centroids)
    land4 =raster::extract(states2015, centroids)
    
    #matriz
    
    data_merra<- cbind(lon=centroids[,1], lat=centroids[,2], merra)#merra wroot humerdad en raices
    data_merra2015 <- cbind(lon=centroids[,1], lat=centroids[,2], merra2015 )
    data_states<- cbind(lon=centroids[,1], lat=centroids[,2], land1, land2, land3, land4)
    write.csv(data_states, "data_states.csv")
    
    #trabajo con la database merra
    
    M= NULL
    V= NULL
    C= NULL
    n= length(data_merra[,1]) 
    
    for(i in 1:n){
      M[i]= mean(data_merra[i,3:532]) 
      V[i]= var(data_merra[i,3:532])
      C[i]= cor(x=data_merra[i,3:531], y=data_merra[i,4:532])
      print(i)
    }
    
    Y <- cbind(lon=centroids[,1], lat=centroids[,2], M,V,C)
    write.csv(Y, "Y_serie_completa.csv")
    
     
    medias <- cbind(lon=centroids[,1], lat=centroids[,2], M)
    medias_ras= rasterFromXYZ(medias)
    plot(medias_ras)
    #writeRaster(medias_ras,'medias.tif')
    
    varianzas <- cbind(lon=centroids[,1], lat=centroids[,2], V)
    varianzas_ras= rasterFromXYZ(varianzas)
    plot(varianzas_ras)
    #writeRaster(varianzas_ras,'varianzas.tif', overwrite=TRUE)
    
    correlacion <- cbind(lon=centroids[,1], lat=centroids[,2], C)
    correlacion_ras = rasterFromXYZ(correlacion)
    plot(correlacion_ras)
    #writeRaster(correlacion_ras,'correlacion.tif')
    
    #trabajo con la database merra 2015
    
    M= NULL
    V= NULL
    C= NULL
    n= length(data_merra2015[,1]) 
    
    for(i in 1:n){
      M[i]= mean(data_merra2015[i,3:14]) 
      V[i]= var(data_merra2015[i,3:14])
      C[i]= cor(x=data_merra2015[i,3:13], y=data_merra2015[i,4:14])
      print(i)
    }
    
    
    quanti <- function(df){
      Q<-rep(0, length(df)) 
      q<- quantile(df, prob=c(0,0.25,0.5,0.75,1), na.rm=TRUE)
      print(q)
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
      return(Q)
      }
    
    #Quantiles_medias<-as.integer(cut(M, quantile(M), include.lowest= T))
    Quantiles_medias <- quanti(M)
    Quantiles_varianza <- quanti(V)
    Quantiles_correlacion <- quanti(C)
    
    nomanejo1985 <- land1[,1]+land1[,2] + land1[,3] +land1[,4]
    nomanejo2015 <- land4[,1]+land4[,2] + land4[,3] +land4[,4]
    
    cambio_manejo= nomanejo1985 - nomanejo2015
    Quantiles_cambio <- quanti(cambio_manejo)
    
    data_arbol_cambio<- as.data.frame(cbind(lon=centroids[,1], lat=centroids[,2], Quantiles_cambio, 
                           cambio_manejo,medias_merra=M,  land1, land4))
    write.csv(data_arbol_cambio, "data_arbo_cambio.csv")
    

### codigo de arboles

    library(rpart)
    library(rpart.plot)
    
    data_arbol<- read.csv("C:/Users/Maria/Documents/servicio social/bases de datos/humedad y temp/humedad_root/merra_2015/data_arbo_cambio.csv")
    
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
         
