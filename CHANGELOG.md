# Changelog

All notable changes to the gligabao documentation site are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this site is versioned on its own with
[Semantic Versioning](https://semver.org/) (separately from `../gligabao`).

## [0.18.0] - 2026-10-23

### Changed
- Documents gligabao 0.47.0: training datasets are found by `dataset.toml` manifests in a training datasets directory (`bin/datasets_training/`, one subdirectory per dataset, with the dataset's generator script next to its data). The Training datasets page has a new section, **The training datasets directory and manifests** (the layout, discovery, every manifest key, the validity rules, problems, names, how to add a dataset), and describes the new list answer (`datasets` and `problems`), the preview's `name`, the record keyed by `<name>.jsonl`, and the upload as a dataset of its own (`409` on a clash). The anchor `#managing-datasets-go-server` is now `#endpoints-go-server`.
- Training jobs, overview, sidecar API and running pages: a job needs a dataset, named in the required `dataset` field (there is no default dataset any more); `modal run app.py --action sync|validate|train` take `--dataset <name>`, and `--action datasets` lists the local ones. The admin UI's training view lists the datasets and the ones that cannot be used.
- Go library and configuration pages: `TrainingDatasetDir` is the training datasets directory of manifests, default `datasets_training` next to `DBPath` (it was `<name>.training_datasets`).

### Removed
- The sidecar's default dataset and the bundled `finetune_data/` from the pages (the 325-example tool-calling dataset); the bundled dataset is now `world-knowledge-base`.

## [0.17.1] - 2026-10-23

### Fixed
- Datasets page: the training text uses Gemma 4's turn markers (`<|turn>user … <turn|> <|turn>model … <turn|>`), not Gemma 2/3's `<start_of_turn>` / `<end_of_turn>`. Documents gligabao 0.46.1, which fixes trained models that never stopped and ran to `max_tokens`: where the end-of-turn token comes from (the dataset file has no EOS), why the old markers failed, how `<bos>` is handled, and that adapters trained before 0.46.1 must be retrained. The Modal page's troubleshooting row for slow warm requests points to it.

## [0.17.0] - 2026-10-22

### Added
- Modal app lifecycle page: a **Cold and warm starts** section (`/training/modal#cold-and-warm-starts`): when a request is cold or warm, what happens in the GPU inference container on a cold request step by step (container start, models volume reload, cache check, adapter lookup, 4-bit load of the base model from the HF cache or a first download from Hugging Face, adapter attach, generation), what `load_seconds` includes, how to read the numbers in the admin UI's request history, and how to get fewer cold requests. Plus a troubleshooting row for slow warm requests. Documents gligabao 0.46.0.

### Changed
- Trained models page and sidecar API page: the answer of an inference has `input_tokens`, `output_tokens`, `cold_start` and `load_seconds`; the admin UI's history shows cold or warm, the model load time and the token counts for each request; the endpoint table describes the stored request. Links to the new section.
- Modal, trained models and running pages: the inference functions are named `infer`, `infer_4b`, `infer_12b` (as of gligabao 0.41.1), also in `modal run app.py --action infer`.

## [0.16.1] - 2026-10-20

### Changed
- Training pages (overview, running, jobs, sidecar API, Modal, trained models): sync with gligabao 0.44.0. Training progress now goes through an event log in a Modal Dict instead of the progress volume (no `/progress` volume, no `SIDECAR_VOL_PROGRESS`); a reader that was offline resumes with `from=<last id + 1>` and gets the events it missed; the new watchdog reports a job whose container died as `failed`; `GET /training/job_progress` needs `job_id`.

## [0.16.0] - 2026-11-02

### Added
- Model sizes page: an **Images** section (`/training/models#images`) listing the images of the Modal app and what each has, including the new inference-only image (`requirements_infer.txt`: `torch`, `transformers`, `peft`, `accelerate`, `bitsandbytes`; no Unsloth, slim base without the CUDA toolkit). Documents gligabao 0.40.0.

### Changed
- Training pages: inference no longer runs in the Unsloth training image; it loads the base model 4-bit and attaches the adapter with `transformers` + `peft` (models, running on Modal, provider and trained models pages, and the Modal app lifecycle page).

## [0.15.0] - 2026-11-01

### Added
- Modal app lifecycle page: a **Warm inference containers** section (`/training/modal#warm-inference-containers`): the loaded model stays in the GPU container, which stays up for `SIDECAR_INFER_IDLE_TIMEOUT_S` (300 seconds by default) after its last request; one model per container; the **Stop inference** button and its `stop_inference` action; how the containers are found (the `<app name>-inference` Modal Dict); redeploying after changing the timeout. Also the `inference` list of the apps endpoint, the warm-containers badge of the apps list, and troubleshooting rows. Documents gligabao 0.39.0.
- Running on Modal: the `SIDECAR_INFER_IDLE_TIMEOUT_S` setting.

### Changed
- Trained models page and sidecar API page: a request no longer loads the model every time (only a cold one does); `POST /infer/query` answers with `cold_start` and `load_seconds`, and a failing reload of the models volume no longer fails the request.

## [0.14.0] - 2026-10-31

### Added
- A **Trained models** page (`/training/trained-models`, in Jobs and data): where a job's weights are saved (the models volume, `trained/<job_id>`, what is in it, what is not: the base model, who writes and reads it, how to inspect, fetch and remove it), how a completed job becomes a `gb_trained_models` row, how an inference request reaches the weights (and its limits: the model is loaded on every request, 600 second Modal timeout, `model_size` must match), and how to add an inference endpoint (a Go endpoint, a sidecar route, a GPU generation function).
- Training pages: a "Trained models" section (`/training/jobs#trained-models`) for the new `gb_trained_models` table (one row per completed job: the weights it saved, with the dataset, steps, settings and files), the `model` of the `completed` event, the endpoints `GET /v1/gb/training/models`, `GET /v1/gb/training/models/{id}` and `POST /v1/gb/training/models/{id}/infer`, and the `Infer` Go client call. Documents gligabao 0.38.0.

### Changed
- Training pages: each job saves its adapter in `/models/trained/<job_id>` instead of overwriting `/models/finetuned_model_<size>`; `POST /infer/query` takes `model_path` to pick the weights.

## [0.13.0] - 2026-10-30

### Added
- Training pages: stopping a job (`POST /v1/gb/training/jobs/{id}/stop`, the sidecar's `POST /training/job_cancel`, `CancelTrainingJob`), the `cancelled` status and the `log` events. Documents gligabao 0.37.0.

### Changed
- Training pages: the trainer logs every step (`logging_steps=1`), and `cancelled` ends a job like `completed` and `failed`.

## [0.12.1] - 2026-10-29

### Changed
- Models and Running on Modal pages: the 12B model now trains and serves through Unsloth, in the same image as 4B (the plain transformers path is gone). Documents gligabao 0.36.0.

## [0.12.0] - 2026-10-29

### Added
- Modal app lifecycle page: a section "The apps list" for the admin UI's list of the active Modal apps of this system (`SIDECAR_APP_NAME` and `SIDECAR_APP_NAME-<suffix>`), with the per-app buttons Redeploy / Deploy, Last log, Stop and Volumes, plus the new routes (`GET /backends/{name}/apps`, `/backends/{name}/apps/{app}/actions/...`), curl examples and troubleshooting rows. Documents gligabao 0.35.0.

### Changed
- Modal app lifecycle and Python sidecar pages: the Deploy / Stop / Volumes buttons are described on the app rows instead of the backend line; route tables and the backend-extension note (`apps()`, `app_actions()`) updated.

## [0.11.0] - 2026-10-28

### Added
- Training page "Modal app lifecycle" (`/training/modal`, in the Training navigation): how the sidecar manages the Modal app without the `modal` CLI. The admin UI's backend badges (healthy, app deployed or not), Deploy and its stages, the live log (SSE, reconnecting, follow), Cancel, Stop (and what it does to running jobs), Volumes, the sidecar and server routes with the action state, where the state lives (Modal, not `~/.modal.toml`, the sidecar's memory), the container, troubleshooting and what is not included. Documents gligabao 0.34.0.

### Changed
- Python sidecar page: the "Backend actions" section is now "Backend actions and Modal lifecycle" with the full route table (`GET /backends`, actions, cancel, events, volumes) and the server's admin routes; the container copies `ai_train/` (the code a deploy uploads), so the page no longer says it is not copied.
- Running on Modal, Training overview (workflow, configuration, section list), Providers, Sidecar training API and Configuration pages: they point to the new page and name the admin UI as a way to deploy besides `modal deploy app.py`.

## [0.10.0] - 2026-10-26

### Added
- Training datasets page: "Managing datasets (Go server)" (`POST` and `GET /v1/gb/training/datasets`, the dataset directory and `serve --training-dataset-dir`, the `gb_training_datasets` record and its `stored` / `synced` status), "How a dataset reaches the sidecar" (inside `POST /training/job_start` as `dataset_content`) and the Python `TrainingDataset` model as the sidecar's internal way to load and pass a dataset around. Documents gligabao 0.30.0 and 0.31.0.

### Changed
- The sidecar has no dataset endpoints: `POST /datasets/upload` and `GET /datasets/list` are gone from the Training overview, Training datasets, Sidecar training API and Python sidecar pages. Datasets are managed by the Go server alone; the sidecar and the Modal app only store a dataset internally when a job needs it. The Go client has no dataset methods; `StartTrainingJobRequest` has `DatasetContent`.
- Training pages now use the training API paths under `/v1/gb/training/` (jobs, datasets, events), which are served whether or not the admin UI is on, instead of `/v1/gb/admin/api/training/...`, and the code now lives in `internal/api/training/`. A job on a dataset the server has no record of is a `400`.
- `GET /v1/gb/training/datasets` lists the datasets from the database instead of asking the sidecar.
- Go library page: `ServeOptions.TrainingDatasetDir`. The training view of the admin UI uploads datasets.

## [0.9.0] - 2026-10-26

### Added
- Python sidecar page: "Backends" (the sidecar forwards every route unchanged to a pluggable backend, `502` when it cannot be reached, the `modal` backend, `modal deploy` versus `modal serve` with `SIDECAR_MODAL_WEB_URL`, adding a backend), "Configuration" (`SIDECAR_BACKEND`, `SIDECAR_APP_NAME`, `SIDECAR_MODAL_WEB_URL`, `MODAL_TOKEN_ID`, `MODAL_TOKEN_SECRET`, `SidecarConfig`), and "Startup check and status" (the one-time health check, `server.SidecarActive`, `GET /v1/gb/admin/api/sidecar/status`, the admin UI's sidecar badge). Documents gligabao 0.29.0 to 0.29.3.

### Changed
- The docs now say that the Go server only ever talks to the sidecar and the sidecar proxies to Modal: the Go code has no Modal client, token or URL. Updated on the Python sidecar page, the Training overview (pieces table, job-start and progress diagrams, `sidecar_url`), Sidecar training API (two layers: the sidecar's routes and the Modal web API behind them; `/healthz` is Modal's, `/health` the sidecar's), Running on Modal (the app is the sidecar's backend; `modal deploy`; the sidecar uses Modal tokens), Training jobs (`502` and `503` meaning), Configuration and Quickstart.
- Python sidecar page: the service is no longer described as a skeleton; the container needs the Modal tokens, copies `routes.py`, `backends/` and `sidecar_config.py`, and the "Adding an endpoint" steps now start with the route table and the backend.
- The `503` of the training endpoints now reads "sidecar is not active" (the sidecar failed its health check at startup).

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
- A "Remote training bootstrap" section on the Python sidecar page: the `sidecar/ai_train/` layout, how to run it with `modal run`, the `SIDECAR_*` settings, how the provider abstraction works and how to add a provider such as AWS. Documents gligabao 0.25.0.

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
