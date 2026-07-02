# DataJam - Conexión Social - Bogotá me conecta

Análisis espacial del programa distrital **Conexión Social - Bogotá me conecta** (SDIS)
frente a la **pobreza multidimensional** y la **matrícula oficial (SIMAT)** de Bogotá,
desarrollado para la **Bogotá Datajam: Uso y Aprovechamiento de Datos (Edición 2) - 2026**.

## Pregunta de análisis

¿Las instalaciones del programa Conexión Social están llegando efectivamente a las
zonas de Bogotá con mayores niveles de pobreza multidimensional y mayor concentración
de estudiantes en colegios públicos?

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
  estudiantes_simat_por_establecimiento.csv     <- estudiantes por colegio oficial (SIMAT)
  comparacion_ipm_cs_simat_localidad.csv        <- tabla conjunta IPM + CS + SIMAT
  comparacion_ipm_cs_simat_upz.csv
outputs/figures/
  dispersion_pobreza_vs_cobertura_localidad.png
  mapa_pobreza_vs_cobertura_localidad.png
  mapa_ipm_con_hogares_cs.png                   <- IPM + ubicacion exacta de cada hogar CS
  mapa_hogares_cs_sector_catastral.png
  mapa_estudiantes_simat_localidad.png
  mapa_simat_con_hogares_cs.png                 <- SIMAT + ubicacion exacta de cada hogar CS
  matriz_correlacion_ipm_cs_simat.png
  mapas_comparacion_ipm_cs_simat.png
  dispersion_comparacion_ipm_cs_simat.png
outputs/powerbi/
  bd_conexion_social_powerbi.xlsx               <- base de datos agregada (6 tablas) para Power BI
  bd_conexion_social_powerbi.db                 <- misma base en SQLite
```

> **Nota:** `mapa_ipm_con_hogares_cs.png` y `mapa_simat_con_hogares_cs.png` grafican
> la coordenada exacta de cada hogar **verificado** (localidad declarada = localidad
> geométrica; ver sección 3 del notebook). Se incluyen por decisión explícita del
> equipo; si se van a compartir fuera del equipo, considerar reemplazarlos por una
> versión de densidad (mapa de calor) en vez de puntos individuales.

## Fuentes de datos

| Fuente | Nivel | ¿Está en este repositorio? |
|---|---|---|
| Instalaciones Conexión Social (`Perfiles_CS.xlsx`, SDIS) — 72.797 hogares | Hogar | **No** (dirección y coordenadas de hogares - dato sensible) |
| Encuesta Multipropósito de Bogotá 2021 (`em2021.csv` + variables adicionales, SDP/DANE) | Persona/hogar | **No** (microdato) |
| Matrícula oficial SIMAT (`Simat_1Jul26.csv`, SED) | Estudiante | **No** (nombre, documento, dirección, teléfono, correo - dato muy sensible; el notebook nunca lee esas columnas) |
| Límites de Localidad, UPZ y Sector Catastral | Polígono | Sí (`data/geo/`, información pública de IDECA / Catastro Bogotá) |

Los archivos con datos personales quedan **solo en el computador de cada integrante**
(ver `.gitignore`). A este repositorio solo se suben el notebook y los **resultados
agregados** por localidad, UPZ, sector catastral y colegio - ninguna fila corresponde
a un hogar o estudiante individual (excepto el mapa de puntos señalado arriba).

## Cómo correr el notebook

1. Colocar en la carpeta raíz de este proyecto (junto al `.gitignore`) los archivos
   originales: `Perfiles_CS.xlsx`, `em2021.csv`, `20240430_variables_adicionales_2021.csv`,
   `Simat_1Jul26.csv`.
2. Instalar dependencias: `pip install geopandas pyogrio pyproj shapely mapclassify jupyter openpyxl pyarrow`.
3. Ejecutar `notebooks/01_analisis_espacial_conexion_social.ipynb` de principio a fin.
   La primera vez descarga los límites geográficos oficiales desde los servicios
   públicos de Catastro Bogotá / IDECA y los guarda en `data/geo/` para no repetir la
   descarga. `Simat_1Jul26.csv` pesa ~500 MB; el notebook solo carga las columnas
   geográficas y de colegio (nunca datos personales) y cachea el resultado agregado
   en `data/processed/` para acelerar corridas posteriores.

## Hallazgos principales (nivel localidad)

El programa opera por fases: de las 20 localidades urbanas, **solo 9 tienen
instalaciones** (Bosa, Suba, Kennedy, San Cristóbal, Rafael Uribe Uribe, Ciudad
Bolívar, Engativá, Tunjuelito y Santa Fe — campo `Localidad` de la fuente, sección 3
del notebook). Eso importa para leer las correlaciones:

- **Pobreza vs. cobertura, toda la ciudad:** correlación de Spearman = **0.36**
  (0.57 a nivel de UPZ). Pero gran parte de esta asociación viene simplemente de que
  las localidades sin abrir tienen cobertura 0 sin importar su pobreza — **Usme**
  tiene la segunda pobreza más alta de la ciudad (10.2%) y cobertura 0 porque el
  programa **todavía no ha llegado ahí**, no por una falla de focalización.
- **Pobreza vs. cobertura, solo dentro de las 9 localidades ya abiertas:**
  correlación = **0.10** — casi nula. Es decir, una vez el programa ya está operando
  en una localidad, cuánto se instala ahí no está explicado por su nivel de pobreza.
  Bosa (6.5% IPM) tiene la cobertura más alta (76.6 hogares por 1.000); Santa Fe
  (8.0% IPM, más pobre) tiene una de las más bajas (17.3 por 1.000).
- **Matrícula oficial (SIMAT) vs. cobertura Conexión Social:** correlación = **0.77**
  - el programa está mucho más alineado con la concentración de estudiantes de
  colegio público que con la pobreza medida por IPM.
- **Pobreza vs. matrícula oficial:** correlación = solo **0.21** - pobreza y
  concentración de estudiantes públicos no coinciden fuertemente en el espacio.

**Lectura conjunta:** la prioridad #1 para focalización no es "llegar a más
localidades pobres" en abstracto, sino dos cosas concretas: (1) evaluar cuándo abrir
en localidades de alta pobreza que siguen fuera del despliegue (Usme, Los Mártires,
Antonio Nariño, Puente Aranda), y (2) dentro de las localidades ya abiertas,
revisar por qué la intensidad de instalación no sigue el nivel de pobreza interno.

El detalle completo, con mapas y tablas por localidad y UPZ, está en el notebook
(secciones 6, 8 y 9). El listado de 192 hogares con desajuste geográfico o sin
coordenadas queda en `revision_equipo_hogares_localidad.xlsx` (solo local, no se
sube — ver sección 3.1 del notebook).

## Base de datos para Power BI

`outputs/powerbi/` trae una base de datos agregada (sin hogares ni estudiantes
individuales) lista para conectar en Power BI Desktop, en Excel (6 hojas) o SQLite.
El notebook (sección 10) explica cómo conectarla y sugiere la estructura de un
tablero de una página (KPIs, mapa por localidad, barras de cobertura, segmentador).
El archivo `.pbix` en sí no se puede generar por código - se arma en Power BI
Desktop siguiendo esa guía.

## Equipo

Conexión Social - Bogotá me conecta | Secretaría Distrital de Integración Social (SDIS)
