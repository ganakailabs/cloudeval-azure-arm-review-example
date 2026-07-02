# CloudEval Azure ARM Review Example

This repository is a public reference for CloudEval GitHub repository sync and GitHub Action pull-request review.

It shows how a real infrastructure repository can:

- keep nested ARM templates in source control,
- define the CloudEval visualization entry point in `.cloudeval/config.yaml`,
- sync through the CloudEval GitHub App,
- run `ganakailabs/cloudeval-action` on pull requests,
- receive a CloudEval bot review comment with posture, validation, cost, AI summary, report links, and review artifacts.

## Repository layout

```text
.
├── .cloudeval/config.yaml
├── .github/workflows/cloudeval-review.yml
├── azuredeploy.json
├── azuredeploy.parameters.json
└── nested/
    ├── compute.json
    ├── keyvault.json
    ├── monitoring.json
    ├── network.json
    └── storage.json
```

`azuredeploy.json` is the visualization source. It links the nested templates under `nested/`.

CloudEval imports these files, resolves the linked templates, builds the architecture graph, and runs reports from the resolved stack model while keeping source files read-only when synced from GitHub.

## Try this with CloudEval

1. Install the CloudEval GitHub App on this repository or your fork.
2. In CloudEval, create a project from the GitHub repository.
3. Choose branch `main` and source root `.`.
4. Confirm CloudEval detects `.cloudeval/config.yaml`.
5. Add these repository secrets for pull-request review:

```text
CLOUDEVAL_ACCESS_KEY
CLOUDEVAL_PROJECT_ID
```

The access key should be scoped to the CloudEval project and include project/report review permissions. If the project is linked to the CloudEval GitHub App and the key can comment through GitHub, PR comments appear from the CloudEval bot.

The workflow intentionally skips CloudEval review when those two secrets are not configured. This keeps forks and public demo branches green until you connect the repository to your own CloudEval project.

When `.cloudeval/config.yaml` enables `ci.review.outputs.pdf.enabled`, the workflow artifact includes `review/review.pdf` alongside `review/review.md` and `review/review.json`. The PR comment still keeps the CloudEval-hosted `PDF` badge, so reviewers get both a hosted download link and the exact PDF captured by the workflow run.

## Demo pull requests

The public repository keeps several pull requests open as examples:

| Pull request | What it demonstrates |
| --- | --- |
| [Security hardening](https://github.com/ganakailabs/cloudeval-azure-arm-review-example/pull/3) | Tightening network controls and improving posture. |
| [Risk regression](https://github.com/ganakailabs/cloudeval-azure-arm-review-example/pull/1) | A risky IaC change that should produce review warnings or failures. |
| [Cost regression](https://github.com/ganakailabs/cloudeval-azure-arm-review-example/pull/2) | Analytics capacity changes that should exceed the configured monthly budget gate. |

These PRs are intentionally left open so docs can link to live examples.

## Config gates

`.cloudeval/config.yaml` sets the review contract:

- `stacks[].entry` tells CloudEval which ARM file drives diagrams and reports.
- `resolve.linked_templates` tells CloudEval to follow relative `templateLink` files.
- `ci.gates.enforcement` controls whether failing gates fail CI or only warn.
- score thresholds define the minimum Well-Architected posture.
- `max_monthly_cost` gates estimated monthly cost.
- `ci.review.outputs.pdf` controls the optional PDF included in the GitHub Actions artifact bundle.

This branch intentionally proposes a larger analytics footprint so the review
comment can show a clear cost regression: large compute nodes, a scaled
PremiumV3 App Service plan, a fixed analytics worker fleet, a higher-capacity
WAF gateway, and geo-redundant hot storage should all be reviewed before merge.

## Safety notes

This repository is a review fixture, not a production deployment module. Values are placeholders and should be reviewed before deployment. Do not commit real tenant IDs, secrets, private keys, passwords, or production IP ranges.
