# How Audits Work, from Management Assertions to Control Matrices

A single-page essay on how attestation audits actually function, using the WebPKI as the worked example. What a management assertion is, why the control matrix defines the scope of the audit, how one requirement gets traced from the TLS Baseline Requirements through ACME to an auditor conclusion, and why the root programs are pushing CAs toward discrete, testable CPS documents.

Part of the assurance series alongside [The Assurance Model Was Built for a World That No Longer Exists](https://rmhrisk.github.io/assurance-model/) and [Why Continuous Assurance Did Not Happen Until Now](https://rmhrisk.github.io/continuous-assurance/).

## Layout

- `index.html`, the complete essay. Self-contained, no build step, no external assets. All nine figures are inlined as SVG with per-figure scoped styles.
- `figs/`, the nine figures as standalone SVGs for reuse elsewhere.
- `source.md`, the prose source of the essay in markdown.

## Publishing

Push to a repository named `how-audits-work` (or anything you like), then enable GitHub Pages in Settings, Pages, deploying from the main branch root. The `.nojekyll` file is included so Pages serves the files as-is.
