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

## 📦 Available Actions

### 1. `extract-changelog`
A zero-dependency composite GitHub Action that parses `CHANGELOG.md` and extracts the release notes for a specific version.

* **Works out of the box** on `ubuntu-latest`, `macos-latest`, and `windows-latest` runners.
* **Intelligent Version Matching**: Automatically strips leading `v` and build metadata (`+1`, `+25`, etc.).
* **Flexible Headings**: Supports `## 1.0.0`, `## [1.0.0]`, `## v1.0.0` with optional date annotations (e.g., `## [1.0.0] - 2026-09-29`).
* **Safe Fallback**: Generates clean fallback notes if the section isn't found.
* **Exports Both Ways**: Provides a file output (`extracted_release_notes.md`) and a multiline step output string (`${{ steps.changelog.outputs.notes }}`).

---

## 🚀 Quickstart

### Basic Example

Add this step to your release workflow:

```yaml
- name: Extract Changelog
  id: changelog
  uses: durvalperipato/nano-workflows/extract-changelog@v1
  with:
    version: '1.0.0'
```

### Full Release Workflow Example (Flutter / Dart)

```yaml
name: Release

on:
  push:
    branches: [ main ]

jobs:
  release:
    runs-on: ubuntu-latest
    permissions:
      contents: write

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Extract Version from pubspec.yaml
        id: get_version
        run: |
          VERSION=$(grep '^version: ' pubspec.yaml | sed 's/version: //')
          echo "version=$VERSION" >> $GITHUB_OUTPUT

      - name: Extract Changelog Notes
        id: changelog
        uses: durvalperipato/nano-workflows/extract-changelog@v1
        with:
          version: ${{ steps.get_version.outputs.version }}
          changelog_path: 'CHANGELOG.md'

      - name: Create GitHub Release
        uses: softprops/action-gh-release@v2
        with:
          tag_name: "v${{ steps.get_version.outputs.version }}"
          name: "Release v${{ steps.get_version.outputs.version }}"
          body_path: ${{ steps.changelog.outputs.notes_file }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

---

## ⚙️ Action Reference: `extract-changelog`

### Inputs

| Input | Description | Required | Default |
| :--- | :--- | :---: | :--- |
| `version` | Version string to extract (e.g. `1.0.0`, `v1.0.0`, `1.0.0+1`) | **Yes** | — |
| `changelog_path` | Relative path to your changelog file | No | `CHANGELOG.md` |
| `output_file` | Output file path to write extracted markdown | No | `extracted_release_notes.md` |
| `fallback_message` | Custom text if header is missing in changelog | No | `Release notes for v{version}` |

### Outputs

| Output | Description |
| :--- | :--- |
| `notes` | The extracted markdown release notes as a multiline string. |
| `notes_file` | Path to the generated file containing the notes. |
| `found` | Boolean (`true` or `false`) indicating if the section was matched. |

---

## 🗺️ Ecosystem Roadmap
- [x] Composite Action: `extract-changelog`
- [ ] Reusable Workflow: `flutter-ci.yml` (Lint, Test, Analyze)
- [ ] Reusable Workflow: `flutter-release.yml` (Tagging, Release, Web deployment)
- [ ] Reusable Workflow: `package-publish.yml` (pub.dev automated publishing)

---

## 📄 License
This repository is open-sourced under the [MIT License](LICENSE).
