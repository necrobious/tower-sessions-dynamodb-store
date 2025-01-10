# Changelog

All notable changes to this project will be documented in this file.

## [unreleased]

## [0.3.0] - 2025-01-09

### Fixed

- updated crate dependencies.


## [0.2.0] - 2024-05-08

### Added

- added `create()` on `DynamoDBStore`, on key conflicts will attempt to retry.
- added `create_key_max_retry_attempts` property to limit number of retries on `create()` key conflicts. The default is 5 retries.

### Fixed

- updated crate dependencies.
- minor updates to documentation.
- fixed rust fmt, clippy, rustdoc errors.


## [0.1.2] - 2023-12-27

### Fixed

- Updated crate dependencies. 


## [0.1.1] - 2023-12-27

### Fixed

- fixed error when publishing to crates.io: `the remote server responded with an error: expected at most 5 keywords per crate` 


## [0.1.0] - 2023-12-27

### Added

- Initial import from https://github.com/maxcountryman/tower-sessions/pull/123
- Example CloudFormation table template
- Change default table name to TowerSession to match CloudFormation example
- Examples and usage updates added to README
