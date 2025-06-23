# GitHub Actions - Reusable Terraform and OpenTofu Workflows

<!-- LOGO -->
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://nuvibit.com/images/logo/logo-nuvibit-banner.png">
  <source media="(prefers-color-scheme: light)" srcset="https://nuvibit.com/images/logo/logo-nuvibit-banner-dark.png">
  <img alt="Fallback image description" src="[Nuvibit Logo](https://nuvibit.com/images/logo/logo-nuvibit-banner.png)" width="400">
</picture>
<br/>
<br/>

<!-- DESCRIPTION -->
Reusable [GitHub Workflows](https://docs.github.com/en/actions/learn-github-actions/) to run state-of-the-art Terraform and OpenTofu pipelines with flexible deployment strategies.

## 🚀 **Two Workflow Categories**

This collection provides workflows for **two distinct Infrastructure as Code scenarios**:

### **📦 Infrastructure Deployment Workflows**
For deploying and managing infrastructure (AWS, Azure, GCP, etc.) using Terraform/OpenTofu:

- **Primary Workflow**: `terraform-stack.yml`
- **Purpose**: Deploy infrastructure changes to cloud environments
- **Target**: Infrastructure repositories, environment-specific deployments
- **Features**: Quality gates, optional plan/apply execution, multi-environment support

### **🔧 Module Development Workflows**  
For developing, testing, and releasing reusable Terraform modules:

- **Workflows**: `terraform-module-test.yml` + `terraform-module-release.yml`
- **Purpose**: Automated testing with Terratest and semantic versioning releases
- **Target**: Terraform module repositories, library development
- **Features**: Integration testing, automated releases, version management

## 🔄 **Deployment Approaches (Infrastructure Workflows)**

The infrastructure deployment workflows support **two distinct deployment patterns**:

### **1. 🔧 Hybrid Approach - Static Checks + External IaC Platform**
Perfect for teams using dedicated IaC management platforms while maintaining quality gates in GitHub:

- **GitHub Actions Role**: Performs all static code quality checks (format, docs, lint, security)
- **External Platform Role**: Handles terraform plan/apply execution  
- **Supported Platforms**: [Terraform Cloud](https://cloud.hashicorp.com/products/terraform), [Spacelift](https://spacelift.io/), [Atlantis](https://www.runatlantis.io/), and other IaC platforms
- **Benefits**: Leverage specialized IaC tooling while maintaining code quality standards
- **Configuration**: Set `enable_terraform_execution: false` (default)

### **2. 🔄 Complete CI/CD - Full GitHub Actions Pipeline**  
Ideal for teams wanting a complete Infrastructure as Code pipeline within GitHub Actions:

- **GitHub Actions Role**: Handles everything - quality checks AND terraform execution
- **Deployment Flow**: Plan on PR → Apply on merge to main branch
- **Features**: PR comments with plan results, automated apply, full workflow visibility
- **Benefits**: Single platform for all IaC operations, simplified toolchain
- **Configuration**: Set `enable_terraform_execution: true`

Both approaches support **Terraform** and **OpenTofu**, provide comprehensive quality gates, and integrate seamlessly with cloud providers for infrastructure deployment.
<br><br>

<!-- DIAGRAM -->
## Terraform Workflow
![Terraform Workflow Diagram](https://github.com/nuvibit/github-terraform-workflows/blob/main/docs/terraform-workflow.png?raw=true)
<br><br>

## Quick Start
Choose the appropriate workflow category for your use case:

### **📦 Infrastructure Deployment**
- [Terraform Stack Workflow](#terraform-stack-workflow) - Deploy infrastructure with quality gates and optional execution

### **🔧 Module Development**  
- [Terraform Module Test Workflow](#terraform-module-test-workflow) - Automated testing with Terratest
- [Terraform Module Release Workflow](#terraform-module-release-workflow) - Automated semantic versioning and releases
<br><br>

## Configuration Repositories (optional)
- [`github-tflint-config`](https://github.com/nuvibit/github-tflint-config)
- [`github-terratest-config`](https://github.com/nuvibit/github-terratest-config)
- [`github-terraform-semantic-release-config`](https://github.com/nuvibit/github-terraform-semantic-release-config)
<br><br>

## Referenced Github Actions
The reusable Github Workflows include the following public Github Actions:

- [`@actions/checkout`](https://github.com/actions/checkout)
- [`@actions/setup-go`](https://github.com/actions/setup-go)
- [`@actions/github-script`](https://github.com/actions/github-script)
- [`@ad-m/github-push-action`](https://github.com/ad-m/github-push-action)
- [`@cycjimmy/semantic-release-action`](https://github.com/cycjimmy/semantic-release-action)
- [`@hashicorp/setup-terraform`](https://github.com/hashicorp/setup-terraform)
- [`@opentofu/setup-opentofu`](https://github.com/opentofu/setup-opentofu)
- [`@reviewdog/action-tflint`](https://github.com/reviewdog/action-tflint)
- [`@reviewdog/action-trivy`](https://github.com/reviewdog/action-trivy)
- [`@terraform-docs/gh-actions`](https://github.com/terraform-docs/gh-actions)
- [`@terraform-linters/tflint-load-config-action`](https://github.com/terraform-linters/tflint-load-config-action)

In addition to these Github Actions, custom bash scripts are run to avoid using [unverified actions](https://docs.github.com/de/apps/publishing-apps-to-github-marketplace/github-marketplace-overview/about-marketplace-badges#for-github-actions).
<br><br><br>

>## Terraform Stack Workflow
* **Purpose**: Deploy and manage infrastructure (AWS, Azure, GCP, etc.) using Terraform/OpenTofu
* **Target**: Infrastructure repositories, environment-specific deployments, cloud resource management
* This workflow provides a complete Terraform/OpenTofu CI/CD pipeline with quality gates
* Supports both Terraform and OpenTofu  
* **Flexible deployment modes**: Static checks only OR complete CI/CD with execution
* Optional terraform plan on pull requests and apply on push to default branch

### 🎯 **Use Case Selection**

**Static Checks Mode** (`enable_terraform_execution: false`)
- Use when leveraging external IaC platforms (Terraform Cloud, Spacelift, etc.)
- GitHub Actions handles quality assurance (format, docs, lint, security)
- External platform handles terraform plan/apply execution
- Ideal for enterprise environments with dedicated IaC tooling

**Complete CI/CD Mode** (`enable_terraform_execution: true`)  
- Use for full GitHub Actions-based Infrastructure as Code pipeline
- All operations (quality checks + terraform execution) in GitHub Actions
- Plan results posted as PR comments, apply on merge to main
- Perfect for teams wanting unified workflow platform

### :white_check_mark: Features
* **Terraform/OpenTofu Support**: Seamless switching between tools
* **Quality Gates**: Format, documentation, lint, and security checks
* **Optional Execution**: Enable terraform plan/apply with simple toggle
* **PR Integration**: Plan results posted as PR comments
* **Branch Protection**: Apply only runs on specified default branch
* **Flexible Configuration**: Extensive customization options

### Workflow Steps
The Terraform Stack workflow consists of the following steps:

`Always Runs`
1. **Terraform Format** - Code formatting with auto-commit
2. **Terraform Docs** - Documentation generation 
3. **Terraform Lint** - Static code analysis with TFLint
4. **Terraform Security** - Security scanning with Trivy

`Optional Steps (when enabled)`
5. **Terraform Plan** - On pull requests (posts results to PR)
6. **Terraform Apply** - On push to default branch (auto-approve)
7. **Workflow Summary** - Centralized status reporting
<br><br>

### Inputs [Terraform Stack Workflow]
| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `use_opentofu` | Use OpenTofu instead of Terraform | `false` | false |
| `terraform_version` | Terraform/OpenTofu version used inside github action | `latest` | false |
| `terraform_working_directory` | A relative path starting with '.' that Terraform will execute within (e.g. './infrastructure') | `.` | false |
| `tflint_repo` | Public repo where tflint config is stored | `nuvibit/github-tflint-config` | false |
| `tflint_repo_config_path` | Path to tflint config in tflint_repo (e.g. "aws/.tflint.hcl") | `""` | false |
| `tflint_repo_ref` | Ref or branch of tflint_repo | `main` | false |
| `tflint_version` | Tflint version to use in github action | `latest` | false |
| `trivy_version` | Trivy version to use in github action | `latest` | false |
| `commit_user` | Username which should be used for commits by github action | `github-actions` | false |
| `commit_email` | Email which should be used for commits by github action | `noreply@github.com` | false |
| `concurrency_group` | Name of concurrency group to manage concurrent github action runs | Auto-generated | false |
| `enable_terraform_execution` | Enable terraform plan on pull requests and apply on push to default branch | `false` | false |
| `default_branch` | Default branch name for terraform apply (e.g. main, master) | `main` | false |
<br>

### Secrets [Terraform Stack Workflow]
| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token is required to pull private terraform module dependencies directly from github | `""` | true |

### Usage [Terraform Stack Workflow - Static Checks Only]
*Perfect for teams using Terraform Cloud, Spacelift, or other IaC platforms*

```yaml
name: TERRAFORM STACK

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-stack:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-stack.yml@v1
    with:
      # enable_terraform_execution: false (default - static checks only)
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
```

### Usage [Terraform Stack Workflow - Complete CI/CD]
*Full GitHub Actions pipeline with terraform plan/apply*

```yaml
name: TERRAFORM STACK

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  terraform-stack:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-stack.yml@v1
    with:
      enable_terraform_execution: true  # Enable complete CI/CD mode
      default_branch: "main"
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
```

### Usage [OpenTofu Stack Workflow - Complete CI/CD]
*OpenTofu with full CI/CD pipeline*
```yaml
name: OPENTOFU STACK

on:
  pull_request:
    branches:
      - main
  push:
    branches:
      - main

jobs:
  opentofu-stack:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-stack.yml@v1
    with:
      use_opentofu: true
      enable_terraform_execution: true
      terraform_version: "1.8.0"
      terraform_working_directory: "./infrastructure"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
```
<br><br>

>## Terraform Module Test Workflow  
* **Purpose**: Automated testing and validation of reusable Terraform modules
* **Target**: Terraform module repositories, library development, module marketplaces
* This workflow runs comprehensive testing for Terraform modules using [Terratest](https://terratest.gruntwork.io/docs/getting-started/quick-start/)
* Includes format, documentation, lint, security, and integration testing
* Supports multiple cloud providers and matrix testing

### :white_check_mark: Features
* **Terratest Integration**: Automated testing with Go-based test framework
* **Matrix Testing**: Support for testing multiple Terraform versions
* **Multi-Cloud**: AWS, Azure, and other cloud provider support
* **Quality Gates**: Same format, docs, lint, security checks as stack workflow
* **Parallel Testing**: Configurable parallel test execution

### Workflow Steps
`On Pull Request Event`
1. **Terraform Format** - Code formatting
2. **Terraform Docs** - Documentation generation  
3. **Terraform Lint** - Static code analysis
4. **Terraform Security** - Security scanning
5. **Terratest** - Integration testing with real infrastructure
6. **Publish Results** - Test results and coverage

### Inputs [Terraform Module Test Workflow]
| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `tfe_hostname` | Terraform Enterprise/Cloud hostname | `app.terraform.io` | false |
| `terraform_version` | Terraform version used for Terratest | `latest` | false |
| `terratest_version` | Terratest version | `v0.48.0` | false |
| `terratest_path` | Path to terratest directory | `test` | false |
| `terratest_examples_path` | Path to terratest examples directory | `examples` | false |
| `terratest_max_parallel` | Maximum number of terratest runs that should run simultaneously | `1` | false |
| `tflint_repo` | Public repo where tflint config is stored | `nuvibit/github-tflint-config` | false |
| `tflint_repo_config_path` | Path to tflint config in tflint_repo | `""` | false |
| `tflint_repo_ref` | Ref or branch of tflint_repo | `main` | false |
| `tflint_version` | Tflint version to use in github action | `latest` | false |
| `trivy_version` | Trivy version to use in github action | `latest` | false |

### Secrets [Terraform Module Test Workflow]
| Name | Description | Required |
|------|-------------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token | true |
| `TFE_API_TOKEN` | Terraform Enterprise/Cloud API Token | false |
| `TERRATEST_AWS_DEFAULT_REGION` | AWS Default Region for Terratest | false |
| `TERRATEST_AWS_ACCESS_KEY_ID` | AWS Access Key for Terratest | false |
| `TERRATEST_AWS_SECRET_ACCESS_KEY` | AWS Secret Access Key for Terratest | false |

### Usage [Terraform Module Test Workflow]
```yaml
name: TERRAFORM MODULE TEST

on:
  pull_request:
    branches:
      - main

jobs:
  terraform-module-test:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-test.yml@v1
    with:
      tflint_repo: "nuvibit/github-tflint-config"
      tflint_repo_config_path: "aws/.tflint.hcl"
      terratest_version: "v0.48.0"
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
      TFE_API_TOKEN: ${{ secrets.TFE_API_TOKEN }}
      TERRATEST_AWS_DEFAULT_REGION: ${{ secrets.TERRATEST_AWS_DEFAULT_REGION }}
      TERRATEST_AWS_ACCESS_KEY_ID: ${{ secrets.TERRATEST_AWS_ACCESS_KEY_ID }}
      TERRATEST_AWS_SECRET_ACCESS_KEY: ${{ secrets.TERRATEST_AWS_SECRET_ACCESS_KEY }}
```
<br><br>

>## Terraform Module Release Workflow
* **Purpose**: Automated versioning and releasing of Terraform modules
* **Target**: Terraform module repositories, library maintenance, version management
* This workflow automatically releases Terraform modules with [semantic versioning]https://github.com/semantic-release/semantic-release#-semantic-release  
* Integrates with conventional commit standards
* Manages branch protection and changelog generation

### :white_check_mark: Features
* **Semantic Versioning**: Automated version bumping based on commit messages
* **Changelog Generation**: Automatic changelog updates
* **Branch Protection**: Temporary bypass for release commits
* **GitHub Releases**: Automated release creation with tags
* **Conventional Commits**: Follows conventional commit standards

### Workflow Steps
`On Push to Main Branch`
1. **Checkout** - Repository checkout
2. **Branch Protection Toggle** - Temporarily disable if enabled
3. **Semantic Release** - Analyze commits and create release
4. **Branch Protection Restore** - Re-enable branch protection

### Inputs [Terraform Module Release Workflow]
| Name | Description | Default | Required |
|------|-------------|---------|----------|
| `github_runner` | Name of GitHub-hosted runner or self-hosted runner | `ubuntu-latest` | false |
| `toggle_branch_protection` | Temporary disable branch protection to allow release action to push updates | `true` | false |
| `semantic_version` | Specify version range for semantic-release | `18.0.0` | false |
| `semantic_release_config` | Shareable config to create release of Terraform Modules | `@nuvibit/github-terraform-semantic-release-config` | false |
| `release_branch` | Name of branch on which Terraform Module release should happen | `main` | false |
| `concurrency_group` | Name of concurrency group to manage concurrent github action runs | Auto-generated | false |

### Secrets [Terraform Module Release Workflow]
| Name | Description | Required |
|------|-------------|----------|
| `GHE_API_TOKEN` | Github (Enterprise) API Token | true |

### Usage [Terraform Module Release Workflow]
```yaml
name: TERRAFORM MODULE RELEASE

on:
  push:
    branches:
      - main

jobs:
  terraform-module-release:
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-release.yml@v1
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
```

### Usage [Complete Module Workflow - Test + Release]
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
  terraform-module-test:
    if: ${{ github.event_name == 'pull_request' }}
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-test.yml@v1
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
    if: ${{ github.event_name == 'push' }}
    uses: nuvibit/github-terraform-workflows/.github/workflows/terraform-module-release.yml@v1
    secrets:
      GHE_API_TOKEN: ${{ secrets.GHE_API_TOKEN }}
```

<!-- AUTHORS -->
## Authors
This collection is maintained by [Nuvibit](https://nuvibit.com) with help from [these amazing contributors](https://github.com/nuvibit/github-terraform-workflows/graphs/contributors)

<!-- LICENSE -->
## License
This collection is licensed under Apache 2.0
<br />
See [LICENSE](https://github.com/nuvibit/github-terraform-workflows/tree/master/LICENSE) for full details

<!-- COPYRIGHT -->
<br />
<br />
<p align="center">Copyright &copy; Nuvibit AG</p>