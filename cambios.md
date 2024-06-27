# arboles de prediccion de cambios en el manejo forestal 

para realizar este.  analisis, se utilizaron dos bases de datos, la primera siendo la de cambio de uso del suelo, la cual se encuentra disponible en el siguiente enlace: enlace 

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
    
    
    primf_1995 <- raster(filename,varname="primf", band=1146) 
    primn_1995 <- raster(filename,varname="primn", band=1146) 
    secdf_1995 <- raster(filename,varname="secdf", band=1146) 
    secdn_1995 <- raster(filename,varname="secdn", band=1146) 
    urban_1995 <- raster(filename,varname="urban", band=1146) 
    c3ann_1995 <- raster(filename,varname="c3ann", band=1146) 
    c4ann_1995 <- raster(filename,varname="c4ann", band=1146) 
    c3per_1995 <- raster(filename,varname="c3per", band=1146) 
    c4per_1995 <- raster(filename,varname="c4per", band=1146) 
    c3nfx_1995 <- raster(filename,varname="c3nfx", band=1146)
    pastr_1995 <- raster(filename,varname="pastr", band=1146)
    range_1995 <- raster(filename,varname="range", band=1146) 
    
    states1995 <- stack(primf_1995, primn_1995, secdf_1995, secdn_1995, urban_1995, c3ann_1995, c4ann_1995, c3per_1995, c4per_1995, c3nfx_1995, pastr_1995, range_1995)
    names(states1995) <- paste("1995",names_states)
    
    
    primf_2005 <- raster(filename,varname="primf", band=1156) 
    primn_2005 <- raster(filename,varname="primn", band=1156) 
    secdf_2005 <- raster(filename,varname="secdf", band=1156) 
    secdn_2005 <- raster(filename,varname="secdn", band=1156) 
    urban_2005 <- raster(filename,varname="urban", band=1156) 
    c3ann_2005 <- raster(filename,varname="c3ann", band=1156) 
    c4ann_2005 <- raster(filename,varname="c4ann", band=1156)
    c3per_2005 <- raster(filename,varname="c3per", band=1156) 
    c4per_2005 <- raster(filename,varname="c4per", band=1156) 
    c3nfx_2005 <- raster(filename,varname="c3nfx", band=1156) 
    pastr_2005 <- raster(filename,varname="pastr", band=1156) 
    range_2005 <- raster(filename,varname="range", band=1156) 
    
    states2005 <- stack(primf_2005, primn_2005, secdf_2005, secdn_2005, urban_2005, c3ann_2005, c4ann_2005, c3per_2005, c4per_2005, c3nfx_2005, pastr_2005, range_2005)
    names(states2005) <- paste("2005",names_states)
    
    
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
    names(states2015) <- paste("2015",names_states)
    
    
    setwd("C:/Users/Maria/Documents/servicio social/bases de datos/humedad y temp/humedad_root/archivos")
    archivo<- "C:/Users/Maria/Documents/servicio social/bases de datos/states_historic.nc"
    state<- raster(filename,varname="primf", band=1166)
    
    files<- list.files(pattern = ".nc4", full.names = FALSE)
    files.ras <- lapply(files, raster)
    fechas<- substr(files, star=28, stop=33) #extraer fechas
    names(files.ras) <- paste('WROOT',fechas) 
    files.stack <- stack(files.ras)
    
    setwd("C:/Users/Maria/Documents/servicio social/bases de datos/humedad y temp/humedad_root/merra_2015")
    files2015<- list.files(pattern = ".nc4", full.names = FALSE)
    files.ras2015 <- lapply(files2015, raster)
    fechas2015<- substr(files2015, star=28, stop=33) #extraer fechas
    names(files.ras2015) <- paste('WROOT',fechas2015) 
    files.stack2015 <- stack(files.ras2015)
    
    #centroides
    #poly<- rasterToPolygons(ras)
    #poligonos<- st_as_sf(poly)
    #centroides<-sf::st_centroid(poligonos) # VERIFICAR WARNINGS()
    
    #solucion de warnings: El map.gpkg parece estar en WGS84 (EPSG:4326) y es un CRS geométrico; Para que un centroide funcione necesitas datos proyectados.
    #Necesita sf::st_transform() a una proyección diferente; cuál dependerá de sus datos, con la cual no estamos familiarizados, pero elija una con orientación este y norte.
    #Para volver a verificar, considere ejecutar st_is_longlat() en su objeto; siempre que devuelva VERDADERO, el centroide no funcionará.
    
    #st_crs(poligonos)
    #+datum=WGS84 +no_defs -- no definido 
    #st_transform(poligonos, proj4string = "??")
        
    #extraer valores en centroides
    #rasValu<-terra::extract(ras, centroides)
    
    ############# ALTERNATIVA CON RASTER
    # y = as_Spatial(centroides)
    # q<-raster::extract(ras, y)
    
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
         
