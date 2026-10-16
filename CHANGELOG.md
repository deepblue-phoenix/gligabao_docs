# Changelog

All notable changes to the gligabao documentation site are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this site is versioned on its own with
[Semantic Versioning](https://semver.org/) (separately from `../gligabao`).

## [0.8.0] - 2026-10-26

### Added
- Training jobs page: "Training progress" (the training monitor, the admin SSE feed `GET /v1/gb/admin/api/training/events`, the training view of the admin UI), "Listing jobs and datasets" (`GET /v1/gb/admin/api/training/jobs` and `.../training/datasets`), and the `gb_training_job_events` table. Documents gligabao 0.28.0.
- Sidecar training API page: the progress events (status and progress, with their fields) and `GET /training/job_events`, the SSE stream the Go server follows.
- Training datasets page: "Choosing the dataset of a job".
- The Admin UI section of Configuration lists the views, including the new training view.

### Changed
- Training jobs: `job_id` is the sidecar's ID of the job and the new `provider_job_id` the provider's; the request and the `TrainingJob` have a `dataset`; the `gb_training_job_runs` table now tracks the job (`status` follows it, plus `dataset`, `last_step`, `last_loss`, `final_loss`, `error`, `finished_at`); a dataset the sidecar rejects is a `400`; the Go client has `StreamTrainingJobEvents` and `StatusError`.
- Training datasets: an upload is stored under its own `filename` (a plain `*.jsonl` name) instead of replacing the one default dataset.
- Training overview: the flow now includes how progress comes back; "Not tracked yet" lists what is still missing (jobs started with `modal run`, jobs whose container dies silently, cancelling).
- Running on Modal: `--dataset`, and `progress_events.py` in the code layout. Python sidecar page: the Go client's new method and `StatusError`.

## [0.7.0] - 2026-10-26

### Added
- A "Training" tab: a major section with its own navigation, `training/*`. Pages: Training (overview: the pieces, how a job flows from the admin API to the provider, a typical workflow, `sidecar_url`, what is not tracked yet), Running on Modal, Training jobs, Training datasets, Model sizes, Sidecar training API, and Providers and extending. Documents gligabao 0.27.0 (and the dataset endpoints and route renames of 0.26.1 and 0.26.2).
- Training jobs: the `TrainingJob` struct on the sidecar and Go sides, the admin endpoint `POST /v1/gb/admin/api/training/jobs` (body, answers and error statuses), the `gb_training_job_runs` table and the Go client's `StartTrainingJob`.
- Training datasets: the jsonl format and how it becomes Gemma turns, `TrainingDataset`, upload and list, and the Go client's `UploadTrainingDataset` and `ListTrainingDatasets`.
- Model sizes: the 4B/12B comparison, LoRA settings, memory, cost estimates, when to use which, and troubleshooting (from the gligabao `docs/training/MODEL_SIZES.md`).
- Sidecar training API: every endpoint of the sidecar web API, including the format of the progress entries.
- `sidecar_url` (env `SIDECAR_URL`) in the configuration table.

### Changed
- The "Remote training bootstrap" section of the Python sidecar page moved to the Training section: the code layout, `modal` commands and settings are in Running on Modal, the provider abstraction and the "Growing it" steps in Providers and extending, the two training paths in Model sizes. The sidecar page keeps a short "Remote training" section that links to them (the anchor `#remote-training-bootstrap` is gone).
- The sidecar page no longer says nothing in the server calls the sidecar; it lists the new Go client methods and the step of adding an admin handler.

## [0.6.0] - 2026-10-29

### Added
- The "Remote training bootstrap" section of the Python sidecar page now covers the full Gemma-4 fine-tuning pipeline: 4B (Unsloth, A10G) and 12B (plain transformers + peft + trl, A100) and why they train differently, the `sync`, `validate`, `train --model-size` and `generate` actions, `modal serve` and the web API endpoints, the per-size images, the dataset, local unit tests, and the new `SIDECAR_VOL_EVAL` and `SIDECAR_VOL_HF_CACHE` settings. Documents gligabao 0.26.0.

### Changed
- `SIDECAR_GPU` is documented as an optional override (defaults: `a10g` for 4b, `a100` for 12b), not the switch between CPU and GPU. The page intro and "Growing it" steps no longer describe the training as a stub.

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
