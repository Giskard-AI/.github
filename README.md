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
| `github>Giskard-AI/.github:automerge` (`automerge.json`) | Repositories that meet the automerge prerequisites below |

The presets do nothing until a repository extends them.

### Adopting the presets

1. Add a `renovate.json` at the root of the repository:

   ```json
   {
     "$schema": "https://docs.renovatebot.com/renovate-schema.json",
     "extends": ["github>Giskard-AI/.github"]
   }
   ```

2. Once the [automerge prerequisites](#automerge-preset) hold, add the automerge preset after the default one:

   ```json
   "extends": ["github>Giskard-AI/.github", "github>Giskard-AI/.github:automerge"]
   ```

3. To change a setting for one repository, set it in that repository's `renovate.json`. Settings written there override the presets, and `packageRules` are appended after the presets' rules, so a later rule wins:

   ```json
   {
     "extends": ["github>Giskard-AI/.github"],
     "schedule": ["* 0-6 * * *"],
     "packageRules": [
       { "matchPackageNames": ["some-package"], "enabled": false }
     ]
   }
   ```

### Default preset

- Runs on Mondays between midnight and 7am (Europe/Paris) and groups updates per package manager.
- Waits 7 days after a release before proposing it. Images on GHCR and Quay have no release timestamp, so they are proposed without the wait.
- Raises security fixes immediately, outside the schedule and without the 7-day wait.
- Refreshes lock files weekly. Lock file maintenance has no release timestamp, so the 7-day wait cannot apply to it in Renovate. Set the cooldown in the package manager instead:
  - uv: `exclude-newer = "7 days"` under `[tool.uv]` in `pyproject.toml`;
  - pnpm: `minimumReleaseAge: 10080` (minutes) in `pnpm-workspace.yaml`;
  - npm: `min-release-age=7` (days) in `.npmrc`.
- Does not pin dependency ranges, so it is safe for published libraries.
- Leaves commit style to Renovate's auto-detection. In a repository that publishes with release-please or semantic-release, a forced `fix(deps):` title would trigger a patch release every week. To get semantic titles without release bumps, add `":semanticCommits", ":semanticCommitTypeAll(chore)"` to `extends`.

### Automerge preset

Automerges, once checks pass, patch and minor updates of development dependencies and the first pinning of GitHub Actions to a commit SHA. It never automerges:

- major updates or production dependencies;
- lock file maintenance, because a lock file refresh can pull in production releases published the same day;
- GitHub Action digest updates, because a new digest for the same tag means the tag was moved to another commit;
- security fixes, because they skip the 7-day wait and must not also skip review.

Prerequisites before extending `github>Giskard-AI/.github:automerge`:

- The repository's own test jobs are required status checks. A required check that does not run the tests is not enough: a PR with failing tests would still merge.
- If the branch rules require an approving review, automerge only completes once something approves Renovate PRs. Never give Renovate a bypass of the branch rules: that would also skip every other required check.

## Workflow templates

Because this repository is public, GitHub offers the workflow templates stored here to every Giskard-AI repository in the "New workflow" page. The templates, which call the reusable workflows from `Giskard-AI/giskard-actions`, arrive in a follow-up pull request.
