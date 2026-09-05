# local-homelab

> A personal homelab, managed entirely as code — from the GitHub org itself to
> the compute that runs local AI models.

Everything here is **infrastructure-as-code and GitOps-first**: changes are made
by editing Terraform and opening a pull request, not by clicking around a UI.
The GitHub organization you're looking at is itself provisioned by Terraform.

## Principles

- **Automate everything.** Every resource — including these org settings and
  repositories — is declared as code and applied through automation.
- **Least standing privilege.** Automation authenticates as a GitHub App that
  mints short-lived tokens; there are no long-lived personal access tokens.
- **Secrets never touch the repo.** Real values live only in local, gitignored
  files; only generic blueprints are committed.

## Architecture

```mermaid
flowchart LR
    subgraph tailnet["Tailscale mesh (private)"]
        mac["MacBook Pro<br/>workstation + agents"]
        win["Windows desktop<br/>RTX 3070 · Ollama"]
        nebula["Nebula Mini PC<br/>Talos control plane (planned)"]
    end
    mac -->|local LLM inference| win
    mac -. talosctl (planned) .-> nebula

    subgraph gh["GitHub org (org-as-code)"]
        infra["infrastructure<br/>Terraform"]
        dotgh[".github<br/>this profile"]
    end
    infra -. manages .-> gh
```

- **AI compute** runs on a dedicated GPU node (Ollama serving quantized coder /
  general models), reached from the workstation over a private Tailscale mesh —
  no ports exposed to the internet.
- **Control plane (planned)** moves to an always-on, low-power node running
  Talos Linux, managed entirely over its API.
- **The org** is defined in Terraform via the `integrations/github` provider —
  repos, branches, protection, secrets, and org settings.

## Tech stack

| Layer | Tooling |
| --- | --- |
| Infra-as-code | Terraform (`integrations/github`), GitHub Actions CI |
| Private network | Tailscale |
| Local AI | Ollama (quantized models on consumer GPU) |
| Control plane (planned) | Talos Linux |

## Repositories

- **[infrastructure](https://github.com/local-homelab/infrastructure)** — the
  org-as-code Terraform and future homelab IaC layers.
- **.github** — this org profile and community-health files.

---

<sub>Built and run as a learning project. Managed as code, applied through CI.</sub>
