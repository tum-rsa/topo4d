# Topographic 4D Extension Specification

- **Title:** Topographic 4D
- **Identifier:** <https://stac-extensions.github.io/topo4d/v1.0.0/schema.json>
- **Field Name Prefix:** topo4d
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: [@tum-rsa](https://www.asg.ed.tum.de/en/rsa/home/)

This repository explains the Topographic 4D (topo4d) Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification, developed as part of the research project: Development of Standardized Metadata for 3D Time Series Workflows ([4D-WORKS](https://www.asg.ed.tum.de/en/rsa/research/4d-works/)).

This extension provides a standardized approach for handling time-dependent metadata in diverse 4D datasets (time series of 3D geographic data). While it notably includes support for point clouds (acquired via laser scanning or photogrammetry), it is designed to be broadly applicable to various types of 4D data including multi-sensor datasets. It addresses the challenge of managing metadata for topographic observations collected over time, where hundreds or thousands of epochs may exist, so far without consistent standards for critical time-dependent information like timestamps and transformation matrices.

By implementing community conventions in a standardized metadata format, this extension makes 4D datasets more accessible, interoperable, and reusable across different projects and tools, regardless of data sources, acquisition techniques, or temporal sampling intervals. The extension supports automated metadata generation for workflows in fields such as geomorphology, hydrology, ecology, and other disciplines requiring time-series analysis of 3D geographic data.

This work is funded by the German Research Foundation (DFG) in the frame of the National Research Data Infrastructure program [NFDI4Earth](https://www.nfdi4earth.de/) (project number: 460036893).

## Examples

- [Multi-sensor point clouds*](./examples/Isar/): Shows integration with mult-sensor point clouds and processing products
- [Near-continuous 3D time series*](./examples/kijkduin/): Shows high-frequency temporal sampling acquisitions
- [Item example*](examples/item.json): Shows the basic usage of the extension in a STAC Item
- [Collection example*](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

*\* the data in examples are only for testing purposes, not real values.*

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [x] Collections
- [x] Item Properties (incl. Summaries in Collections)
- [x] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [ ] Links

### Collection Summaries

| Field Name          | Type    | Description                                                             |
|---------------------|---------|-------------------------------------------------------------------------|
| num_items           | Integer | Number of items in the collection                                       |
| timestamp_list      | Array   | List of acquisition timestamps for temporal overview                    |
| temporal_resolution | String  | Temporal resolution of multiple acquisition (e.g., monthly, weekly, 1hr) |

### Item Properties

| Field Name                | Type    | Description  |
|---------------------------|---------|--------------------------------|
| datetime                  | String  | **REQUIRED.** Timestamp of data collection (ISO 8601 format) |
| topo4d:data_type          | String  | **REQUIRED.** The type of data: pointcloud, mesh, raster, vector, text |
| topo4d:tz                 | String  | Timezone of the data collection, e.g., "UTC+1", "Europe/Munich" |
| topo4d:acquisition_mode   | String  | Acquisition method: "ULS" (drone lidar), "UPH" (drone photogrammetry), "TLS" (terrestrial laser scanning) |
| topo4d:duration           | Number  | Time duration of the measurement in seconds |
| topo4d:spatial_resolution | Number  | Spatial resolution in meters (sampling interval or grid spacing) |
| topo4d:measurement_error  | Number  | System measurement error or sensor accuracy in meters |
| topo4d:orientation        | String  | UAV survey pattern: "Nadir", "Nadir+Oblique" |
| topo4d:global_trafo       | Array   | 4x4 transformation matrix for 3D point cloud georeferencing |
| topo4d:trafometa          | [Object](#trafometa-object)  | Metadata for inter-epoch transformations and co-registration |
| topo4d:productmeta       | [Object](#productmeta-object)  | Metadata for derived products including processing parameters |


### trafometa Object

The `topo4d:trafometa` object contains transformation metadata for multi-epoch alignment:

| Field Name            | Type   | Description  |
|-----------------------|--------|------------------------------------|
| reference_epoch       | Object/Link | **REQUIRED.** Reference to the temporal reference dataset |
| registration_error    | Number | Root mean square error of co-registration in meters |
| transformation        | Array  | 4x4 transformation matrix (preferred method) |
| affine_transformation | Array  | Alternative 4x4 or 3x4 affine transformation matrix |
| rotation              | Array  | 3x3 rotation matrix (when transformation not provided) |
| translation           | Array  | 3D translation vector (when transformation not provided) |
| reduction_point       | Array  | Origin shift for numerical stability |

### productmeta Object

The `topo4d:productmeta` object describes derived data products:

| Field Name    | Type        | Description  |
|---------------|-------------|---------------------|
| product_name  | String      | Product identifier (e.g., "M3C2", "DEM", "Mesh") |
| param         | Object      | Processing parameters specific to the product type |
| derived_from  | Object/Link | Reference to source data or processing chain |
| product_level | String      | Processing level following STAC conventions (L0, L1, L2, etc.) |

### External Asset References

Large array data is stored as separate asset files to improve performance and maintainability:

| Asset Type | File Format | Description |
|------------|-------------|-------------|
| trajectory | GeoJSON | UAV flight paths with timestamps |
| scan_positions | GeoJSON | Terrestrial scanning positions (X,Y,Z coordinates) |

### Example Asset Reference
```json
"assets": {
  "trajectory": {
    "href": "./trajectory.geojson",
    "type": "application/geo+json",
    "title": "UAV Flight Trajectory",
    "description": "Flight path with timestamps for acquisition metadata",
    "roles": ["metadata"]
  }
}
```

## Best Practices

### Field Usage Guidelines

#### **datetime and topo4d:tz**
Always specify acquisition time in ISO 8601 format. Use `topo4d:tz` to clarify the local timezone for fieldwork coordination:
```json
"datetime": "2024-08-12T10:30:00Z",
"topo4d:tz": "Europe/Munich"
```

#### **topo4d:data_type**
Choose the most specific data type. For point clouds from different sources:
- `"pointcloud"` - Raw 3D point measurements
- Processing derivatives may use other types (e.g., `"raster"`, `"mesh"`, `"vector"`, `"text"`, `"other"`)

#### **topo4d:acquisition_mode**
Use standardized abbreviations:
- `"ULS"` - Uncrewed Aerial System with LiDAR sensor
- `"UPH"` - Uncrewed Aerial System with photogrammetry
- `"TLS"` - Terrestrial Laser Scanning
- `"MLS"` - Mobile Laser Scanning

#### **Spatial Resolution and Measurement Error**
Express both in consistent units (meters). For point clouds:
```json
"topo4d:spatial_resolution": 0.05,  // 5cm point spacing
"topo4d:measurement_error": 0.02    // 2cm accuracy
```

#### **topo4d:global_trafo vs proj:transform**
- Use `topo4d:global_trafo` for 3D point cloud transformations (4x4 matrices)
- Use `proj:transform` from the Projection extension for 2D raster coordinate transforms
- These serve different purposes and should not be confused

#### **Transformation Workflows**
For multi-epoch datasets, use `topo4d:trafometa` to document co-registration:
```json
"topo4d:trafometa": {
  "reference_epoch": {
    "rel": "reference_dataset_id",
    "href": "./reference_epoch.json"
  },
  "transformation": [[1,0,0,0.05],[0,1,0,-0.02],[0,0,1,0.01],[0,0,0,1]],
  "registration_error": 0.015
}
```

### Coordinate System Management

- **Use proj:code** from the [Projection extension](https://github.com/stac-extensions/projection?tab=readme-ov-file#fields) for spatial reference system identification
- **Combine with topo4d:global_trafo** when additional 3D transformations are needed
- **Document elevation reference** in asset descriptions (ellipsoidal vs orthometric heights)

### Asset Organization

- Store large trajectory data as external GeoJSON files
- Use scan position assets for terrestrial measurement campaigns
- Link external calibration files, processing reports and metadata documentations as related assets

## Usage with Other Extensions

The topo4d extension is designed to work alongside existing STAC extensions:

### [Point Cloud Extension](https://github.com/stac-extensions/pointcloud/tree/main)
```json
"stac_extensions": [
  "https://stac-extensions.github.io/topo4d/v1.0.0/schema.json",
  "https://stac-extensions.github.io/pointcloud/v1.0.0/schema.json"
],
"pc:count": 1500000,
"pc:type": "lidar",
"pc:encoding": "laz",
"topo4d:data_type": "pointcloud",
"topo4d:spatial_resolution": 0.05
```

### [Projection Extension](https://github.com/stac-extensions/projection?tab=readme-ov-file#fields)
```json
"stac_extensions": [
  "https://stac-extensions.github.io/topo4d/v1.0.0/schema.json",
  "https://stac-extensions.github.io/projection/v1.1.0/schema.json"
],
"proj:code": "EPSG:25832",
"topo4d:global_trafo": [[0.999, 0.001, 0, 1.5], [...]],
```

### [Timestamps Extension](https://github.com/stac-extensions/timestamps)
```json
"stac_extensions": [
  "https://stac-extensions.github.io/topo4d/v1.0.0/schema.json",
  "https://stac-extensions.github.io/timestamps/v1.1.0/schema.json"
],
"datetime": "2024-08-12T10:30:00Z",
"timestamps:published": "2024-08-15T14:20:00Z",
"topo4d:tz": "Europe/Berlin"
```

### [Common Metadata](https://github.com/radiantearth/stac-spec/blob/master/commons/common-metadata.md)
Use standard STAC fields alongside topo4d-specific properties:
```json
"instruments": ["riegl-vz2000"],
"platform": "terrestrial-laser-scanner",
"topo4d:acquisition_mode": "TLS",
"topo4d:measurement_error": 0.01
```

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```