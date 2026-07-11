<div align="center">

# 🌍 Terraform Keyword Map

**A practitioner's mental map of Terraform — for 10+ years of infra experience, not a "getting started" guide.**

![Terraform](https://img.shields.io/badge/Terraform-844FBA?style=for-the-badge&logo=terraform&logoColor=white)
![HCL](https://img.shields.io/badge/HCL-black?style=for-the-badge)
![Level](https://img.shields.io/badge/Level-Advanced-red?style=for-the-badge)

</div>

---

## 📑 Table of Contents

1. [Core Concepts / Building Blocks](#1-core-concepts--building-blocks)
2. [HCL Language Mechanics](#2-hcl-language-mechanics)
3. [Functions](#3-functions)
4. [State Management](#4-state-management)
5. [Workflow / CLI](#5-workflow--cli)
6. [Dependency Model](#6-dependency-model)
7. [Lifecycle & Behavior Control](#7-lifecycle--behavior-control)
8. [Variables & Configuration Precedence](#8-variables--configuration-precedence)
9. [Modules](#9-modules)
10. [Provider Concepts](#10-provider-concepts)
11. [Security / Sensitive Data](#11-security--sensitive-data)
12. [Collaboration / Enterprise Features](#12-collaboration--enterprise-features)
13. [Testing / Validation](#13-testing--validation)
14. [CI/CD & Automation](#14-cicd--automation)
15. [Import & Migration](#15-import--migration)
16. [Miscellaneous / Exam Traps](#16-miscellaneous--exam-traps)

---

## 1. Core Concepts / Building Blocks

| Keyword | What it is |
|---|---|
| **Provider** | Plugin that talks to an API (AWS, Azure, GCP, etc.) |
| **Resource** | The actual infrastructure object you create/manage |
| **Data Source** | Read-only fetch of existing infra (`data "aws_ami" ...`) |
| **Module** | Reusable, encapsulated group of resources |
| **Variable / Output** | Input/output interface of a module or root config |
| **Provisioner** | Last-resort imperative step (`local-exec`, `remote-exec`) |

---

## 2. HCL Language Mechanics

- HCL syntax vs JSON syntax
- Arguments vs Blocks
- Expressions — references, operators, conditionals
- String interpolation (`"${}"`)
- Heredoc syntax
- Meta-arguments: `count`, `for_each`, `depends_on`, `provider`, `lifecycle`
- `for` expressions
- **`for_each` vs `count`** ⚠️ — the classic "which one, when" question
- Dynamic blocks
- Conditional expressions (`condition ? true_val : false_val`)
- Splat expressions (`*`)
- Type constraints & type conversion
- Sensitive variables (`sensitive = true`)
- Validation blocks (`validation {}` inside variable)
- Nullable variables

<details>
<summary>💡 <b>Gotcha: for_each vs count</b></summary>

- `count` — best for identical resources indexed by number; breaks ordering if the list shrinks/reorders (resources get destroyed/recreated).
- `for_each` — best for resources keyed by a stable identifier (map or set of strings); avoids the reordering-destroys-everything problem.

</details>

---

## 3. Functions

| Category | Examples |
|---|---|
| Numeric | `min`, `max`, `ceil`, `floor` |
| String | `format`, `join`, `split`, `replace`, `lower`, `upper`, `trim` |
| Collection | `length`, `merge`, `lookup`, `concat`, `flatten`, `element`, `contains`, `distinct`, `sort`, `zipmap`, `setproduct` |
| Encoding | `jsonencode`, `jsondecode`, `yamlencode`, `yamldecode`, `base64encode`/`decode` |
| Filesystem | `file`, `filebase64`, `templatefile`, `fileexists` |
| Date/Time | `timestamp`, `formatdate`, `timeadd` |
| Type conversion | `tostring`, `tonumber`, `tolist`, `tomap`, `tobool` |
| Hash/crypto | `md5`, `sha256`, `uuid`, `bcrypt` |
| IP/network | `cidrsubnet`, `cidrhost` |

> 🧪 Test any function interactively with `terraform console`.

---

## 4. State Management

- State file (`terraform.tfstate`) — structure: resources, metadata, serial, lineage
- Local state vs Remote state
- Remote backends: S3, AzureRM, GCS, Terraform Cloud, Consul, etcd
- **State locking** (DynamoDB for S3 backend)
- Backend block vs Partial configuration
- `terraform_remote_state` data source
- State commands: `list`, `show`, `mv`, `rm`, `pull`, `push`, `replace-provider`
- `terraform import`
- `terraform refresh` (now via `-refresh-only`)
- Drift detection
- Sensitive data in state ⚠️ (state is **not** encrypted by default)
- Workspaces (`terraform workspace new/list/select/show`) + `terraform.workspace` variable
- State file versioning / backups (`.tfstate.backup`)

---

## 5. Workflow / CLI

```bash
terraform init          # download providers, set up backend
terraform validate      # syntax/config check
terraform fmt           # auto-format
terraform plan          # -out, -target, -destroy, -refresh-only, -replace
terraform apply         # -auto-approve
terraform destroy
terraform graph
terraform console
terraform output
terraform providers
terraform show
```

**Flags & env vars worth knowing:**
- `-var` and `-var-file`
- `TF_VAR_*`, `TF_LOG`, `TF_CLI_ARGS`, `TF_DATA_DIR`, `TF_WORKSPACE`
- Debugging: `TF_LOG=TRACE/DEBUG/INFO/WARN/ERROR`
- Plan exit codes: `0` (no changes), `1` (error), `2` (changes present)

---

## 6. Dependency Model

- Implicit dependency (via reference)
- Explicit dependency (`depends_on`)
- **DAG** (Directed Acyclic Graph) — how Terraform decides order
- Parallelism (`-parallelism=n`)
- Resource graph visualization (`terraform graph`)

---

## 7. Lifecycle & Behavior Control

```hcl
lifecycle {
  create_before_destroy = true
  prevent_destroy        = true
  ignore_changes          = [tags]
  replace_triggered_by    = [aws_instance.example.id]
}
```

- Resource targeting (`-target`)
- Force replacement (`-replace`)

---

## 8. Variables & Configuration Precedence

⚠️ **Exam trap — memorize this order (highest to lowest):**

1. CLI flags (`-var`, `-var-file`)
2. `*.auto.tfvars` (alphabetical order)
3. `terraform.tfvars`
4. `TF_VAR_*` environment variables
5. Default value in the variable block

---

## 9. Modules

- Module sources: local path, Terraform Registry, GitHub, Git, HTTP, S3/GCS bucket
- Module versioning (`version` argument, pessimistic constraint `~>`)
- Public Terraform Registry vs Private Module Registry
- Module inputs/outputs
- Nested modules
- Root module vs child module

---

## 10. Provider Concepts

- Provider versioning & constraints (`>=`, `<=`, `~>`)
- **Provider aliasing** (multi-region/multi-account within one config)
- Multiple provider configurations
- `.terraformrc` / `terraform.rc` (CLI config file)
- Provider mirror / network mirror (offline installs)
- Dependency lock file (`.terraform.lock.hcl`)

---

## 11. Security / Sensitive Data

- Sensitive output/variable marking
- State file encryption (backend-dependent, e.g. S3 SSE)
- Secrets management integration (Vault provider, AWS Secrets Manager data sources)
- `.gitignore` for state & `.tfvars` ⚠️

---

## 12. Collaboration / Enterprise Features

- Terraform Cloud (TFC) / Terraform Enterprise (TFE)
- Remote execution mode
- VCS-driven workflow
- Run triggers
- Private Registry
- **Sentinel** (policy-as-code, TFE/TFC only)
- **OPA** (Open Policy Agent — open-source alternative to Sentinel)
- RBAC / Teams in TFC
- Cost Estimation (TFC feature)

---

## 13. Testing / Validation

- `terraform validate` / `terraform plan` as a check
- Terraform test framework (`.tftest.hcl`, `terraform test`)
- `check {}` blocks (assertions)
- Preconditions / Postconditions (lifecycle and outputs)
- `tflint` — third-party linting
- `terratest` — Go-based testing framework
- `checkov` / `tfsec` — security scanning

---

## 14. CI/CD & Automation

- Terraform in pipelines (GitHub Actions, GitLab CI, Jenkins, Azure DevOps)
- Plan/Apply approval gates
- **Atlantis** — open-source PR automation
- GitOps workflow with Terraform

---

## 15. Import & Migration

| Keyword | Notes |
|---|---|
| `terraform import` | Legacy, resource-by-resource |
| `import` block | Declarative import (Terraform 1.5+) |
| `moved` block | Refactor without destroy-recreate |
| `removed` block | Remove from state without destroying (Terraform 1.7+) |

---

## 16. Miscellaneous / Exam Traps

- Terraform Registry vs Provider Registry
- Immutable infrastructure philosophy
- Idempotency
- `.terraform/` directory (downloaded providers/modules cache)
- `terraform.tfstate.d` (workspace state storage)
- `count.index` vs `each.key`/`each.value`
- `null_resource` → largely replaced by **`terraform_data`** (1.4+)
- Custom conditions (`precondition`/`postcondition` blocks)

---

<div align="center">

**Part of the [devops-cheatsheets](../) series** · Terraform Associate (004) exam-relevant items marked ⚠️

</div>
