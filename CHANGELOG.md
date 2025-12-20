# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- This CHANGELOG file
- Githb workflow to test all new commits

## [0.2.0] - 2025-12-15

### Added

- README.md
- Implement AsRef for more complete transparent use of RcSubstring as &RcSubstring

### Changed

- `assert!` macros in `RcSubstring::new()` changed to `debug_asset!`s to improve efficiency in release. This means that this method will now only panic on invalid range inputs in dev builds. (It will almost certainly still panic downstream when the slice is done.)

## [0.1.0] - 2025-12-12

### Added

- Initial release (my first to crates.io!)

[unreleased]: https://github.com/chrisjhh/rcsubstring/compare/0.2.0...HEAD
[0.2.0]: https://github.com/chrisjhh/rcsubstring/compare/0.1.0...0.2.0
[0.1.0]: https://github.com/chrisjhh/rcsubstring/tree/0.1.0
