# ⚡ nano-workflows

<p align="center">
  <img src="https://img.shields.io/badge/nano--ecosystem-workflows-0284C7?style=for-the-badge&logo=githubactions&logoColor=white" alt="Nano Ecosystem Workflows" />
  <img src="https://img.shields.io/badge/status-active-10B981?style=for-the-badge" alt="Status Active" />
  <img src="https://img.shields.io/badge/license-MIT-6366F1?style=for-the-badge" alt="License MIT" />
</p>

<p align="center">
  <b>Centralized GitHub Actions & Reusable Workflows for the Nano Ecosystem.</b><br/>
  Zero boilerplate. Zero redundant scripts. High reliability across all repositories.
</p>

---

## 📚 Table of Contents
- [Reusable Workflows](#-reusable-workflows)
  - [1. Flutter CI (`flutter-ci.yml`)](#1-flutter-ci-flutter-ciyml)
  - [2. Dart CI (`dart-ci.yml`)](#2-dart-ci-dart-ciyml)
  - [3. Auto Release (`reusable-release.yml`)](#3-auto-release-reusable-releaseyml)
- [Composite Actions](#-composite-actions)
  - [extract-changelog](#extract-changelog)
- [License](#-license)

---

## 🔄 Reusable Workflows

### 1. Flutter CI (`flutter-ci.yml`)
Runs Flutter setup, caching, `flutter pub get`, static analysis (`--fatal-infos`), and tests.

In your Flutter project (e.g. `nano-hub`, `nano-core`):
`.github/workflows/ci.yml`:
```yaml
name: CI

on:
  push:
    branches: [ develop, main ]
  pull_request:
    branches: [ develop, main ]

jobs:
  ci:
    uses: durvalperipato/nano-workflows/.github/workflows/flutter-ci.yml@v1
```

#### Optional Inputs:
| Input | Description | Type | Default |
| :--- | :--- | :---: | :---: |
| `flutter_channel` | Flutter channel | `string` | `'stable'` |
| `flutter_version` | Specific Flutter version (empty uses channel) | `string` | `''` |
| `fatal_infos` | Treat analysis info warnings as fatal errors | `boolean` | `true` |
| `run_tests` | Run flutter test suite | `boolean` | `true` |

---

### 2. Dart CI (`dart-ci.yml`)
Runs Dart SDK setup, `dart pub get`, static analysis, and tests. Ideal for pure Dart packages, CLI tools, and servers (`nano-core-mcp`, `nano-agents`).

In your Dart project:
`.github/workflows/ci.yml`:
```yaml
name: CI

on:
  push:
    branches: [ develop, main ]
  pull_request:
    branches: [ develop, main ]

jobs:
  ci:
    uses: durvalperipato/nano-workflows/.github/workflows/dart-ci.yml@v1
```

---

### 3. Auto Release (`reusable-release.yml`)
Extracts version from `pubspec.yaml`, checks for duplicate tags, extracts version-specific notes from `CHANGELOG.md`, and creates the GitHub Release & Tag. Supports optional deploy hooks (e.g., Render webhooks).

In any project:
`.github/workflows/release.yml`:
```yaml
name: Auto Release

permissions:
  contents: write

on:
  push:
    branches: [ main ]

jobs:
  release:
    uses: durvalperipato/nano-workflows/.github/workflows/reusable-release.yml@v1
    secrets: inherit
```

#### Example with Deploy Webhook (e.g. `nano-core-mcp` on Render):
```yaml
name: Auto Release

permissions:
  contents: write & Tagging

on:
  push:
    branches: [ main ]

jobs:
  release:
    uses: durvalperipato/nano-workflows/.github/workflows/reusable-release.yml@v1
    secrets:
      RENDER_DEPLOY_HOOK_URL: ${{ secrets.RENDER_DEPLOY_HOOK_URL }}
```

---

## 📦 Composite Actions

### `extract-changelog`
A zero-dependency composite GitHub Action that parses `CHANGELOG.md` and extracts the release notes for a specific version.

```yaml
- name: Extract Changelog
  id: changelog
  uses: durvalperipato/nano-workflows/extract-changelog@v1
  with:
    version: '1.0.0'
```

---

## 📄 License
This repository is open-sourced under the [MIT License](LICENSE).
