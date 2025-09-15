# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).



## [v0.1.0]

The pre-release version for testing the accessibility and validation of this STAC extension hosted by https://github.com/tum-rsa.

### Changed

- `topo4d:tz` is replaced by `topo4d:timezone`
- Identifier: <https://stac-extensions.github.io/topo4d/v1.0.0/schema.json> is replaced by <https://github.com/tum-rsa/topo4d/v0.1.0/schema.json>


## [Unreleased]

### Added

- External asset references `trajectory` and `scan_positions` are stored as separate asset files
- Best Practices including **Field Usage Guidelines**, **Coordinate System Management** and **Asset Organization**

### Changed

- `topo4d:native_crs` is replaced by `proj:code`
- `topo4d:sensor` is replaced by `instruments`
- `topo4d:trajectory` and `topo4d:scan_positions` are replaced by external assets
- `topo4d:point_count` is replaced by `pc:count`
- `lastupdate` in the `topo4d:productmeta` is replaced by `timestamps:published`

### Deprecated

### Removed

`topo4d:native_crs`, `topo4d:sensor`, `topo4d:trajectory`, `topo4d:scan_positions`, `topo4d:point_count`

### Fixed

[v0.1.0]: <https://github.com/tum-rsa/topo4d/v0.1.0>
[Unreleased]: <https://github.com/tum-rsa/topo4d...HEAD>