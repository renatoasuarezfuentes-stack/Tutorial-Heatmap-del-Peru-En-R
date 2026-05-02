# Como Hacer un Mapa Coroplético

## Introducción

En este tutorial se explica cómo crear un mapa del Perú en R utilizando la técnica de mapa de calor (o mapa coroplético), la cual permite representar visualmente la intensidad de una variable en distintas regiones del país mediante colores. La idea principal es aprender el proceso completo: desde cargar un shapefile hasta integrar datos estadísticos y transformarlos en una visualización clara y útil. Más adelante, este procedimiento se aplicará a un caso específico, donde el mapa será orientado al análisis de una temática en particular, en este caso, la distribución de suicidios por departamento en el Perú a partir de datos reales.

### Obtención de los datos geográficos
Para poder construir el mapa del Perú, es necesario contar con un archivo que contenga los límites de cada departamento. Este tipo de archivo se conoce como shapefile y es ampliamente utilizado en análisis geoespacial. En este caso, el shapefile fue obtenido desde la página de Geo GPS Perú, la cual recopila información geográfica basada en fuentes oficiales. El archivo descargado incluye los límites departamentales del país: https://www.geogpsperu.com/2018/02/limite-departamental-politico-shapefile.html#google_vignette. Es importante mencionar que un shapefile no es un único archivo, sino un conjunto de archivos complementarios (como .shp, .dbf, .shx, entre otros), por lo que todos deben mantenerse en la misma carpeta para su correcto funcionamiento.

### Obtención de los datos estadísticos
Para complementar el mapa, se utilizaron datos abiertos del Ministerio de Salud del Perú, específicamente del sistema SINADEF: https://www.minsa.gob.pe/reunis/?op=1&niv=1&tbl=1
Este portal proporciona información detallada sobre defunciones registradas en el país, incluyendo variables como la causa de muerte, ubicación geográfica y características demográficas. Para este tutorial, se trabajó con los registros asociados a suicidios, lo que permite analizar su distribución a nivel departamental.

Para fines prácticos dentro de los archivos de repositorio se encuentra el dataset limpio y filtrado.

### Preparación del entorno de trabajo
Antes de comenzar con el procesamiento de los datos, es necesario instalar y cargar las librerías que permitirán trabajar con información geográfica y generar visualizaciones.
```r
#install.packages(c("sf", "ggplot2", "viridis", "dplyr", "scales","ggrepel"),
                 #repos = "https://cran.r-project.org")
library(ggplot2)
library(sf)
library(scales)
library(dplyr)

peru_dep <- st_read("C:/Users/Renato/MAPA/INEI_LIMITE_DEPARTAMENTAL_GEOGPSPERU_JUANSUYO_931381206.shp")
```
Impotante: En caso de no contar con las librerías instaladas, quitar el símbolo de comentario(#) al momento de ejecutar el código en R, por el contrario mantenerlo.

### Carga del shapefile
Una vez descargado el shapefile, se procede a cargarlo en R utilizando la librería sf.
```r
peru_dep <- st_read("ruta/del/shapefile.shp")
```

### Carga de la base de datos
A continuación, se importa la base de datos descargada desde el portal del Ministerio de Salud.
```r
datos <- read.csv("ruta/datos.csv")
```
Este conjunto de datos será la base para el análisis que se representará en el mapa.

### Preparación y limpieza de los datos
Antes de trabajar con la información, es importante estandarizar los textos para evitar errores al momento de agrupar o unir datos, pero todo este preoceso depende de tu dataset y lo que busques representar con tu mapa de calor.
```r
datos <- datos %>%
  mutate(
    across(starts_with("CAUSA_"), ~ toupper(trimws(.))),
    MUERTE_VIOLENTA = toupper(trimws(MUERTE_VIOLENTA)),
    DEPARTAMENTO_FALLECIMIENTO = toupper(trimws(DEPARTAMENTO_FALLECIMIENTO))
  )
```
Este paso permite que todos los valores tengan el mismo formato, evitando problemas como diferencias entre mayúsculas, minúsculas o espacios adicionales.

### Identificación de los casos de suicidio 
Para identificar correctamente los casos de suicidio, se utilizaron los códigos CIE-10 (X60–X84), los cuales corresponden a lesiones autoinfligidas intencionalmente. En lugar de depender únicamente de una variable, se revisan todas las posibles causas registradas en la base de datos.
```r
suicidios <- datos %>%
  mutate(
    suicidio = grepl("^X6[0-9]|^X7[0-9]|^X8[0-4]", CAUSA_A_CIEX) |
               grepl("^X6[0-9]|^X7[0-9]|^X8[0-4]", CAUSA_B_CIEX) |
               grepl("^X6[0-9]|^X7[0-9]|^X8[0-4]", CAUSA_C_CIEX) |
               grepl("^X6[0-9]|^X7[0-9]|^X8[0-4]", CAUSA_D_CIEX) |
               grepl("^X6[0-9]|^X7[0-9]|^X8[0-4]", CAUSA_E_CIEX) |
               grepl("^X6[0-9]|^X7[0-9]|^X8[0-4]", CAUSA_F_CIEX) |
               MUERTE_VIOLENTA == "SUICIDIO"
  ) %>%
  filter(suicidio)
```
Este enfoque permite capturar una mayor cantidad de registros y evita perder casos relevantes.

### Agrupación de la información
Una vez filtrados los datos, se agrupan por departamento para obtener el total de casos.
```r
DF <- suicidios %>%
  group_by(DEPARTAMENTO_FALLECIMIENTO) %>%
  summarise(total = n(), .groups = "drop") %>%
  mutate(
    DEPARTAMENTO_FALLECIMIENTO = ifelse(
      DEPARTAMENTO_FALLECIMIENTO == "LIMA METROPOLITANA",
      "LIMA",
      DEPARTAMENTO_FALLECIMIENTO
    )
  )
```
### Integración de datos con el mapa
Ahora se procede a unir los datos estadísticos con el shapefile, utilizando el nombre del departamento como clave.
```r
mapa <- peru_dep %>%
  mutate(NOMBDEP = toupper(trimws(NOMBDEP))) %>%
  left_join(DF, by = c("NOMBDEP" = "DEPARTAMENTO_FALLECIMIENTO"))

mapa$total[is.na(mapa$total)] <- 0
```
### Construcción del mapa de calor
Finalmente, se genera la visualización utilizando ggplot2.
```r
ggplot(mapa) +
  geom_sf(aes(fill = total), color = "white", linewidth = 0.3) +
  scale_fill_gradientn(
    colours = c("#FFF7EC", "#FDD49E", "#FC8D59", "#B30000"),
    name = "Casos"
  ) +
  labs(
    title = "Mortalidad por suicidio en el Perú",
    subtitle = "Distribución por departamento",
    caption = "Fuente: SINADEF - MINSA"
  ) +
  theme_void()
```
El resultado es un mapa de calor donde la intensidad del color representa la cantidad de casos en cada departamento.

### Interpretación del mapa y Cierre

El mapa permite identificar patrones espaciales en la distribución de los casos.mLas zonas con colores más intensos indican una mayor concentración, mientras que los tonos más claros representan menor cantidad de registros.

Este procedimiento muestra cómo integrar datos geográficos y estadísticos en R para generar visualizaciones claras y útiles. Además, esta misma metodología puede aplicarse a otros temas, como resultados electorales u otros indicadores sociales.

Además, como parte del desarrollo del trabajo, se ha elaborado un documento en formato Quarto (.qmd), en el cual se incluye todo el proceso completo: carga de datos, limpieza, análisis y visualización.

Este archivo se encuentra dentro del repositorio y permite:

- Ejecutar nuevamente todo el análisis
- Ver el código junto con sus resultados
- Generar un reporte en formato HTML o PDF

Asegurando así la reproducibilidad del trabajo y facilita su revisión.
