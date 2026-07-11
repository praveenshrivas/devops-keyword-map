<div align="center">

# 🅰️ Ansible Keyword Map

**A practitioner's mental map of Ansible — for 10+ years of infra experience, not a "getting started" guide.**

![Ansible](https://img.shields.io/badge/Ansible-black?style=for-the-badge&logo=ansible&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=for-the-badge&logo=yaml&logoColor=white)
![Level](https://img.shields.io/badge/Level-Advanced-red?style=for-the-badge)

</div>

---

## 📑 Table of Contents

1. [Core Concepts](#1-core-concepts)
2. [Inventory](#2-inventory)
3. [Playbooks & YAML](#3-playbooks--yaml)
4. [Variables](#4-variables)
5. [Templating](#5-templating)
6. [Roles](#6-roles)
7. [Collections](#7-collections)
8. [Modules](#8-modules-common-categories)
9. [Connection & Execution](#9-connection--execution)
10. [Security / Secrets](#10-security--secrets)
11. [Idempotency & Execution Control](#11-idempotency--execution-control)
12. [Testing / Linting](#12-testing--linting)
13. [CLI Tools](#13-cli-tools)
14. [Scaling / Enterprise](#14-scaling--enterprise)
15. [Networking / Advanced](#15-networking--advanced)
16. [Comparison Points](#16-comparison-points)

---

## 1. Core Concepts

| Keyword | What it is |
|---|---|
| **Control Node** | Machine running Ansible |
| **Managed Node(s)** | Target hosts (agentless — no daemon required) |
| **Inventory** | List of managed hosts (static or dynamic) |
| **Playbook** | YAML file defining automation |
| **Task** | Single unit of action |
| **Play** | Maps hosts to tasks |
| **Module** | Unit of work (`yum`, `copy`, `service`, etc.) |
| **Plugin** | Connection, callback, lookup, filter, action plugins |
| **Role** | Reusable, structured collection of tasks/vars/handlers |
| **Collection** | Packaging format for roles/modules/plugins |

> ⚡ Ad-hoc command example: `ansible all -m ping`

---

## 2. Inventory

- Static inventory (`/etc/ansible/hosts`, custom INI/YAML)
- Dynamic inventory (AWS EC2, Azure, GCP plugins/scripts)
- Inventory groups (`[web]`, `[db]`, `children`)
- Host variables / Group variables (`host_vars/`, `group_vars/`)
- Inventory patterns (`all`, `webservers:!staging`, wildcards)
- `ansible-inventory` command

---

## 3. Playbooks & YAML

- YAML syntax (indentation-sensitive, lists, dicts)
- `hosts`, `become`, `tasks`, `vars`, `handlers`, `roles` keywords
- `become` / `become_user` / `become_method` — privilege escalation
- `gather_facts`
- `tags` (`--tags`, `--skip-tags`)
- `when` conditionals
- `loop` / `with_items` / `with_dict` ⚠️ — prefer `loop`, `with_*` is legacy
- `register` — capture task output
- `notify` + `handlers` — event-driven tasks
- `block` / `rescue` / `always` — error handling (try/catch equivalent)
- `import_playbook` / `include_playbook`
- **`import_tasks` vs `include_tasks`** ⚠️ — static vs dynamic

<details>
<summary>💡 <b>Gotcha: import_tasks vs include_tasks</b></summary>

- `import_*` — processed at **playbook parse time** (static). Tags/conditionals apply to the whole imported block.
- `include_*` — processed at **runtime** (dynamic). Better for conditional logic and looping over includes.

</details>

---

## 4. Variables

⚠️ **Exam trap — memorize variable precedence (highest to lowest, simplified):**

1. Extra vars (`-e`)
2. Task vars
3. Block vars
4. Role vars
5. Play vars
6. Host facts / registered vars
7. `host_vars/`
8. `group_vars/`
9. Role `vars/main.yml`
10. Role `defaults/main.yml` (lowest)

**Other keywords:**
- Facts (`ansible_facts`, `setup` module)
- Magic variables (`hostvars`, `inventory_hostname`, `group_names`, `ansible_play_hosts`)
- Vault-encrypted variables

---

## 5. Templating

- **Jinja2** templating
- Filters (`| default`, `| join`, `| to_json`, `| regex_replace`)
- `template` module vs `copy` module
- Conditionals/loops inside Jinja2 (`{% if %}`, `{% for %}`)

---

## 6. Roles

```
role_name/
├── tasks/
├── handlers/
├── templates/
├── files/
├── vars/
├── defaults/
└── meta/
```

- `ansible-galaxy` (init, install, role search)
- `meta/main.yml` — role dependencies
- Role includes vs role dependencies

---

## 7. Collections

- `ansible-galaxy collection install`
- `requirements.yml`
- **FQCN** (Fully Qualified Collection Name) — e.g. `ansible.builtin.copy`
- `ansible.builtin`, `community.general`, cloud collections (`amazon.aws`, `azure.azcollection`, `google.cloud`)

---

## 8. Modules (common categories)

| Category | Modules |
|---|---|
| Command execution | `command`, `shell`, `raw`, `script` |
| File management | `copy`, `file`, `template`, `lineinfile`, `blockinfile`, `fetch`, `synchronize` |
| Package management | `yum`, `apt`, `dnf`, `package`, `pip` |
| Service management | `service`, `systemd` |
| User/group | `user`, `group` |
| Cloud | `ec2_instance`, `azure_rm_virtualmachine`, `gcp_compute_instance` |

> ✅ Idempotency is built into modules via `state: present/absent/latest`.

---

## 9. Connection & Execution

- SSH (default connection plugin)
- WinRM (Windows targets)
- Local connection
- `ansible.cfg` — remote_user, become settings, inventory path, etc.
- Forks (`--forks`, parallel execution)
- Strategy plugins (`linear`, `free`, `debug`)
- `serial` — rolling updates, batch size control
- `delegate_to` / `local_action`
- `run_once`

---

## 10. Security / Secrets

```bash
ansible-vault create/edit/encrypt/decrypt/view/rekey
--ask-vault-pass
--vault-password-file
```

- **vault_id** — multiple vault passwords
- `become` (privilege escalation) — sudo, su, pbrun, etc.
- `no_log` — hide sensitive task output ⚠️

---

## 11. Idempotency & Execution Control

- Check mode (`--check`, dry run)
- Diff mode (`--diff`)
- Idempotent module design philosophy
- Handlers only run **once**, at end of play, on `notify`
- `force_handlers`
- `any_errors_fatal` / `max_fail_percentage`

---

## 12. Testing / Linting

- `ansible-lint`
- **Molecule** — role testing framework (industry standard, third-party)
- `--syntax-check`
- `--list-tasks` / `--list-hosts`

---

## 13. CLI Tools

```bash
ansible              # ad-hoc
ansible-playbook
ansible-galaxy
ansible-vault
ansible-config
ansible-doc
ansible-inventory
ansible-console       # REPL
```

---

## 14. Scaling / Enterprise

- **Ansible Automation Platform (AAP)** — formerly Ansible Tower
- **AWX** — open-source upstream of Tower/AAP
- Job Templates, Workflows, Credentials, Projects (in AWX/AAP)
- RBAC in AAP/Tower
- **Execution Environments** — containerized Ansible runtime (replaces virtualenvs)
- Ansible Runner

---

## 15. Networking / Advanced

- Network modules (`ios_command`, `junos_config`, etc.)
- Pull mode (`ansible-pull`)
- Callback plugins (custom output — JSON, JUnit)
- Lookup plugins (`lookup('file', ...)`, `lookup('env', ...)`)
- Filter plugins (custom Jinja2 filters)

---

## 16. Comparison Points

> Since you're coming from a Terraform mindset:

| | Ansible | Terraform |
|---|---|---|
| Model | Procedural, push-based | Declarative, state-based |
| Agent | Agentless (SSH/WinRM) | N/A — talks to cloud APIs directly |
| Best for | Configuration management, app deployment | Infrastructure provisioning |
| Idempotency | Per-module design | Built into plan/apply diffing |

💬 Common interview question: *"Ansible vs Terraform — when do you use which?"* Worth keeping this distinction crisp.

---

<div align="center">

**Part of the [devops-cheatsheets](../) series** · RHCE (EX294)-relevant items marked ⚠️

</div>
