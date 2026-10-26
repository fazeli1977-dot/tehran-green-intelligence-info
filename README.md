# Tehran Green Intelligence

Tehran Green Intelligence is a bilingual Streamlit application for exploring
real Landsat observations of land-surface temperature and vegetation across
Tehran and its surroundings. It combines USGS scene discovery, satellite-image
visualization, quality-masked analysis, and a local history of validated
results. The application does not present synthetic values as observations.

## What the application does

### Explore Landsat scenes

The real satellite data page queries the public USGS LandsatLook STAC catalog
for Landsat 8 and Landsat 9 Collection 2 Level-2 surface-temperature (ST) and
surface-reflectance (SR) scenes intersecting the study area. Users can choose a
date range and inspect scene identifiers, acquisition times, satellite names,
whole-scene cloud cover, and the availability of ST and SR products.

Catalog search returns scene metadata; it does not itself display satellite
imagery. The study-area map initially shows the selected area outline. A
georeferenced Landsat image is displayed after the required scene bands have
been downloaded or uploaded and processed.

### Display natural-color satellite imagery

When automatic downloads are configured, the application creates a natural
color image from Landsat surface-reflectance bands SR_B4 (red), SR_B3 (green),
and SR_B2 (blue). The image is clipped to the study area, positioned using its
geographic bounds, and masked for fill, clouds, cloud shadows, cirrus, snow,
and saturated RGB pixels. The displayed image is from the selected real
Landsat scene; it is not a continuously refreshed, high-resolution basemap.

### Analyze land-surface temperature

The application analyzes Landsat ST_B10 together with QA_PIXEL from the same
scene. It clips the rasters to the study area, removes invalid and
quality-flagged pixels, applies the official Collection 2 scale and offset,
and reports:

- Mean, median, 95th percentile, minimum, and maximum land-surface
  temperature.
- The number and percentage of valid pixels in the study area.
- A color preview and a georeferenced map overlay of the masked raster.

The result is land-surface temperature at the satellite acquisition time; it
is not air temperature. Landsat 8/9 thermal observations have a native
resolution of approximately 100 m. The Level-2 thermal product is resampled
onto a 30 m grid; this resampling does not create additional thermal detail.
The surface-reflectance bands have a 30 m product resolution.

### Calculate NDVI

The application calculates the Normalized Difference Vegetation Index (NDVI)
from SR_B4 (red), SR_B5 (near-infrared), QA_PIXEL, and QA_RADSAT files from the
same scene. It applies the USGS surface-reflectance scale and offset and masks
fill, cloud, shadow, snow, and saturated red or near-infrared pixels. It
reports mean, median, 95th percentile, minimum, maximum, and valid-pixel
coverage, with both a color preview and a georeferenced map overlay.

NDVI is a spectral index. It is not a vegetation-cover percentage, a tree
inventory, or a land-cover classification.

### Refresh and download scenes automatically

While the real-data page is open, the application checks the catalog hourly
for the newest paired ST/SR Landsat 8 or Landsat 9 scene from the previous
90 days that covers the full study area. With valid local USGS Machine-to-
Machine (M2M) credentials, it downloads the required source bands, generates
the natural-color image, calculates temperature and NDVI, and displays the
results. No hourly checks run while the page is closed or the application is
stopped.

Public catalog search does not require an account. Downloading source bands
requires a USGS account and an M2M API token with the required permissions.
The token must be configured locally; never publish it or send it in chat.
The application reports download or authentication errors and still supports
manual GeoTIFF upload.

### Upload scene bands manually

Without configured automatic downloads, users can provide GeoTIFF bands from
one Landsat 8 or Landsat 9 Collection 2 Level-2 scene:

- Temperature: ST_B10 and QA_PIXEL.
- NDVI: SR_B4, SR_B5, QA_PIXEL, and QA_RADSAT.

For automatic natural-color imagery, SR_B2 and SR_B3 are also downloaded.
Manual uploads are checked for matching scene names, coordinate systems, and
pixel grids before analysis. Uploaded and downloaded raster data are
processed in memory and are not stored as source files by the application.

### Review and export analysis history

Successful temperature and NDVI analyses store summary statistics and
scene/date metadata in a local SQLite database. The dashboard can show the
latest validated indicators, plot up to 100 recent results, compare the two
latest observations for a selected metric, and export history as a UTF-8 CSV.
History can be filtered by metric and by all dates or the most recent 90, 180,
or 365 days. When the same metric and scene are analyzed again, the existing
history entry is updated.

The database stores summary values, valid-pixel coverage, dates, scene IDs,
and the study-area extent; it does not store source rasters or credentials.
History is local to the running installation and is not automatically shared
between computers or hosting instances.

## Study area and interpretation

The current study area is a fixed rectangular extent:

- Longitude: 51.1 to 51.7 degrees east.
- Latitude: 35.5 to 35.9 degrees north.

This extent includes Tehran and surrounding areas. It is not an official
Tehran municipal boundary or a set of district boundaries. The application
currently reports area-wide results and does not provide district rankings.
Scene-level cloud cover describes the full satellite scene; it is not a
substitute for the pixel-level quality masks applied during analysis.

The application does not currently provide official district boundaries,
district rankings, land-cover classification, an inventory of planting
locations or tree species, greening scenario simulations, 3D city or shadow
analysis, cooling-route recommendations, heat-wave alerts, or social
vulnerability analysis. These capabilities require validated data and
methods that are not included in this version.

## Data sources and privacy

- Satellite catalog and Landsat Collection 2 data: U.S. Geological Survey
  (USGS) LandsatLook and EarthExplorer services.
- Temperature scaling: [USGS Landsat Collection 2 surface temperature
  guidance](https://www.usgs.gov/landsat-missions/landsat-collection-2-surface-temperature).
- Reflectance scaling: [USGS Landsat Collection 2 surface reflectance
  guidance](https://www.usgs.gov/landsat-missions/landsat-collection-2-surface-reflectance).
- Quality masks: [USGS Collection 2 quality assessment
  bands](https://www.usgs.gov/landsat-missions/landsat-collection-2-quality-assessment-bands).

USGS credentials must be configured securely by the application operator.
Never commit or share an M2M token. The application does not intentionally
save credentials or source satellite rasters.

## Source code and deployment

This repository is a public, documentation-only overview. The application
source code is maintained separately in a private repository and is not
included here. A public deployment would require a hosting provider, securely
configured USGS credentials if automatic downloads are required, persistent
storage for analysis history, and an appropriate access-control policy.
