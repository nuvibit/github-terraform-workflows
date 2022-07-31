# GitHub Actions: reusable terraform workflows

<!-- LOGO -->
<a href="https://nuvibit.com">
    <img src="https://nuvibit.com/images/logo/logo-nuvibit-square.png" alt="logo_nuvibit" title="nuvibit" align="right" width="100" />
</a>

<!-- SHIELDS -->
[![Maintained by nuvibit.com][nuvibit-shield]][nuvibit-url]
[![Latest Release][release-shield]][release-url]

<!-- DESCRIPTION -->
Reusable [GitHub Workflows][github_workflows_link] for [Terraform Enterprise or Cloud][tfe_intro].
<br><br>

## Quick Start

To get started add github-terraform-workflows to an existing GitHub workflow:

[Terraform Workspace Workflow (Standard)](#terraform-workspace-workflow-standard) \
[Terraform Workspace Workflow (Minimal)](#terraform-workspace-workflow-minimal) \
[Terraform Module Workflow (Standard)](#terraform-module-workflow-standard) \
[Terraform Module Workflow (Matrix)](#terraform-module-workflow-matrix) \
<br>

## Actions

The reusable Github Workflows include the following public Github Actions:

- [`@actions/checkout`](https://github.com/actions/checkout)
- [`@actions/setup-go`](https://github.com/actions/setup-go)
- [`@ad-m/github-push-action`](https://github.com/ad-m/github-push-action)
- [`@cycjimmy/semantic-release-action`](https://github.com/cycjimmy/semantic-release-action)
- [`@hashicorp/setup-terraform`](https://github.com/hashicorp/setup-terraform)
- [`@octokit/request-action`](https://github.com/octokit/request-action)
- [`@reviewdog/action-tflint`](https://github.com/reviewdog/action-tflint)
- [`@reviewdog/action-tfsec`](https://github.com/reviewdog/action-tfsec)
- [`@terraform-docs/gh-actions`](https://github.com/terraform-docs/gh-actions)
- [`@terraform-linters/tflint-load-config-action`](https://github.com/terraform-linters/tflint-load-config-action)

In addition to these Github Actions, custom scripts are executed.
<br><br><br>

>## Terraform Workspace Workflow (Standard)

* This workflow can be used to run Terraform code in a [Terraform workspace][tfe_workspace].

### :exclamation: Requirements

* A Terraform Enterprise or Cloud workspace is required.
* The Terraform workspace should be configured for [CLI runs][tfe_cli_run].
* The terraform repository should contain a [remote backend][tfe_remote_backend].

### Workflow Steps

The Terraform workspace workflow consists of the following steps:

`On Pull Request Event`
1. Terraform Format
2. Terraform Docs
3. Terraform Lint
4. Terraform Security
5. Terraform Plan
6. Publish Result

`On Push Event`
1. Terraform Format
2. Terraform Docs
3. Terraform Lint
4. Terraform Security
5. Terraform Apply
<br><br>

### Inputs [Terraform Workspace Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `tfe_hostname` | Terraform Enterprise/Cloud hostname | `app.terraform.io` | false |
| `terraform_version` | Terraform version used inside github action | `latest` | false |
| `terraform_working_directory` | A relative path starting with '.' that Terraform will execute within (e.g. './infrastructure') | `.` | false |
| `tflint_repo` | Public repo where tflint config is stored. Format: owner/name | `nuvibit/github-tflint-config` | false |
| `tflint_repo_config_path` | Path to tflint config in tflint_repo (e.g. "aws/.tflint.hcl") | `""` | false |
| `tflint_repo_ref` | Ref or branch of tflint_repo | `main` | false |
| `tflint_version` | Tflint version to use in github action | `lastest` | false |
| `tfsec_version` | Tfsec version to use in github action | `lastest` | false |
| `commit_user` | Username which should be used for commits by github action | `github-actions` | false |
| `commit_email` | Email which should be used for commits by github action | `noreply@github.com` | false |
<br>

### Secrets [Terraform Workspace Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token is required to pull private terraform module dependencies directly from github | `""` | true |
| `TFE_API_TOKEN` | Terraform Enterprise/Cloud API Token is required to authenticate with Terraform workspace | `""` | true |

### Usage [Terraform Workspace Workflow]

```yaml
name: TERRAFORM WORKSPACE

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-workspace:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-workspace.yml@v1
    with:
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
      TFE_API_TOKEN: ${{ secrets.TFE_API_TOKEN }}
```

### Usage [Terraform Workspace Workflow + custom working directory]

```yaml
name: TERRAFORM WORKSPACE PRODUCTION
on:
  pull_request:
    branches:
      - main
    paths:
      - 'production/**'
  push:
    branches:
      - main
    paths:
      - 'production/**'
jobs:
  terraform-workspace:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-workspace.yml@v1
    with:
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
      terraform_working_directory: "./production"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
      TFE_API_TOKEN: ${{ secrets.TFE_API_TOKEN }}
```
<br><br>

>## Terraform Workspace Workflow (Minimal)

* This workflow is mostly the same as [Terraform Workspace Workflow (Standard)](#terraform-workspace-workflow-standard) but reduced to only run terraform plan or apply.
* This can be useful for terraform repositories that are maintained in an automated manner.
<br><br>

>## Terraform Module Workflow (Standard)

* This workflow can be used to run [Terratest][terratest_intro] for a Terraform module.
* This workflow releases the module automatically with [semantic versioning][semantic_intro].
* This workflow tests a specific or latest Terraform version.

### :exclamation: Requirements

* An AWS account or Azure subscription is required to run Terratest.
* For this workflow a Terraform Enterprise or Cloud workspace is required.
* The Terraform workspace should be configured for [local runs][tfe_local_runs].
* The terraform repository should contain a [remote backend][tfe_remote_backend].
* [Semantic release config file][semantic_config] is required.

### Workflow Steps

The Terraform module workflow consists of the following steps:

`On Pull Request Event`
1. Terraform Format
2. Terraform Docs
3. Terraform Lint
4. Terraform Security
5. Terraform Terratest
6. Publish Result

`On Push Event`
1. Disable Branch Protection
2. Release module
3. Enable Branch Protection
<br><br>

### Inputs [Terraform Module Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `tfe_hostname` | Terraform Enterprise/Cloud hostname | `app.terraform.io` | false |
| `terraform_version` | Terraform version used for Terratest | `latest` | false |
| `terratest_path` | Path to terratest directory | `test` | false |
| `terratest_examples_path` | Path to terratest example directory | `examples` | false |
| `tflint_repo` | Public repo where tflint config is stored. Format: owner/name | `nuvibit/github-tflint-config` | false |
| `tflint_repo_config_path` | Path to tflint config in tflint_repo (e.g. "aws/.tflint.hcl") | `""` | false |
| `tflint_repo_ref` | Ref or branch of tflint_repo | `main` | false |
| `tflint_version` | Tflint version to use in github action | `lastest` | false |
| `tfsec_version` | Tfsec version to use in github action | `lastest` | false |
| `commit_user` | Username which should be used for commits by github action | `github-actions` | false |
| `commit_email` | Email which should be used for commits by github action | `noreply@github.com` | false |
<br>

### Secrets [Terraform Module Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token is required to pull private terraform module dependencies directly from github | `""` | true |
| `TFE_API_TOKEN` | Terraform Enterprise/Cloud API Token is required to authenticate with Terraform workspace | `""` | true |
| `TERRATEST_AWS_DEFAULT_REGION` | AWS Default Region for Terratest Account | `""` | false |
| `TERRATEST_AWS_ACCESS_KEY_ID` | AWS Access Key for Terratest Account | `""` | false |
| `TERRATEST_AWS_SECRET_ACCESS_KEY` | AWS Secret Access Key for Terratest Account | `""` | false |
| `TERRATEST_ARM_SUBSCRIPTION_ID` | Azure Subscription Id for Terratest Subscription | `""` | false |
| `TERRATEST_ARM_TENANT_ID` | Azure Tenant Id for Terratest Subscription | `""` | false |
| `TERRATEST_ARM_CLIENT_ID` | Azure Client Id for Terratest Subscription | `""` | false |
| `TERRATEST_ARM_CLIENT_SECRET` | Azure Client Secret for Terratest Subscription | `""` | false |
<br>

### Inputs [Terraform Release Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `semantic_version` | Specify specifying version range for semantic-release | `18.0.0` | false |
| `semantic_release_config` | Shareable config to create release of Terraform Modules | `@nuvibit/github-terraform-semantic-release-config` | false |
| `release_branch` | Name of branch on which Terraform Module release should happen | `main` | false |
<br>

### Secrets [Terraform Release Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token is required to pull private terraform module dependencies directly from github | `""` | true |


### Usage [Terraform Module + Release Workflow]

```yaml
name: TERRAFORM MODULE

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-module:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module.yml@v1
    if: ${{ github.event_name == 'pull_request' }}
    with:
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
      TFE_API_TOKEN: ${{ secrets.TFE_API_TOKEN }}
      TERRATEST_AWS_DEFAULT_REGION: ${{ secrets.TERRATEST_AWS_DEFAULT_REGION }}
      TERRATEST_AWS_ACCESS_KEY_ID: ${{ secrets.TERRATEST_AWS_ACCESS_KEY_ID }}
      TERRATEST_AWS_SECRET_ACCESS_KEY: ${{ secrets.TERRATEST_AWS_SECRET_ACCESS_KEY }}

  terraform-module-release:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-release.yml@v1
    if: ${{ github.event_name == 'push' }}
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
```
<br><br>

>## Terraform Module Workflow (Matrix)

* This workflow can be used to run [Terratest][terratest_intro] for a Terraform module.
* This workflow releases the module automatically with [semantic versioning][semantic_intro].
* This workflow uses a [matrix strategy][github_matrix] to allow testing of different Terraform versions.

### :exclamation: Requirements

* An AWS account or Azure subscription is required to run Terratest.
* For this workflow a Terraform Enterprise or Cloud workspace is required.
* The Terraform workspace should be configured for [local runs][tfe_local_runs].
* The terraform repository should contain a [remote backend][tfe_remote_backend].

### Workflow Steps

The Terraform module workflow consists of the following steps:

`On Pull Request Event`
1. Terraform Format
2. Terraform Docs
3. Terraform Lint
4. Terraform Security
5. Terraform Terratest (multiple versions via matrix)
6. Publish Result

`On Push Event`
1. Disable Branch Protection
2. Release module
3. Enable Branch Protection
<br><br>

### Inputs [Terraform Module Matrix Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `tfe_hostname` | Terraform Enterprise/Cloud hostname | `app.terraform.io` | false |
| `terraform_version` | Terraform version used to format code | `latest` | false |
| `registry_hostname` | Hostname for terraform registry used to download providers | `registry.terraform.io` | false |
| `terratest_path` | Path to terratest directory | `test` | false |
| `terratest_examples_path` | Path to terratest example directory | `examples` | false |
| `terratest_max_parallel` | Maximum number of terratest runs that should run simultaneously | `1` | false |
| `terratest_config_repo` | Public repo where terratest matrix json is stored | `nuvibit/github-terratest-config` | false |
| `terratest_config_repo_ref` | Ref or branch of terratest_config_repo | `main` | false |
| `terratest_config_repo_path` | Path to terratest matrix json config in terratest_config_repo (e.g. "aws/matrix.json") | `""` | false |
| `tflint_repo` | Public repo where tflint config is stored. Format: owner/name | `nuvibit/github-tflint-config` | false |
| `tflint_repo_config_path` | Path to tflint config in tflint_repo (e.g. "aws/.tflint.hcl") | `""` | false |
| `tflint_repo_ref` | Ref or branch of tflint_repo | `main` | false |
| `tflint_version` | Tflint version to use in github action | `lastest` | false |
| `tfsec_version` | Tfsec version to use in github action | `lastest` | false |
| `commit_user` | Username which should be used for commits by github action | `github-actions` | false |
| `commit_email` | Email which should be used for commits by github action | `noreply@github.com` | false |
<br>

### Secrets [Terraform Module Matrix Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token is required to pull private terraform module dependencies directly from github | `""` | true |
| `TFE_API_TOKEN` | Terraform Enterprise/Cloud API Token is required to authenticate with Terraform workspace | `""` | true |
| `TERRATEST_AWS_DEFAULT_REGION` | AWS Default Region for Terratest Account | `""` | false |
| `TERRATEST_AWS_ACCESS_KEY_ID` | AWS Access Key for Terratest Account | `""` | false |
| `TERRATEST_AWS_SECRET_ACCESS_KEY` | AWS Secret Access Key for Terratest Account | `""` | false |
| `TERRATEST_ARM_SUBSCRIPTION_ID` | Azure Subscription Id for Terratest Subscription | `""` | false |
| `TERRATEST_ARM_TENANT_ID` | Azure Tenant Id for Terratest Subscription | `""` | false |
| `TERRATEST_ARM_CLIENT_ID` | Azure Client Id for Terratest Subscription | `""` | false |
| `TERRATEST_ARM_CLIENT_SECRET` | Azure Client Secret for Terratest Subscription | `""` | false |
<br>

### Inputs [Terraform Release Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `toggle_branch_protection` | Temporary disable branch protection to allow release action to push updates to changelog | `true` | false |
| `semantic_version` | Specify specifying version range for semantic-release | `18.0.0` | false |
| `semantic_release_config` | Shareable config to create release of Terraform Modules | `@nuvibit/github-terraform-semantic-release-config` | false |
| `release_branch` | Name of branch on which Terraform Module release should happen | `main` | false |
<br>

### Secrets [Terraform Release Workflow]

| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token is required to pull private terraform module dependencies directly from github | `""` | true |
<br>

### Usage [Terraform Module Matrix + Release Workflow]

```yaml
name: TERRAFORM MODULE

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-module:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-matrix.yml@v1
    if: ${{ github.event_name == 'pull_request' }}
    with:
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
      terratest_config_repo: "nuvibit/github-terratest-config"
      terratest_config_repo_path: "aws/matrix.json"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
      TFE_API_TOKEN: ${{ secrets.TFE_API_TOKEN }}
      TERRATEST_AWS_DEFAULT_REGION: ${{ secrets.TERRATEST_AWS_DEFAULT_REGION }}
      TERRATEST_AWS_ACCESS_KEY_ID: ${{ secrets.TERRATEST_AWS_ACCESS_KEY_ID }}
      TERRATEST_AWS_SECRET_ACCESS_KEY: ${{ secrets.TERRATEST_AWS_SECRET_ACCESS_KEY }}

  terraform-module-release:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-release.yml@v1
    if: ${{ github.event_name == 'push' }}
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
```

<!-- AUTHORS -->
## Authors

This collection is maintained by [Nuvibit][nuvibit-url] with help from [these amazing contributors][contributors-url]

<!-- LICENSE -->
## License

This collection is licensed under Apache 2.0
<br />
See [LICENSE][license-url] for full details

<!-- COPYRIGHT -->
<br />
<br />
<p align="center">Copyright &copy; 2022 Nuvibit AG</p>

<!-- MARKDOWN LINKS & IMAGES -->
[nuvibit-shield]: https://img.shields.io/badge/maintained%20by-nuvibit.com-%235849a6.svg?style=flat&color=1c83ba
[nuvibit-url]: https://nuvibit.com
[release-shield]: https://img.shields.io/github/v/release/nuvibit/github-terraform-workflows?style=flat&color=success
[release-url]: https://github.com/nuvibit/github-terraform-workflows/releases
[contributors-url]: https://github.com/nuvibit/github-terraform-workflows/graphs/contributors
[license-url]: https://github.com/nuvibit/github-terraform-workflows/tree/master/LICENSE

[github_workflows_link]: https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions
[tfe_intro]: https://www.terraform.io/cloud-docs
[tfe_workspace]: https://www.terraform.io/cloud-docs/workspaces
[tfe_cli_run]: https://www.terraform.io/cloud-docs/run/cli
[tfe_remote_backend]: https://www.terraform.io/language/settings/backends/remote#basic-configuration
[tfe_local_runs]: https://www.terraform.io/cloud-docs/workspaces/state#state-usage-in-terraform-runs
[github_matrix]: https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs
[terratest_intro]: https://terratest.gruntwork.io/docs/getting-started/quick-start/
[semantic_intro]: https://github.com/semantic-release/semantic-release#-semantic-release
[semantic_config]: https://github.com/semantic-release/semantic-release/blob/master/docs/usage/configuration.md#configuration