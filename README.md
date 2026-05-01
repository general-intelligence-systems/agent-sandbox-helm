# agent-sandbox-helm

[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/general-intelligence-systems/agent-sandbox-helm)

Helm charts for [Agent Sandbox](https://github.com/kubernetes-sigs/agent-sandbox), automatically generated from upstream GitHub releases using [helmify](https://github.com/arttor/helmify).

Agent Sandbox provides a `Sandbox` CRD and controller for managing isolated, stateful, singleton workloads in Kubernetes -- designed for AI agent runtimes.

## Prerequisites

- Kubernetes >= 1.27
- Helm >= 3.6

## Install

Add the repo:

```bash
helm repo add agent-sandbox https://general-intelligence-systems.github.io/agent-sandbox-helm
helm repo update
```

Install the core components (CRD, controller, RBAC):

```bash
helm install agent-sandbox agent-sandbox/agent-sandbox
```

Install the extensions (SandboxTemplate, SandboxClaim, SandboxWarmPool):

```bash
helm install agent-sandbox-extensions agent-sandbox/agent-sandbox-extensions
```

Install a specific version:

```bash
helm install agent-sandbox agent-sandbox/agent-sandbox --version 0.2.1
helm install agent-sandbox-extensions agent-sandbox/agent-sandbox-extensions --version 0.2.1
```

## Upgrade

```bash
helm repo update
helm upgrade agent-sandbox agent-sandbox/agent-sandbox
helm upgrade agent-sandbox-extensions agent-sandbox/agent-sandbox-extensions
```

## Uninstall

```bash
helm uninstall agent-sandbox-extensions
helm uninstall agent-sandbox
```

Uninstall extensions first if installed, since they depend on the core controller.

## Available charts

| Chart | Description |
|---|---|
| `agent-sandbox` | Core Sandbox CRD, controller deployment, RBAC, and service |
| `agent-sandbox-extensions` | Extension CRDs (SandboxTemplate, SandboxClaim, SandboxWarmPool) and controller |

## Configuration

### agent-sandbox

| Key | Default | Description |
|---|---|---|
| `agentSandboxController.replicas` | `1` | Controller replica count |
| `agentSandboxController.agentSandboxController.image.repository` | `registry.k8s.io/agent-sandbox/agent-sandbox-controller` | Controller image |
| `agentSandboxController.agentSandboxController.image.tag` | *(appVersion)* | Controller image tag |
| `agentSandboxController.nodeSelector` | `{}` | Node selector for controller pods |
| `agentSandboxController.tolerations` | `[]` | Tolerations for controller pods |
| `agentSandboxController.topologySpreadConstraints` | `[]` | Topology spread constraints |
| `serviceAccount.create` | `true` | Create a service account |
| `serviceAccount.name` | `""` | Service account name override |
| `serviceAccount.annotations` | `{}` | Service account annotations |

### agent-sandbox-extensions

| Key | Default | Description |
|---|---|---|
| `agentSandboxController.replicas` | `1` | Extensions controller replica count |
| `agentSandboxController.agentSandboxController.image.repository` | `registry.k8s.io/agent-sandbox/agent-sandbox-controller` | Controller image |
| `agentSandboxController.agentSandboxController.image.tag` | *(appVersion)* | Controller image tag |
| `agentSandboxController.nodeSelector` | `{}` | Node selector |
| `agentSandboxController.tolerations` | `[]` | Tolerations |
| `agentSandboxController.topologySpreadConstraints` | `[]` | Topology spread constraints |

## How it works

A [sync workflow](.github/workflows/sync.yml) runs every 6 hours, checks for new upstream releases, downloads the official `manifest.yaml` and `extensions.yaml`, converts them to Helm charts with helmify, and tags the result. A [release workflow](.github/workflows/release.yml) then packages the charts and publishes them to GitHub Pages via [chart-releaser](https://github.com/helm/chart-releaser).
