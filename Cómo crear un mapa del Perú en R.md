# Como Hacer un Mapa Coroplético

## Introducción

En este tutorial se explica cómo crear un mapa del Perú en R utilizando la técnica de mapa de calor (o mapa coroplético), la cual permite representar visualmente la intensidad de una variable en distintas regiones del país mediante colores. La idea principal es aprender el proceso completo: desde cargar un shapefile hasta integrar datos estadísticos y transformarlos en una visualización clara y útil. Más adelante, este procedimiento se aplicará a un caso específico, donde el mapa será orientado al análisis de una temática en particular, en este caso, la distribución de suicidios por departamento en el Perú a partir de datos reales.

### Obtención de los datos geográficos
Para poder construir el mapa del Perú, es necesario contar con un archivo que contenga los límites de cada departamento. Este tipo de archivo se conoce como shapefile y es ampliamente utilizado en análisis geoespacial. En este caso, el shapefile fue obtenido desde la página de Geo GPS Perú, la cual recopila información geográfica basada en fuentes oficiales. El archivo descargado incluye los límites departamentales del país: https://www.geogpsperu.com/2018/02/limite-departamental-politico-shapefile.html#google_vignette. Es importante mencionar que un shapefile no es un único archivo, sino un conjunto de archivos complementarios (como .shp, .dbf, .shx, entre otros), por lo que todos deben mantenerse en la misma carpeta para su correcto funcionamiento.

### Obtención de los datos estadísticos
Para complementar el mapa, se utilizaron datos abiertos del Ministerio de Salud del Perú, específicamente del sistema SINADEF: https://www.minsa.gob.pe/reunis/?op=1&niv=1&tbl=1
Este portal proporciona información detallada sobre defunciones registradas en el país, incluyendo variables como la causa de muerte, ubicación geográfica y características demográficas. Para este tutorial, se trabajó con los registros asociados a suicidios, lo que permite analizar su distribución a nivel departamental.

### Preparación del entorno de trabajo
Antes de comenzar con el procesamiento de los datos, es necesario instalar y cargar las librerías que permitirán trabajar con información geográfica y generar visualizaciones.
```r
aaa
```r   
