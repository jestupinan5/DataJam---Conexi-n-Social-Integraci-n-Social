# DataJam - Conexión Social - Bogotá me conecta

Análisis espacial del programa distrital **Conexión Social - Bogotá me conecta** (SDIS)
frente a la **pobreza multidimensional** de Bogotá, desarrollado para la
**Bogotá Datajam: Uso y Aprovechamiento de Datos (Edición 2) - 2026**.

## Pregunta de análisis

¿Las instalaciones del programa Conexión Social están llegando efectivamente a las
zonas de Bogotá con mayores niveles de pobreza multidimensional?

## Contenido del repositorio

```
notebooks/
  01_analisis_espacial_conexion_social.ipynb   <- notebook principal
data/geo/
  localidad.geojson, upz.geojson,
  sector_catastral.geojson                     <- límites oficiales (públicos, IDECA / Catastro Bogotá)
  sector_catastral_hogares_cs.geojson           <- sector catastral + conteo agregado de hogares CS
outputs/tables/
  cruce_localidad_pobreza_conexion_social.csv
  cruce_upz_pobreza_conexion_social.csv
  hogares_cs_por_sector_catastral.csv
outputs/figures/
  dispersion_pobreza_vs_cobertura_localidad.png
  mapa_pobreza_vs_cobertura_localidad.png
  mapa_ipm_con_hogares_cs.png                   <- IPM + ubicacion exacta de cada hogar CS
  mapa_hogares_cs_sector_catastral.png
outputs/powerbi/
  bd_conexion_social_powerbi.xlsx               <- base de datos agregada (5 tablas) para Power BI
  bd_conexion_social_powerbi.db                 <- misma base en SQLite
```

> **Nota:** `mapa_ipm_con_hogares_cs.png` grafica la coordenada exacta de los 15.465
> hogares instalados (un punto por hogar). Se incluye por decisión explícita del
> equipo; si se va a compartir fuera del equipo, considerar reemplazarlo por una
> versión de densidad (mapa de calor) en vez de puntos individuales.

## Fuentes de datos

| Fuente | Nivel | ¿Está en este repositorio? |
|---|---|---|
| Instalaciones Conexión Social (`Perfiles_CS.xlsx`, SDIS) | Hogar | **No** (dirección y coordenadas de hogares - dato sensible) |
| Encuesta Multipropósito de Bogotá 2021 (`em2021.csv` + variables adicionales, SDP/DANE) | Persona/hogar | **No** (microdato) |
| Límites de Localidad, UPZ y Sector Catastral | Polígono | Sí (`data/geo/`, información pública de IDECA / Catastro Bogotá) |

Los archivos con datos personales quedan **solo en el computador de cada integrante**
(ver `.gitignore`). A este repositorio solo se suben el notebook y los **resultados
agregados** por localidad, UPZ y sector catastral - ninguna fila corresponde a un hogar
individual.

## Cómo correr el notebook

1. Colocar en la carpeta raíz de este proyecto (junto al `.gitignore`) los archivos
   originales: `Perfiles_CS.xlsx`, `em2021.csv`, `20240430_variables_adicionales_2021.csv`.
2. Instalar dependencias: `pip install geopandas pyogrio pyproj shapely mapclassify jupyter openpyxl`.
3. Ejecutar `notebooks/01_analisis_espacial_conexion_social.ipynb` de principio a fin.
   La primera vez descarga los límites geográficos oficiales desde los servicios
   públicos de Catastro Bogotá / IDECA y los guarda en `data/geo/` para no repetir la
   descarga.

## Hallazgo principal (nivel localidad)

Correlación de Spearman entre % de pobreza multidimensional (IPM, EMB 2021) y
cobertura de Conexión Social (hogares instalados por cada 1.000 hogares): **0.33** a
nivel de localidad y **0.57** a nivel de UPZ - una asociación positiva pero moderada,
que no descarta un desajuste territorial. El caso más notorio: **Usme** tiene la
segunda pobreza multidimensional más alta de la ciudad (10.2%) pero una de las
coberturas más bajas del programa. El detalle completo está en el notebook.

## Base de datos para Power BI

`outputs/powerbi/` trae una base de datos agregada (sin hogares individuales) lista
para conectar en Power BI Desktop, en Excel (5 hojas) o SQLite. El notebook
(sección 9) explica cómo conectarla y sugiere la estructura de un tablero de una
página (KPIs, mapa por localidad, barras de cobertura, segmentador). El archivo
`.pbix` en sí no se puede generar por código - se arma en Power BI Desktop siguiendo
esa guía.

## Pendiente

- **SIMAT** (matrícula por establecimiento educativo oficial): el cruce se dejó
  preparado en la sección 8 del notebook, a la espera del archivo del equipo. Ya se
  identificó un dataset público equivalente en Datos Abiertos Bogotá
  ("Matrícula Total en Colegios Oficiales. Bogotá D.C.") para activarlo cuando se
  decida incorporarlo.

## Equipo

Conexión Social - Bogotá me conecta | Secretaría Distrital de Integración Social (SDIS)
