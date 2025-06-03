# Pc4d Extension Specification

- **Title:** Pc4d
- **Identifier:** <https://stac-extensions.github.io/pc4d/v1.0.0/schema.json>
- **Field Name Prefix:** pc4d
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions/README.md#extension-maturity):** Proposal
- **Owner**: @tum-rsa

This document explains the Pc4d Extension to the [SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.
This extension develops a standardized approach for handling time-dependent metadata in dense 3D time series (4D) point cloud datasets.

- Examples:
  - [Item example](examples/item.json): Shows the basic usage of the extension in a STAC Item
  - [Collection example](examples/collection.json): Shows the basic usage of the extension in a STAC Collection
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

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
| num_items           | Integer | Number of items                                                         |
| timestamp_list      | Array   | list of datetime                                                        |
| temporal_resolution | String  | Temporal resolution of multiple acquisiton (e.g., monthly, weekly, 1hr) |
| attribute_list      | Array   | List of attributes, e.g., [intensity, R, G, B, cls_label]               |

### Item Properties

| Field Name              | Type     | Description                                                                                                                                          |
|-------------------------|----------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| pc4d:sensor             | String   | e.g., Zenmuse L2, Camera                                                                                                                             |
| pc4d:native_crs         | String   | **REQUIRED.** The CRS of stored point clouds, e.g., projected CRS                                                                                    |
| pc4d:datetime           | datetime | **REQUIRED.** Acquired datetime                                                                                                                      |
| pc4d:tz                 | String   | Time zone, UTC?                                                                                                                                      |
| pc4d:acquisition_mode   | String   | e.g., ULS, UPH, TLS                                                                                                                                  |
| pc4d:duration           | Number   | unit: seconds                                                                                                                                        |
| pc4d:trajectory         | Array    | List of scan position (X, Y, Z).  **UAV only.**                                                                                                      |
| pc4d:scan_positions     | Array    | List of scan positions (X, Y, Z)                                                                                                                     |
| pc4d:orientation        | String   | e.g., nadir, oblique angles                                                                                                                          |
| pc4d:data_type          | String   | **REQUIRED.** e.g., lidar, image, text                                                                                                               |
| pc4d:point_count        | Integer  | Number of points                                                                                                                                     |
| pc4d:avg_point_density  | Number   | Avg. point density with given radius                                                                                                                 |
| pc4d:spatial_resolution | Number   | e.g., sampling interval                                                                                                                              |
| pc4d:measurement_error  | Number   | System error during measurement or sensor accuracy                                                                                                   |
| pc4d:global_transfo     | Array    | Global transformation matrix, e.g., offset                                                                                                           |
| pc4d:trafometa          | Object   | An object include, could be an object including all relevant meta infomation                                                                         |
| pc4d:product_info       | Object   | Exists if this item is a generated product. This object describe relavant meta information of the product e.g., parameters used to generate products |

### trafometa Object

| Field Name            | Type   | Description                                                                                                                                                                                    |
|-----------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| reference_epoch       | Link   | e.g., link to other item                                                                                                                                                                       |
| registration_error    | Number | Exists if it's a co-registered point cloud                                                                                                                                                     |
| affine_transformation | Array  | A 4x4 or 3x4 matrix representing the affine transformation. Given as a numpy array. If this argument is given, the rotation and translation arguments are ignored.                             |
| rotation              | Array  | A 3x3 matrix specifying the rotation to apply                                                                                                                                                  |
| translation           | Array  | A vector specifying the translation to apply                                                                                                                                                   |
| reduction_point       | Array  | A translation vector to apply before applying rotation and scaling. This is used to increase the numerical accuracy of transformation. If a transformation is given, this argument is ignored. |

### product_info Object
| Field Name    | Type        | Description                                                                                                            |
|---------------|-------------|------------------------------------------------------------------------------------------------------------------------|
| product_name  | String      | e.g., m3c2, DEM, etc                                                                                                   |
| lastupdate    | datetime    | e.g., generated datetime                                                                                               |
| param         | object/dict | e.g., product-related parameters                                                                                       |
| derived_from  | Link        | e.g., the data source                                                                                                  |
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
