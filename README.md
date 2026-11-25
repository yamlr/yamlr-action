# Official GitHub Action for Yamlr

[![GitHub Action](https://img.shields.io/badge/GitHub%20Action-Yamlr-blue?style=flat-square&logo=githubactions&logoColor=white)](https://github.com/yamlr/yamlr-action)
[![Version](https://img.shields.io/badge/Release-v0.1.3-blue?style=flat-square)](https://github.com/yamlr/yamlr-action/releases)
[![Security: Local-First](https://img.shields.io/badge/Security-Local--First%20%7C%20Zero%20Egress-brightgreen?style=flat-square)]()
[![SARIF Upload](https://img.shields.io/badge/SARIF-GitHub%20Code%20Scanning-orange?style=flat-square)]()

The official GitHub Action for [**Yamlr**](https://github.com/yamlr/yamlr). Clinically audit and heal Kubernetes manifests in CI/CD pipelines with native **SARIF** security reporting into GitHub Code Scanning.

---

## 🚀 Quick Start (PR Validation & Security Scanning)

Create `.github/workflows/yamlr-scan.yml` in your repository:

```yaml
name: "Yamlr Manifest Security & Schema Audit"

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  yamlr-audit:
    name: "Audit Kubernetes Manifests"
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Run Yamlr Audit
        uses: yamlr/yamlr-action@v0.1.3
        with:
          path: "./k8s"
          k8s-version: "1.36"
          output: "sarif"
          output-file: "yamlr-results.sarif"
          fail-on: "error"

      - name: Upload Results to GitHub Code Scanning
        uses: github/codeql-action/upload-sarif@v3
        if: always()
        with:
          sarif_file: "yamlr-results.sarif"
```

---

## 🩹 Automated PR Auto-Healing (GitOps Workflow)

Automatically heal broken manifests and submit fixes back into Pull Requests with comment preservation:

```yaml
name: "Yamlr Auto-Heal"

on:
  pull_request:
    paths:
      - 'deploy/**'
      - 'manifests/**'

jobs:
  auto-heal:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
        with:
          ref: ${{ github.head_ref }}

      - name: Run Yamlr Heal
        uses: yamlr/yamlr-action@v0.1.3
        with:
          command: "heal"
          apply: true

      - name: Commit Healed Manifests
        uses: stefanzweifel/git-auto-commit-action@v5
        with:
          commit_message: "chore(k8s): auto-heal manifest schema & deprecation errors [skip ci]"
```

---

## 📊 Inputs Reference

| Input | Description | Default | Required |
| :--- | :--- | :---: | :---: |
| `path` | Path to manifests (file or directory) | `.` | No |
| `command` | Execution mode: `scan`, `heal`, `fmt`, or `diff` | `scan` | No |
| `k8s-version` | Target Kubernetes version schema (e.g. `1.30`, `1.31`, `1.36`) | `1.36` | No |
| `apply` | Automatically apply suggested fixes in-place (when command is `heal`) | `false` | No |
| `fail-on` | Failure severity threshold: `error`, `warning` | `error` | No |
| `output` | Output format: `human`, `sarif`, or `json` | `human` | No |
| `output-file` | Path to write structured report (e.g. `results.sarif`) | — | No |
| `version` | Specific Yamlr release tag to install from official CDN | `v0.1.3` | No |

---

## 📚 Documentation & Resources

- **Main Storefront & Docs**: [github.com/yamlr/yamlr](https://github.com/yamlr/yamlr)
- **Official Docs**: [yamlr.dev/docs](https://yamlr.dev/docs)
