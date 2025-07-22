# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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

[Unreleased]: <https://github.com/stac-extensions/topo4d/compare/v1.0.0...HEAD>
