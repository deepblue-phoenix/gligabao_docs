# Changelog

All notable changes to the gligabao documentation site are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this site is versioned on its own with
[Semantic Versioning](https://semver.org/) (separately from `../gligabao`).

## [0.43.0] - 2026-11-02

### Added
- Guides / Research agents: a new page that documents gligabao 0.128.0 and 0.128.1: starting a deep-research agent on a topic (`POST /v1/gb/research`, the MCP tool `research_start`, `gligabao research start`), following it (`GET /v1/gb/research` and `/{id}`, `research_list`, `research_get`, `research ls|get`), the runs and the states stored in the new tables `gb_agent_research_runs` and `gb_agent_research_states`, the limit of 3 agents working at once per user, the cost, and how the agents are run. Added to the Guides group in the navigation.
- Guides / The deep-research agent: a new page about the agent itself (gligabao 0.127.0 to 0.128.1): its tool-calling loop, its prompt, the tools `web_search` and `fetch_url`, how every agent is isolated (a goroutine, one struct and an HTTP client of its own), its limits and what a run costs.
- API reference / Endpoints overview: the three research endpoints.
- Guides / MCP and Guides / CLI: the tools `research_start`, `research_list` and `research_get`, and the commands `research start`, `research ls` and `research get`.
- Getting started / Configuration: the `mcp` setting says that the research tools are served too.
- Home page: a card for the research agents.

## [0.42.0] - 2026-10-30

### Added
- Guides / CLI: a new page that documents gligabao 0.126.0: the commands `remember`, `query`, `dataset`, `api`, `status` and `stop`, the local daemon the first command starts (on `127.0.0.1`, without authentication), where its config and data live (`~/.gligabao`), `--server`, `--user` and `--json`, the JSON output and the exit codes. Linked from the home page, the Quickstart (a new step 5) and the Query page; added to the Guides group in the navigation.
- API reference / Query: the new request field `evidence_only` and a new section, "Retrieval only", for a call that retrieves the evidence without asking the LLM to write the answer.
- Getting started / Configuration: the new setting `host` (and `serve --host`), the address to listen on.

## [0.41.0] - 2026-10-29

### Added
- Embedding & ops / Pipeline: a new page that documents gligabao 0.124.0 and 0.125.0, the pattern that `remember` and `query` share: the list of stages, the `Fatal` and `Advisory` policies, `Stop` and `Each`, what the runner tells the run log, how inferences are filed under their stage, the run log, the admin views, and how to add a stage or an operation. Linked from the Remember, Query and API overview pages.
- API reference / Query: a new section, "Run log": every `query` call is recorded in the new table `gb_query_runs` (the question, the status, and for each stage `retrieve_graph`, `detect_geo`, `nearby_geo` and `query` its status, error, times and inferences), the new response field `query_run_id`, and `GET /v1/gb/admin/api/query-runs`. A `query` call is also described as a pipeline of four stages.
- API reference / Remember: a new section, "How the pipeline is declared", and the new stage status `skipped` in the Run log (a stage the call does not use; a run that succeeded has no stage left `not_started`).
- Admin UI: the **query** view, the same as the **remember** view over the runs of `query` (and `query_runs` among the slots of the admin extensions).

### Changed
- The code locations that moved with the pipeline refactor: the pipeline of `remember` is in `internal/memory/memory_remember/remember_pipeline.go` (Chunking), the code of `query` is in `internal/memory/memory_query/query.go`.
- Operations / Inferences: the run log of `query` also lists the inferences of each stage.
- Embedding / Remember manager: a queued run has every stage `not_started` until the pipeline passes it.
- Schema: `gb_remember_runs` accepts `skipped` for a stage and `gb_query_runs` is new; there is no migration, so an existing database has to be recreated.

## [0.40.0] - 2026-10-29

### Added
- API reference / Remember: a new section, "Stage outputs", that documents gligabao 0.122.0: what each stage of a run produced is kept for debugging in the `gb_remember_run_stage_outputs` table (columns, the 64 KiB limit, the `name` and `label` of each step, and the output of every stage), and the new row of that table in the Schema section.
- API reference / Remember, Admin UI: the **Outputs** button of a stage (the outputs open in a modal, as colour coded JSON), the **?** button that explains each stage (gligabao 0.123.0) and the new endpoint `GET /v1/gb/admin/api/remember-run-outputs`.
- Evals: the evals are read from the directories of `evals_knowledge_dirs` (more than one, `-evals` overrides them), and the `text` of a `remember` entry can be a list of strings (gligabao 0.121.0).

### Changed
- Documents gligabao 0.123.1: the Go packages moved to `internal/memory/memory_geo` (was `internal/geo`) and `internal/memory/memory_chunk` (was `internal/chunk`), so the Go examples of the Geo and Chunking guides use `memory_geo.` and `memory_chunk.`.
- Home: the description says "Minimal Knowledge engine for AI".
- Evals: the runner is `go run ./internal/evals/knowledge/cmd`.

## [0.39.0] - 2026-10-28

### Added
- API reference / Query: documents gligabao 0.120.0 and 0.120.1. The retrieval is now in two steps, keywords and similarity: the question is embedded and searched in the entity, edge, chunk and summary vector indexes (cosine similarity of at least 0.75), similar entities join the seeds, similar facts are added with their entities, and the chunks of long texts that are close to the question become passages that are given to the LLM (the best three with the text of the chunk). New response field `evidence.passages` (`artifact_id`, `parent_id`, `index`, `summary`, `text`), the `passages` the LLM cites, a new `502` cause (the question could not be embedded), and the requirement of `OPENAI_API_KEY`.
- API reference / Query: a new section, "Facts over time and history": only live facts are retrieved (a fact that newer knowledge superseded, with an `expired_at`, never is, however it is found, though its vector stays in the index), a fact that ended without being superseded (`invalid_at` only) is given with it, and what that means for questions about the past.
- Guides / Vector storage: a new section, "What `query` reads".
- Evals: the query trajectory has a new `search_vectors` step (the embedding of the question and the matches of each index) before `retrieve_graph`, which now also holds the passages; the example is updated.

### Changed
- `OPENAI_API_KEY` (`openai_api_key`) is documented as required, not optional: the server does not start without it, because `query` embeds the question as `remember` embeds what it stores. Getting started (Quickstart, Configuration), API reference / Remember, Vector storage.
- Getting started / How it works, the home page, Concepts / Temporal facts, Artifacts and Datasets, Guides / Chunking, Operations / Inferences (the stages of `query`): `query` is no longer described as keyword-based, as not reading the vectors, or as not using artifact text.
- API reference / Query: the Request section is in sync with gligabao again (`reasoning` per stage, `models`).

## [0.38.0] - 2026-10-27

### Added
- Guides / Geo: a new section, "Nearby entities in `query`", documents gligabao 0.117.0: the nearby search is off by default and a request turns it on (`geo_nearby`, or `geo_radius_km`); how the places are found; that a place of the question the resolver finds is stored as an entity with coordinates, so later questions find it in the database; the `nearby` field of the response; and how the radius is set (50 km by default; `geo_radius_km` in the config, or in the request).
- API reference / Query: the request fields `geo_nearby` and `geo_radius_km`. Getting started / Configuration: the setting `geo_radius_km`.

### Changed
- Guides / Geo: `query` is no longer described as not using geo.

## [0.37.0] - 2026-10-27

### Added
- Guides / MCP: a new page, `/guides/mcp`, for gligabao 0.111.0's MCP server: turning it on (`mcp = true`, `serve --mcp`), the endpoint `/v1/gb/mcp` (streamable HTTP, stateless), how to add it to Claude Code and to a JSON client config, the arguments of the `remember` and `query` tools, how results and errors are returned, the missing authentication and what is not included yet.

### Changed
- The home page describes gligabao as an MCP/HTTP server and has an MCP card.
- Configuration (`mcp`), API overview (`/v1/gb/mcp`), Identity (the MCP tools take any `user_id`), Go library (`MountOptions.Wrap` covers the MCP endpoint) and How it works mention MCP.

## [0.36.0] - 2026-10-27

### Added
- Guides / Geo: a new page, `/guides/geo`, for gligabao 0.110.0's geo stage of `remember`: turning it on (`geo = true`, `serve --geo`, `ServeOptions.GeoResolver`), what the stage does (candidates, the `detect_geo` LLM call, the resolver, storage), the `geo` list of the response, the `gb_entity_geo` table, the steps in an eval trajectory and how to write a resolver.

### Changed
- Remember: the pipeline has a geo step (4c), the `geo` field of the response, the `detect_geo` stage in the models, reasoning and ignored-stages lists, the `resolve_geo` stage of the run log and the `gb_entity_geo` table.
- Configuration (`geo`), Go library (`GeoResolver`), Inferences (`detect_geo` stage), How it works, API overview, the home page and Evals (the geo trajectory steps and the `detect_geo` reasoning level) mention geo.
- Embedding pages: the paths of `EmbedMount` and `systemEventHook` follow the move into `gligabao_int/gligabao_int_embed` and `gligabao_int/gligabao_int/options.go`.

## [0.35.1] - 2026-10-26

### Changed
- Documents gligabao 0.109.0's move of `bin/` to `build/`: the paths of the config file, databases, binary, datasets, evals and ontology in the getting started, concepts, training, evals, operations and API reference pages.

## [0.35.0] - 2026-11-07

### Added
- Operations / PyPI packages: documents gligabao 0.108.0's two pip packages, `gligabao` (the client) and `gligabao-server` (the binary and its default config, one wheel per platform), and `run_pypi.sh` (build flags per platform, `--arch`, `--upload`). The Building section of Building and versioning links to it.

## [0.34.0] - 2026-11-05

### Changed
- `recall` is now **`query`**, for gligabao 0.106.0: the page moved from `/api-reference/recall` to `/api-reference/query` (the old link redirects), and every page that mentions it uses the new names: `POST /v1/gb/query` and `/v1/gb/eval/query`, `memory_query.Query`, the `query.completed` / `query.failed` events, the `query` stage and type of inferences, the `query` key of the eval `reasoning` and the eval database columns `query_calls` and `query_cost_usd`. "Precision over recall" is unchanged.

## [0.33.0] - 2026-11-04

### Changed
- *Model exporting* is now **Model deploying**, for gligabao 0.105.0: the page moved from `/training/exporting` to `/training/deploying` (old links redirect), and the pages that mention it (*Hooks*, *Go library*, *Trained models*, *Sidecar API*, *Overview*) use the new names: the deployer hook (`TrainedModelDeployers`), the `/deployments` and `/deployers` endpoints, `trained_model_deploy_dir`, `gb_trained_model_deployments` and the admin UI's **Deploy** dialog and **Deployers** block.


## [0.32.0] - 2026-11-02

### Added
- *Embedding in Go programs*, for gligabao 0.102.0: `Mount` in the API list and a new section, **Mounting on your own HTTP server**: registering gligabao's handlers (`/v1/gb/`) on the host program's `*http.ServeMux` instead of starting a server on its own port, `MountOptions` (`Mux`, `Wrap`), what is and is not registered, `release`, and the warning that gligabao has no access control of its own.

## [0.31.0] - 2026-10-29

### Added
- *Model exporting* and *Hooks*, for gligabao 0.101.0: the optional `Description` of a `TrainedModelExporter` (shown in the admin UI), the new `GET /v1/gb/training/exporters` endpoint, and a short section on the admin UI's **Export** dialog and **Exporters** block in the training view.

## [0.30.0] - 2026-10-25

### Added
- A new page, **Admin UI extensions** (`/embedding/admin-extensions`, in Embedding & ops > Embedding in Go), for gligabao 0.97.0: how a program that embeds gligabao adds its own views (nav buttons) and buttons, modals and blocks inside the existing views of the admin UI with plain JavaScript and no build step: a quick start, registering an extension (`ServeOptions.AdminExtensions`), the `gb` API, views, the slots, a Go backend for the extension, styling, how loading works, developing, and the caveats (trusted code, no access control, versioning).
- *Hooks*, *Embedding in Go* and *Configuration* (Admin UI): link to the new page; *Embedding in Go* lists `AdminExtensions` among the `ServeOptions`.

## [0.29.0] - 2026-10-24

### Added
- A new page, **Model exporting** (`/training/exporting`, in Training > Jobs and data), for gligabao 0.96.0 and 0.96.1: how exporting a trained model works inside (the flow of an export request, fetching and verifying the weights, the `gb_trained_model_exports` record, the two endpoints and their statuses, system events, limits) and the contract of the exporter hook.
- *Hooks*: a *Trained model exporters* section and its row in the table of hooks.
- *Sidecar training API*: the `GET /models/file` endpoint that streams a model's files.

### Changed
- *Trained models*, *Training overview* and *Embedding in Go*: link to the new page; the endpoints table lists the export endpoints, and `ServeOptions.TrainedModelExportDir` / `--trained-model-export-dir` and `TrainedModelExporters` are described.

## [0.28.4] - 2026-10-24

### Changed
- Documents gligabao 0.95.0: Gemma 4 31B (`31b`, `google/gemma-4-31B-it`, A100 80 GB) as a third model size, in *Model sizes*, *Running on Modal*, *Training jobs*, *Trained models*, the sidecar API page, the training overview and the Eval page.

## [0.28.3] - 2026-10-23

### Changed
- Documents gligabao 0.89.0: the default of `SIDECAR_INFER_IDLE_TIMEOUT_S` (how long a GPU inference container stays up after its last request) is 60 seconds instead of 300, in *Running on Modal*, *Trained models* and the sidecar API page.

## [0.28.2] - 2026-10-19

### Fixed
- Documents gligabao 0.66.0: *Remember* no longer lists the placeholder table `gb_items`, which was removed.
- Documents gligabao 0.65.0: *Ontologies* points to the bundled ontology at `build/ontology/market.owl` (it was `ontology/market.owl`), in the examples and the link.

## [0.28.1] - 2026-10-19

### Fixed
- Documents gligabao 0.63.1: the evals are the files of `build/evals_knowledge/` and `build/evals_training/`, read from disk (`serve --training-eval-dir`), not `evals/*/evals/` embedded in the binary. Fixed in *Evals of trained models*, *Training data audit*, *Experiments* and the web search guide; `GET /eval-definitions` now says it lists the files of the evals directory.

## [0.28.0] - 2026-10-19

### Changed
- Documents gligabao 0.61.0: the Eval button of a trained model runs real evals, and evals and audits share their types and JSON shapes. *Evals of trained models* now describes the evals (`evals/training/evals/*.json`, named after the training dataset they are about, with named cases the model never saw), the new `eval` and `cases` fields of `POST /models/{id}/evals` (no more `scheme`), `GET /eval-definitions`, that the command-line runner stores its runs, and that `case_name` of a training case is the case's name. *Training data audit* and the two pages' tables describe the shared run and case types (`TrainedModelRun` with a `kind`, `TrainedModelCase`), the `{"run", "cases"}` detail and SSE events of both, `run_id` in a case, and the judge call types `training_eval_judge` and `training_audit_judge`.

## [0.27.0] - 2026-10-19

### Changed
- Documents gligabao 0.60.0: `failed` is only a verdict about checks (knowledge evals), and `error` means a case or run could not be run to the end, also for evals and audits of trained models (run: `running`, then `completed`, `error` or `cancelled`; case: `judged` or `error`; counter `num_errored`). The totals of a run are one count per case status (`passed_cases`, `failed_cases`, `error_cases`, `judged_cases`) instead of a `passed_cases` that counted judged cases for training and a `failed_cases` that held all the others. *Experiments*, *Evals of trained models* and *Training data audit* say so.

## [0.26.1] - 2026-10-19

### Changed
- Documents gligabao 0.59.0: evals and audits of trained models use the same statuses. A run is `running`, then `completed`, `failed` or `cancelled` (an eval used to end `passed`), and a case is `judged` or `failed` (an eval's case used to be `passed`); `passed_cases` of a training run counts the `judged` cases. *Evals of trained models*, *Experiments* and *Training data audit* say so; the audit page's comparison table has a row for the statuses.

## [0.26.0] - 2026-10-19

### Added
- New page **Training data audit** (`training/audit`, in the Training section), documenting gligabao 0.58.0: the **Audit** button of a trained model asks it the questions of its own fine-tuning dataset and an LLM judges the answers, to check that the training took and to find bad training data. It is not an eval and says nothing about generalization; it has its own endpoints (`/v1/gb/training/models/{id}/audits`, `/audits/{id}`, `/audits/{id}/events`, `/audits/{id}/cancel`), tables (`gb_training_audit_runs`, `gb_training_audit_cases`) and Go types (`TrainedModelAuditRun`, `TrainedModelAuditCase`).

### Changed
- *Evals of trained models* has a note that a model's own dataset is an audit, not an eval, and that the `finetune_dataset` scheme stays until evals have cases of their own. *Trained models* and *Training datasets* point to the audit.

## [0.25.3] - 2026-10-19

### Changed
- Documents gligabao 0.57.0: a training eval has one case result per example, like a knowledge eval has one per case (`gb_experiment_case_results`), with the per-case columns in `gb_training_case_meta` (replacing `gb_training_case_results`); the eval run is the run (its ID, status and times), with `judge_cost_usd` and `error` in `gb_training_eval_run_meta`. Updated *Eval experiments*, *Evals of trained models* and *Trained models*.

## [0.25.2] - 2026-10-19

### Changed
- Documents gligabao 0.56.0's terminology: an eval is a set of cases (it was an "eval suite"), and a run of an eval against a trained model is an eval run; "suite" is gone. Updated *Evals*, *Eval experiments*, *Evals of trained models*, *Trained models*, *Web search* and the quickstart: the `eval` column of `gb_experiment_cases` / `gb_experiment_case_results` (was `suite`), `gb_training_eval_run_meta` (was `gb_training_suite_run_meta`), `eval_run_id` (was `suite_run_id`), `TrainedModelEvalRun`, the history endpoint's `?eval=..&name=..`, and the runner's `-evals` / `-eval` / `-list-evals` flags.


## [0.25.1] - 2026-10-19

### Changed
- Documents gligabao 0.55.0's rename: the table of the outcome of a case in a run is `gb_experiment_case_results` (was `gb_experiment_results`), its Go type is `ExperimentCaseResult`, a run's `Results` are `CaseResults`, and the graph endpoint is `GET /case_results/{id}/graph`. Updated *Eval experiments* and *Evals of trained models*, and the table name on *Trained models*.

## [0.25.0] - 2026-10-19

### Changed
- Documents gligabao 0.53.0's eval terminology: an **eval case** is one input/output combination (plus metadata) to be evaluated, and cases are part of an **eval suite**. *Evals and ATIF trajectories* and *Eval experiments* say case where they said eval for one case of a suite (tables `gb_experiment_cases`, `gb_knowledge_case_meta`, `case_name`, `total_cases`, ...), and add the terms.
- *Evals of trained models*: an eval is a run of a suite (the model's fine-tuning dataset, each example a case) against the model; the page describes the `finetune_dataset` scheme and the new `scheme` field, the `cases` (were `items`) of the API, and the renamed tables (`gb_training_suite_run_meta`, `gb_training_case_results`).

## [0.24.0] - 2026-10-26

### Changed
- **Eval tables.** The *Eval experiments* and *Evals of trained models* pages describe the new, unified eval tables: knowledge and training evals share `gb_experiments`, `gb_experiment_evals`, `gb_experiment_runs` and `gb_experiment_run_evals`, each with a `type` column (`knowledge` or `training`), and keep their own columns in `gb_knowledge_eval_meta` and `gb_training_eval_meta` / `gb_training_eval_items`. They replace `gb_trained_model_evals`, `gb_trained_model_eval_items`, `gb_training_experiments` and `gb_training_experiment_runs`. The table for the outcome of an eval in a run is `gb_experiment_results` (a short *Terms* note defines eval, suite, run, result and experiment). Suites are described for both kinds (training evals have none yet). The *Trained models* page links to them.

## [0.23.0] - 2026-10-26

### Changed
- The **Training** tab is merged into the **Documentation** tab as a **Training** group, with its three groups (Getting started, Jobs and data, Internals) nested in it. The tab is gone from the top navigation; the page URLs are unchanged.

## [0.22.0] - 2026-10-26

### Changed
- The three eval pages now live in a new `evals/` directory: `/operations/evals` is `/evals/evals`, `/operations/experiments` is `/evals/experiments` and `/training/evals` is `/evals/training`. The old URLs redirect to the new ones, and all internal links point to the new URLs.

## [0.21.0] - 2026-10-26

### Changed
- The eval pages (**Evals and ATIF trajectories**, **Eval experiments**, **Evals of trained models**) are now together in a new **Evals** group of the Documentation tab. They were spread over the Training tab and the Embedding & ops tab.

## [0.20.0] - 2026-10-25

### Added
- Documents gligabao 0.51.0: the **Evals of trained models** page has a new section, **Experiments**: an eval started with `experiment` becomes a run of a named training experiment, what a run shows (the eval as it is now, the trained model evaluated, mean score and share of correct answers), the endpoints `GET /v1/gb/training/experiments` and `GET /v1/gb/training/experiments/{id}`, and the tables `gb_training_experiments` and `gb_training_experiment_runs`, which are separate from the knowledge-extraction experiments.

## [0.19.0] - 2026-10-24

### Added
- Documents gligabao 0.49.0 and 0.49.1: a new page, **Evals of trained models** (`/training/evals`), on the **Eval** button of a trained model in the admin UI. It covers what an eval does (the model is asked the questions of its training dataset through the sidecar, an LLM judges the answers from 0 to 5 in batches of 10 by default), the score, how the judge works (the rubric, retries, when a run gives up), the endpoints (`POST /v1/gb/training/models/{id}/evals`, `GET .../evals`, `GET /v1/gb/training/evals/{id}`, `POST .../cancel`) and the `gb_trained_model_evals` / `gb_trained_model_eval_items` tables.
- A section on **why the trained model is asked one question per prompt**: Gemma 4 4B and 12B were fine-tuned on single turns, so several questions in one prompt are a shape they never saw, interfere with each other at that size, share one output budget and make the scores depend on the batching. It also says what is not the same (batching at the inference engine, parallel calls) and why batching the judge's items is different.

### Changed
- The knowledge-extraction runner moved to `evals/knowledge` in gligabao 0.49.0: the Evals, Eval experiments, Web search, Remember, Configuration pages now say `go run ./evals/knowledge` and link `evals/knowledge/README.md` and `evals/knowledge/suites/`. The Evals page points to the new page for the other eval system.
- Trained models, Training datasets and Training overview pages link to the new page (the Eval button, its endpoints and tables; a dataset must stay in the training datasets directory to be evaluated on).

## [0.18.0] - 2026-10-23

### Changed
- Documents gligabao 0.47.0: training datasets are found by `dataset.toml` manifests in a training datasets directory (`build/datasets_training/`, one subdirectory per dataset, with the dataset's generator script next to its data). The Training datasets page has a new section, **The training datasets directory and manifests** (the layout, discovery, every manifest key, the validity rules, problems, names, how to add a dataset), and describes the new list answer (`datasets` and `problems`), the preview's `name`, the record keyed by `<name>.jsonl`, and the upload as a dataset of its own (`409` on a clash). The anchor `#managing-datasets-go-server` is now `#endpoints-go-server`.
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
