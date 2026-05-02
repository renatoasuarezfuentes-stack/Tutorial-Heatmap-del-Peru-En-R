# Como Hacer un Mapa Coroplético

## Introducción

En este tutorial se explica cómo crear un mapa del Perú en R utilizando la técnica de mapa de calor (o mapa coroplético), la cual permite representar visualmente la intensidad de una variable en distintas regiones del país mediante colores. La idea principal es aprender el proceso completo: desde cargar un shapefile hasta integrar datos estadísticos y transformarlos en una visualización clara y útil. Más adelante, este procedimiento se aplicará a un caso específico, donde el mapa será orientado al análisis de una temática en particular, en este caso, la distribución de suicidios por departamento en el Perú a partir de datos reales.

### Obtención de los datos geográficos
Para poder construir el mapa del Perú, es necesario contar con un archivo que contenga los límites de cada departamento. Este tipo de archivo se conoce como shapefile y es ampliamente utilizado en análisis geoespacial. En este caso, el shapefile fue obtenido desde la página de Geo GPS Perú, la cual recopila información geográfica basada en fuentes oficiales. El archivo descargado incluye los límites departamentales del país: https://www.geogpsperu.com/2018/02/limite-departamental-politico-shapefile.html#google_vignette. 
