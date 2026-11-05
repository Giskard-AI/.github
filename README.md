# .github

Org-wide defaults for Giskard-AI: community health files, the shared Renovate preset and, soon, workflow templates.

This repository is public on purpose: GitHub only applies default community health files from a public `.github` repository, and only offers its workflow templates to public repositories when it is public. Do not put anything sensitive here.

## Community health files

| File | Purpose |
| --- | --- |
| `SECURITY.md` | How to report a vulnerability privately |
| `CONTRIBUTING.md` | Short, generic contribution guide |
| `.github/ISSUE_TEMPLATE/` | Bug report and feature request forms; blank issues disabled |
| `.github/pull_request_template.md` | Default pull request body |

GitHub shows these files in every Giskard-AI repository, public or private, that does not have its own file of the same type. To override a default, add your own file to the repository. For issue templates the whole folder is replaced: if a repository has anything in its own `.github/ISSUE_TEMPLATE/`, none of the defaults from this folder are used.

`.github/CODEOWNERS` and `.github/workflows/` only apply to this repository.

## Renovate presets

| Preset | Use it in |
| --- | --- |
| `github>Giskard-AI/.github` (`default.json`) | Every code repository |
| `github>Giskard-AI/.github:automerge` (`automerge.json`) | Repositories whose CI runs tests |

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>Giskard-AI/.github", "github>Giskard-AI/.github:automerge"]
}
```

The default preset runs weekly (before 4am on Monday), waits 7 days after a release before proposing it, raises security fixes immediately, refreshes lock files weekly, and groups updates per package manager. It does not pin dependency ranges, so it is safe for published libraries.

The automerge preset merges patch and minor updates of development dependencies, GitHub Action digest updates, and lock file maintenance once checks pass. It never automerges major updates or production dependencies. Only use it if the default branch requires status checks to pass: GitHub's auto-merge merges immediately when no check is required.

The presets do nothing until a repository extends them.

## Workflow templates

Coming in a follow-up: templates that call the reusable workflows from `Giskard-AI/giskard-actions`.
