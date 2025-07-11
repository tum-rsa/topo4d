# Topographic 4D Extension Specification

- **Title:** Topographic 4D
- **Identifier:** <https://stac-extensions.github.io/topo4d/v1.0.0/schema.json>
- **Field Name Prefix:** topo4d
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: [@tum-rsa](https://www.asg.ed.tum.de/en/rsa/home/)

This repository explains the Topographic 4D (topo4d) Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification, developed as part of the research project: Development of Standardized Metadata for 3D Time Series Workflows ([4D-WORKS](https://www.asg.ed.tum.de/en/rsa/research/4d-works/)). 

This extension provides a standardized approach for handling time-dependent metadata in diverse 4D datasets (time series of 3D data). While it includes support for point clouds, it is designed to be broadly applicable to various types of 4D geospatial data including photogrammetry, and multi-sensor integrated datasets. It addresses the challenge of managing metadata for topographic observations collected over time, where hundreds or thousands of epochs may exist without consistent standards for critical time-dependent information like timestamps and transformation matrices. 

By standardizing metadata formats, this extension makes 4D datasets more accessible, interoperable, and reusable across different projects and tools, regardless of data sources, acquisition techniques, or temporal sampling intervals. The extension supports automated metadata generation for workflows in fields such as geomorphology, hydrology, ecology, and other disciplines requiring time-series analysis of 3D spatial data. 

This work is funded by the German Research Foundation (DFG) in the frame of the National Research Data Infrastructure program [NFDI4Earth](https://www.nfdi4earth.de/) (project number: 460036893). 

- Examples:
  - [Multi-sensor integrated datasets*](./examples/Isar/)
  - [Near-continuous 4D dataset*](./examples/kijkduin/)
  - [Item example*](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Collection example*](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

*\* the data in examples are only for test, not real values.*

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [x] Collections
- [x] Item Properties (incl. Summaries in Collections)
- [ ] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [ ] Links

### Collection Summaries

| Field Name          | Type    | Description                                                             |
|---------------------|---------|-------------------------------------------------------------------------|
| num_items           | Integer | Number of items                                                         |
| timestamp_list      | Array   | list of datetime                                                        |
| temporal_resolution | String  | Temporal resolution of multiple acquisiton (e.g., monthly, weekly, 1hr) |

### Item Properties

| Field Name                | Type    | Description  |
|---------------------------|---------|--------------------------------|
| datetime                  | String  | **REQUIRED.** Timestamp of data collection |
| topo4d:data_type          | String  | **REQUIRED.** The type of data, e.g., pointcloud, mesh, raster, vector, text |
| topo4d:native_crs         | String  | **REQUIRED.** The spatial reference to apply to the data, e.g., "EPSG:32634 ([PDAL example](https://pdal.io/en/stable/stages/readers.las.html#options))", "Local", "Undefined". |
| topo4d:sensor             | String  | e.g., Zenmuse L2, Camera |
| topo4d:tz                 | String  | The timezone of the data. It can be the string like "UTC+1" or "Europe/Munich" |
| topo4d:acquisition_mode   | String  | The mode of acquisition, e.g., ULS, UPH, TLS" |
| topo4d:duration           | Number  | The time duration of the measurement  |
| topo4d:trajectory         | Array   | Flight trajectory with timestamps.  **UAV only.** |
| topo4d:scan_positions     | Array   | List of scan positions (X, Y, Z) |
| topo4d:orientation        | String  | The orientation of the scan, e.g., nadir, oblique, mixed |
| topo4d:point_count        | Integer | Number of points |
| topo4d:spatial_resolution | Number  | The spatial resolution, e.g., sampling interval |
| topo4d:measurement_error  | Number  | System error during measurement or sensor accuracy |
| topo4d:global_trafo       | Array   | Global transformation matrix, e.g., offset |
| topo4d:trafometa          | Object  | An object including all relevant meta information for transformation |
| topo4d:productmeta       | Object  | Exists if this item is a generated product. This object describe relavant meta information of the product e.g., parameters used to generate products |

### trafometa Object

| Field Name            | Type   | Description  |
|-----------------------|--------|------------------------------------|
| reference_epoch       | Object/Link | **REQUIRED.** e.g., link to other item |
| registration_error    | Number | Exists if it's a co-registered point cloud  |
| transformation        | Array  | A transformation object that describes the transformation to apply. If this is given, the other arguments are ignored. |
| affine_transformation | Array  | A 4x4 or 3x4 matrix representing the affine transformation. Given as a numpy array. If this argument is given, the rotation and translation arguments are ignored. |
| rotation              | Array  | A 3x3 matrix specifying the rotation to apply    |
| translation           | Array  | A vector specifying the translation to apply |
| reduction_point       | Array  | A translation vector to apply before applying rotation and scaling. This is used to increase the numerical accuracy of transformation. If a transformation is given, this argument is ignored. |

### productmeta Object
| Field Name    | Type        | Description  |
|---------------|-------------|---------------------|
| product_name  | String      | e.g., m3c2, DEM, etc  |
| lastupdate    | String    | e.g., generated datetime   |
| param         | Object/Dict | e.g., product-related parameters   |
| derived_from  | Object/Link | e.g., the data source   |
| product_level | String      | e.g., [processing level](https://github.com/stac-extensions/processing?tab=readme-ov-file#suggested-processing-levels) |

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
