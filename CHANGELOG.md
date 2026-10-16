# Changelog

All notable changes to the gligabao documentation site are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this site is versioned on its own with
[Semantic Versioning](https://semver.org/) (separately from `../gligabao`).

## [0.5.0] - 2026-10-28

### Added
- A "Remote training bootstrap" section on the Python sidecar page: the `side_car/ai_train/` layout, how to run it with `modal run`, the `SIDECAR_*` settings, how the provider abstraction works and how to add a provider such as AWS. Documents gligabao 0.25.0.

## [0.4.0] - 2026-10-27

### Added
- A "With docker compose" section on the Python sidecar page: `docker compose up --build` runs the server and the sidecar on one network, the server reaches the sidecar at `http://sidecar:8787`, ports are published on `127.0.0.1` only and data lives in the `gligabao-data` volume. Documents gligabao 0.24.1.

### Changed
- The Quickstart has a tip for running with docker compose, and the Building section of Building and versioning mentions `docker compose up --build`.

## [0.3.0] - 2026-10-26

### Added
- A Python sidecar page (`operations/sidecar`): the FastAPI service, its configuration, its container and build script, the Go client and how to add an endpoint. Documents gligabao 0.24.0. Added to the Operations group in the navigation.

### Changed
- The Building section of Building and versioning mentions `./run_build_sidecar.sh`.

## [0.2.0] - 2026-10-25

### Added
- A Timeseries guide (`guides/timeseries`): supplying, fetching, stats, observations and reading series, linking to the API reference for details.

### Changed
- The home page Timeseries card now links to the guide; the API reference links to it too.

## [0.1.0] - 2026-10-24

### Added
- First version of the Mintlify site, covering gligabao 0.23.1: getting started, concepts, guides, API reference, embedding in Go, operations and appendix.

### Changed
- Brought the site in line with `../gligabao/docs` and `README.md`: replaced stale `timeseries.md` references, and added the eval `trajectory_id`, admin UI and endpoint overview details.
