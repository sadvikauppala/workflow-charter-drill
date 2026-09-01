# Workflow Charter

This document outlines the core engineering policies and practices that govern how our team builds, validates, releases, and recovers software. Adherence to this charter is required for all team members.

## Validation Gates
To protect the `main` branch from broken code, every Pull Request must pass the following automated validation gates before the merge button becomes available. 

- **Gate Name**: Unit & Integration Tests (CI)
  - **What it checks**: Executes the entire automated test suite to ensure existing and new functionality works flawlessly without regressions.
  - **Failure outcome**: If tests fail, the PR is strictly blocked from merging. The author must investigate, fix the code, and push new commits to re-trigger the tests.
  - **Configuration Location**: `.github/workflows/ci.yml`

- **Gate Name**: ESLint / Linter Check
  - **What it checks**: Analyzes code formatting and adherence to the project's syntax and style guidelines.
  - **Failure outcome**: A failure blocks the merge. The author must fix the formatting violations locally and push.
  - **Configuration Location**: `.github/workflows/ci.yml`

**Bypass Policy**: Under no circumstances—not even extreme deadline pressure or a severe production incident—should these validation gates be bypassed by administrators. Unvalidated code deployed in a rush only compounds an outage.

## Release Practices
Our release cycle transforms stable commits into tagged, trackable deployment artifacts.

- **Versioning Format**: We use Semantic Versioning (SemVer) format `vMAJOR.MINOR.PATCH` (e.g., `v1.2.0`).
- **Tagging Command**: Releases must be tagged in Git to mark their exact commit snapshot and capture deployment history.
  - *Example Tag Command*: `git tag -a v1.2.0 -m "Release v1.2.0: Added payment gateway and user profile settings"`
- **Release Checklist**: Before the tag is created and pushed, the following must be true:
  - 1. All features intended for the release are merged into `main`.
  - 2. The `main` branch is entirely stable and passing all CI checks.
  - 3. A manual QA sanity check has signed off on the branch.
- **Hotfix Process**: Hotfixes must circumvent the normal sprint release cycle. To create a hotfix, an engineer branches off the **most recent release tag** (not necessarily HEAD of `main`), fixes the critical bug, gets expedited reviews, merging fixes directly. Once verified, a new semver patch tag is issued (e.g., `v1.2.1`).

## Rollback Readiness
We prioritize swift recovery over lengthy debugging when the main branch or production environment fundamentally fails.

### Method 1: Git Revert (Shared Branch Problems)
- **When it applies**: A single Pull Request or commit was recently merged into `main` and broke the build or introduced a localized bug, but the larger release is otherwise stable.
- **First Command to Run**:
  ```bash
  git revert <commit-hash>
  ```
*(This safely undoes the bad commit by creating a new forward-moving commit, without rewriting shared remote history.)*

### Method 2: Redeployment from a Previous Tag (Widespread Failures)
- **When it applies**: A new release was deployed to production and caused disastrous, application-wide failures or a system crash. The priority is restoring service for users immediately.
- **First Command to Run**:
  ```bash
  git checkout <previous-stable-tag>
  ```
*(Using this command, you roll back the local state to the known-good release, usually followed by triggering the deployment automation for that tag, e.g., restoring `v1.1.0` to override the broken `v1.2.0`.)*
