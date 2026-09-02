# Branching Strategy

To ensure stability and collaboration efficiency, the team will adopt the following branching strategy.

## 1. Branch Naming Convention
All branches must follow a standardized prefix to clearly communicate their purpose. The prefixes are strictly defined below:
- **`feature/`**: For developing new features or functionality.
  - *Example*: `feature/user-authentication`
- **`bugfix/`**: For fixing existing bugs in non-production environments.
  - *Example*: `bugfix/payment-gateway-timeout`
- **`hotfix/`**: For urgent fixes directly addressing issues in the production release.
  - *Example*: `hotfix/login-crash-fix`
- **`release/`**: For preparing a new release (version bump, final QA).
  - *Example*: `release/v2.1.0`
- **`chore/`**: For routine maintenance, dependency updates, or internal configuration that does not impact product functionality.
  - *Example*: `chore/update-eslint-rules`

## 2. Protected Branches
The following branches are strictly protected, requiring adherence to the rules below before any changes can be merged:

- **`main`**: The primary, stable branch reflecting production code.
  - **Protection Settings**: 
    - Require a pull request before merging.
    - Require at least 1 approving review from a CODEOWNER.
    - Require status checks (CI/validation gates) to pass before merging.
    - Block direct pushes (no one can `git push` directly to `main`).
  - **Why**: Keeps production stable. Direct commits and unchecked PRs previously caused the main branch to break roughly every few weeks.

## 3. Branch Lifecycle
1. **Creation**: A developer creates a semantic branch (e.g., `feature/...`) branching off of the most recent `main`. 
2. **Development**: Regular commits are made to the branch with descriptive messages.
3. **Review**: The developer opens a Pull Request against `main`. Automated checks run.
4. **Approval**: Code owners review the changes and approve the PR.
5. **Merge**: The PR is squash-merged into `main` to retain a clean history, and the branch is **deleted** immediately after merge (via GitHub’s "Automatically delete head branches" setting).
6. **Maximum Age**: A feature branch should be kept open for a maximum of **7 days**. Long-lived branches increase merge conflicts and lead to stale integration.

## 4. Sync Policy
To prevent divergence and massive, unresolvable merge conflicts, developers must frequently sync their active feature and bugfix branches with `main`.
- **Frequency**: Developers must sync their active branches with `main` at least **once per day**.
- **Command**: The preferred method for syncing is utilizing a rebase, to keep branch history linear without cluttering it with merge commits:
  ```bash
  git fetch origin
  git rebase origin/main
  ```
  *(If conflicts occur, they should be resolved immediately before continuing work.)*
