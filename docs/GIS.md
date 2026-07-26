# GIS & Geospatial Analysis

## Geospatial Information Systems Integration

This document outlines the GIS and geospatial analysis framework for Flood Sentinel, including data sources, spatial processing workflows, and integration with flood prediction models.

## GIS Data Overview

### Core Datasets

#### 1. Digital Elevation Model (DEM)
**Purpose**: Fundamental for flood risk assessment, watershed delineation, and flow routing

**Characteristics**:
- Resolution: 10-30 meters (SRTM, Copernicus, Lidar)
- Format: GeoTIFF, Cloud-optimized GeoTIFF
- Coverage: Global (SRTM), local (Lidar for high-quality)
- Vertical accuracy: ±5-10 meters (SRTM), ±0.3 meters (Lidar)

**Data Sources**:
- **SRTM v3**: 30m resolution, global coverage
  - Access: [USGS Earth Explorer](https://earthexplorer.usgs.gov/)
  - Tile-based download
  
- **Copernicus DEM**: 30m and 90m resolution
  - Access: [Copernicus Program](https://www.copernicus.eu/)
  - Cloud-native on AWS
  
- **Local Lidar**: 1-5m resolution (where available)
  - Contact: National mapping agencies, survey departments
  - Higher cost but crucial for urban accuracy

**Processing**:
- Mosaic tiles into seamless DEM
- Fill sinks/depressions (hydrologic conditioning)
- Compute derived products (slope, aspect, curvature)
- Create for each region of interest

#### 2. Building Footprints & Urban Data
**Purpose**: Identify structures at flood risk, estimate population exposure

**Data Sources**:
- **OpenStreetMap (OSM)**:
  - Free, global, community-maintained
  - Extract via Overpass API or QuickOSM
  - Building tag with height/type information
  
- **Google Open Buildings**:
  - AI-derived from satellite imagery
  - 30m resolution, 5.2 billion buildings globally
  - High-quality for developing regions
  
- **Proprietary**: Local cadastral/property data
  - Higher accuracy, may include ownership
  - Contact municipal authorities
  
- **Sentinel-2/Landsat**: Derive urban density via NDVI

**Attributes to Extract**:
- Building footprint (geometry, area)
- Use type (residential, commercial, industrial)
- Height (if available)
- Construction year (vulnerability assessment)
- Population estimate

#### 3. Hydrological Network
**Purpose**: Understand water flow, upstream contributions, channel geometry

**Components**:
- Stream network (rivers, channels)
- Watershed boundaries
- Flow direction and accumulation
- Stream order (Strahler classification)

**Data Sources**:
- **SRTM-derived**: HydroSHEDS dataset
  - 15 arc-second resolution (~500m)
  - Global stream network and basins
  - Access: [HydroSHEDS](https://www.hydrosheds.org/)
  
- **OSM Waterways**:
  - Lines and polygons representing streams
  - Variable quality
  
- **Local Surveys**: Field surveys, gauging stations
  - High accuracy for critical reaches
  
- **Satellite Imagery**: NDVI/NDWI to identify water bodies

**Processing Workflow**:
```
DEM → Flow Direction → Flow Accumulation → 
Stream Network → Watershed Delineation
```

#### 4. Land Use / Land Cover (LULC)
**Purpose**: Understand runoff generation, infiltration, roughness

**Classes**:
- Water bodies
- Vegetation (forest, grassland, crops)
- Urban/built-up
- Bare soil/rock
- Agricultural

**Data Sources**:
- **Copernicus LULC**: 10m resolution, annual
  - Access: [Copernicus Program](https://www.copernicus.eu/)
  - Cloud-native geospatial layer
  
- **MODIS LULC**: 500m resolution, global, free
  - Lower resolution but open access
  
- **Sentinel-2 Classification**: Custom ML classification
  - Higher resolution (10m) with local training data

**Integration with Models**:
- Manning's n (roughness) by LULC class
- Runoff coefficients (CN values for SCS method)
- Infiltration rates

#### 5. Flood Hazard & Risk Maps
**Purpose**: Validate model predictions, understand historical patterns

**Data Sources**:
- **Global Flood Database**: UNEP-WCMC, DFO
  - Historical flood extent from satellite
  - 1984-present, free access
  
- **Local/National Flood Maps**:
  - 100-year, 50-year, 25-year flood extents
  - Contact: Flood management departments, water resources ministries
  
- **FEMA Flood Insurance Rate Maps (FIRM)** (US):
  - 1% annual exceedance probability zones
  - Historical and model-based
  
- **Proprietary Risk Models**:
  - Insurance companies, consulting firms
  - Often require licensing

#### 6. Infrastructure & Vulnerability
**Purpose**: Assess critical assets, population exposure, economic impact

**Data Layers**:
- Road networks (OSM, HERE, Google Maps)
- Bridges and causeways
- Power lines and substations
- Water supply/sewage systems
- Hospitals, schools, emergency services
- Levees, dams, flood control structures

**Population Data**:
- **WorldPop**: 100m resolution population density
  - High-quality for developing regions
  - Access: [WorldPop](https://www.worldpop.org/)
  
- **GHSL**: Global Human Settlement Layer
  - Built-up area and population density
  - Multiple time periods for change analysis
  
- **Census Data**:
  - Official population counts and demographics
  - Contact: National statistical agencies

#### 7. Sentinel-2 & Satellite Imagery
**Purpose**: Monitor vegetation, water, urban changes; validate models

**Characteristics**:
- 10-20m resolution, multispectral (12 bands)
- 5-day revisit frequency
- Free, open access via Google Earth Engine or AWS
- Near real-time data availability

**Common Indices**:
- NDVI (Normalized Difference Vegetation Index): Vegetation stress
- NDWI (Normalized Difference Water Index): Water detection
- NDBI (Normalized Difference Built-up Index): Urban extent
- MNDWI: Modified water index for flooded areas

---

## GIS Processing Workflows

### Workflow 1: Watershed Delineation & Sensor Placement

**Objective**: Determine optimal sensor locations for monitoring

**Steps**:
1. **Flow Direction Analysis**:
   ```
   Input: Conditioned DEM
   Process: D8 or D-infinity flow direction algorithm
   Output: Flow direction raster
   ```

2. **Flow Accumulation**:
   ```
   Process: Cumulative flow from upslope cells
   Output: Accumulation raster (in units of contributing cells)
   ```

3. **Stream Network Definition**:
   ```
   Threshold: Cells with >100,000 accumulated flow = stream
   Output: Stream network raster/vector
   ```

4. **Watershed Delineation**:
   ```
   For each sensor location:
   - Snap to nearest stream cell
   - Delineate upslope contributing area
   - Calculate watershed characteristics
   ```

5. **Sensor Optimal Placement**:
   ```
   Criteria:
   - Downstream from population centers
   - Where stream network density is high
   - Near existing infrastructure
   - Balanced spatial coverage
   
   Method: Spatial optimization (e.g., location-allocation model)
   ```

### Workflow 2: Flood Inundation Mapping

**Objective**: Map likely flood extent for different return periods

**Methods**:

**Method A: DEM-based Depression Analysis**
```
1. Identify depressions (sinks) in DEM
   - These are likely flood-prone areas
2. For given water level:
   - Cells below threshold = inundated
   - Use conditional raster analysis
3. Overlay with buildings to find exposed structures
```

**Method B: Hydrodynamic Modeling (advanced)**
```
1. Define channel geometry from DEM
2. Estimate discharge from rainfall-runoff model
3. Route through hydrodynamic model (HEC-RAS, LISFLOOD)
4. Extract inundation extent and depth
```

**Method C: ML-based Inundation Prediction**
```
1. Training: Historical flood extent data + GIS features
2. Features: Elevation, slope, proximity to water, LULC
3. Model: Logistic regression, random forest, or neural network
4. Output: Probability of inundation for each cell
```

### Workflow 3: Vulnerability & Risk Assessment

**Objective**: Quantify exposure and impact of floods

**Steps**:
1. **Identify Exposed Assets**:
   ```
   Intersect with inundation extent:
   - Buildings (count, area, type)
   - Population (from WorldPop or census)
   - Critical infrastructure (hospitals, power plants)
   - Agricultural land
   ```

2. **Assign Vulnerability Weights**:
   ```
   Building vulnerability = f(height, age, construction type)
   Population vulnerability = f(age distribution, health status)
   Infrastructure criticality = f(function, replacement cost)
   ```

3. **Calculate Risk Metrics**:
   ```
   Risk = Hazard × Exposure × Vulnerability
   Expected Annual Loss = Sum over all flood scenarios
   ```

4. **Identify Vulnerable Communities**:
   ```
   Equity analysis:
   - Areas with high flood risk + low income
   - Areas with low access to warnings
   - Spatially isolated communities
   ```

### Workflow 4: Urban Growth & Climate Change Scenarios

**Objective**: Project future flood risk under different scenarios

**Steps**:
1. **Baseline (Current)**:
   - Current LULC, infrastructure, population
   
2. **Scenario A: Urban Growth (2030, 2050)**:
   - Project building expansion from current trends
   - Increase impervious surface
   - Recalculate runoff and flood extent
   
3. **Scenario B: Climate Change**:
   - Downscale climate models (CMIP5/6) to local scale
   - Adjust precipitation intensity and frequency
   - Rerun hydrological models
   - Compare to baseline

4. **Scenario C: Mitigation**:
   - Add green spaces, retention ponds
   - Improve drainage infrastructure
   - Elevate buildings in flood zones
   - Assess impact on flood risk

---

## Feature Engineering from GIS Data

### GIS-Derived Features for ML Models

**1. Topographic Features**:
- Elevation (meters above sea level)
- Slope (degrees or %)
- Aspect (direction of slope)
- Terrain Ruggedness Index (TRI)
- Topographic Position Index (TPI)
- Curvature (convergence/divergence)

**2. Hydrological Features**:
- Upstream Contributing Area (km²)
- Stream Order (Strahler)
- Distance to nearest stream (meters)
- Slope to nearest stream
- Specific Catchment Area (Topographic Index)

**3. Urban Morphology**:
- Building density (buildings/km²)
- Impervious surface fraction
- Street network density
- Average building height
- Land use diversity (entropy index)

**4. Proximity Features**:
- Distance to major water body
- Distance to levee or flood control structure
- Elevation difference from flood control structure

**5. Neighborhood Features** (for each building):
- Median elevation of surrounding buildings
- Standard deviation of elevation
- Count of buildings below/above building height
- Distance to highest building in vicinity

### Feature Extraction Code Example

```python
import geopandas as gpd
import rasterio
from rasterio.mask import mask
from shapely.geometry import box

# Load data
sensors = gpd.read_file('sensors.geojson')
dem = rasterio.open('dem.tif')
buildings = gpd.read_file('buildings.geojson')

# Extract elevation at sensor locations
for idx, sensor in sensors.iterrows():
    point = sensor.geometry
    # Buffer to small area
    bbox = box(point.x - 15, point.y - 15, point.x + 15, point.y + 15)
    # Extract DEM values in buffer
    out_image, out_transform = mask(dem, [bbox], crop=True)
    sensors.loc[idx, 'elevation'] = out_image.mean()
    sensors.loc[idx, 'elevation_std'] = out_image.std()

# Count buildings near sensor
for idx, sensor in sensors.iterrows():
    buffer = sensor.geometry.buffer(1000)  # 1 km buffer
    nearby = buildings[buildings.geometry.within(buffer)]
    sensors.loc[idx, 'building_count'] = len(nearby)
    sensors.loc[idx, 'building_area_m2'] = nearby.geometry.area.sum()

sensors.to_file('sensors_with_features.geojson', driver='GeoJSON')
```

---

## Tools & Software

### GIS Analysis Tools

**QGIS**
- Open-source, professional GIS
- Python plugin support (PyQGIS)
- Suitable for: Visualization, basic analysis, DEM processing

**GDAL/OGR**
- Command-line geospatial data tools
- Language bindings (Python via rasterio/fiona)
- Suitable for: Batch processing, format conversion, raster operations

**ArcGIS (ESRI)**
- Industry standard, high cost
- Powerful geoprocessing tools
- Suitable for: Complex workflows, professional production

### Python Geospatial Libraries

**GeoPandas**
- Pandas-like interface for vector (GIS) data
- Integration with shapely and fiona
- Excellent for: Spatial operations, filtering, joining

**Rasterio**
- Read/write geospatial rasters
- Integration with GDAL
- Excellent for: DEM processing, satellite imagery analysis

**Shapely**
- Geometric object processing
- Buffer, intersect, union operations
- Excellent for: Vector geometry manipulation

**Fiona**
- Read/write vector data (shapefiles, GeoJSON, etc.)
- Clean Python API to OGR
- Excellent for: Data I/O, format conversion

**Folium/Leaflet.js**
- Interactive web mapping
- Lightweight JavaScript library
- Excellent for: Web dashboards, public-facing maps

**PostGIS**
- Spatial extension for PostgreSQL
- Powerful SQL-based spatial queries
- Excellent for: Large datasets, production systems

### Google Earth Engine

**Why GEE?**
- Petabyte-scale imagery archive
- Parallel processing on Google's infrastructure
- Free academic access
- Python API

**Typical Workflow**:
```javascript
var sentinel2 = ee.ImageCollection("COPERNICUS/S2")
    .filterBounds(roi)
    .filterDate('2020-01-01', '2020-12-31')
    .filter(ee.Filter.lt('CLOUDY_PIXEL_PERCENTAGE', 20));

var ndvi = sentinel2.map(function(image) {
    return image.normalizedDifference(['B8', 'B4']);
});

var ndviMean = ndvi.mean();
Map.addLayer(ndviMean, {min: 0, max: 1}, 'NDVI');
```

---

## Integration with Flood Prediction

### Workflow Integration

```
GIS Analysis → Feature Engineering → ML Model Training
                      ↓
              Historical Flood Events
              (validation data)
                      ↓
              Model Evaluation & Uncertainty
                      ↓
              Risk Assessment & Mapping
                      ↓
              Alert Generation & Dissemination
```

### Real-Time Updates

**Scenario: Flood Alert System**

1. **Sensor Data Received** (5-min interval)
   - Water level, rainfall, soil moisture
   
2. **Feature Engineering**:
   - Extract GIS features (cached from analysis)
   - Combine with sensor data
   
3. **Model Inference**:
   - Predict water level (1h, 3h, 6h ahead)
   - Compute flood probability
   
4. **Inundation Mapping**:
   - If probability >50%, trigger detailed mapping
   - GIS query: Which buildings inundated?
   - GIS query: Which critical infrastructure at risk?
   
5. **Alert Dispatch**:
   - Identify affected populations (spatial join: buildings × population)
   - Generate location-specific alerts
   - Route to SMS/app/sirens

---

## Data Management & Storage

### PostgreSQL + PostGIS Setup

```sql
-- Create spatial database
CREATE DATABASE flood_sentinel;

-- Enable PostGIS extension
CREATE EXTENSION postgis;

-- Create tables
CREATE TABLE sensors (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    location GEOGRAPHY(POINT, 4326),
    elevation FLOAT,
    watershed_area FLOAT
);

CREATE TABLE buildings (
    id SERIAL PRIMARY KEY,
    geometry GEOGRAPHY(POLYGON, 4326),
    building_height FLOAT,
    use_type VARCHAR(50),
    population INT
);

CREATE TABLE flood_extent (
    id SERIAL PRIMARY KEY,
    return_period VARCHAR(20),
    geometry GEOGRAPHY(POLYGON, 4326),
    flood_depth_m FLOAT
);

-- Create spatial indexes
CREATE INDEX idx_sensors_location ON sensors USING GIST(location);
CREATE INDEX idx_buildings_geom ON buildings USING GIST(geometry);
CREATE INDEX idx_flood_geom ON flood_extent USING GIST(geometry);

-- Spatial queries
SELECT COUNT(*) FROM buildings 
WHERE ST_Contains(
    (SELECT geometry FROM flood_extent WHERE return_period='100yr'),
    buildings.geometry
);
```

---

## Best Practices

### Data Quality
- Validate CRS (coordinate reference system) consistency
- Check for topology errors (overlaps, gaps)
- Compare elevation with nearby reference points
- Regular updates for LULC and building footprints

### Performance Optimization
- Use spatial indexes (GIST, BRIN in PostGIS)
- Cache computed features
- Tile large rasters for efficient access
- Use appropriate resolution (don't over-sample)

### Documentation
- Record data source, date, and version
- Document any preprocessing or assumptions
- Maintain metadata (CRS, units, accuracy)
- Version control analysis scripts (Git)

---

## References

### Key Papers
- Bates et al. (2010): Flood inundation modeling
- Schumann et al. (2014): Satellite remote sensing of floods
- Sampson et al. (2015): A high-resolution global flood hazard model

### Data Repositories
- [HydroSHEDS](https://www.hydrosheds.org/)
- [Google Earth Engine](https://earthengine.google.com/)
- [OpenStreetMap](https://www.openstreetmap.org/)
- [USGS Earth Explorer](https://earthexplorer.usgs.gov/)
- [Copernicus Program](https://www.copernicus.eu/)

### Textbooks
- *Geographic Information Analysis* by David O'Sullivan & David J. Unwin
- *Remote Sensing and GIS for Ecology* by Millington et al.

---

**Status**: Phase 0 - GIS framework definition (implementation begins Phase 1)  
**Last Updated**: July 2026

Questions about GIS integration? Open an issue or discussion!
