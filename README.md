# gligabao docs

Documentation site for [gligabao](https://github.com/deepblue-phoenix/gligabao), built with [Mintlify](https://mintlify.com).

## Develop locally

```sh
npm i -g mint
mint dev          # http://localhost:3000
mint broken-links # check internal links
```

Navigation and theme are configured in `docs.json`. Deploy by installing the Mintlify GitHub app on this repository.

## Layout

- `index.mdx`, `getting-started/`: landing page and onboarding
- `concepts/`, `guides/`: how gligabao models and processes memory
- `api-reference/`: endpoints
- `evals/`: knowledge evals and ATIF trajectories, eval experiments, evals of trained models
- `embedding/`, `operations/`, `appendix/`: library use, operations, versioning, design notes
