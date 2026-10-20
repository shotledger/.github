# `.github`

Repository settings as code for the [@shotledger](https://github.com/shotledger)
organization, applied by [`github/safe-settings`](https://github.com/github/safe-settings)
from `.github/workflows/safe-settings.yml`: on every push to `main` that touches
`safe-settings/`, weekly for drift correction, and on manual dispatch.

**This repository must stay private** until the open- or closed-source decision
for ShotLedger is made. A private `.github` repository is not used for org-wide
community health files or templates; it only holds this configuration.

| Path | Owns |
|---|---|
| `safe-settings/settings.yml` | org-wide repository defaults (squash-only, branch cleanup, sign-off, security toggles) |
| `safe-settings/suborgs/product.yml` | `shotledger`: its full label set, auto-merge, and the `protect-main` and `protect-tags` rulesets, and its environments |
| `safe-settings/suborgs/meta.yml` | this repo: the defaults only, labels untouched |
| `safe-settings/deployment-settings.yml` | scope: an empty exclude list, deliberately |

Things that bite:

- **safe-settings deletes any label not listed** for a repo that declares labels.
  Never prune the list in `product.yml` to tidy it; add a label there before (or
  with) anything that uses it.
- **Rulesets and auto-merge only on a public repo.** The org is on the free
  plan: a repository ruleset on a private repo fails the sync, so
  `shotledger`'s rulesets and `allow_auto_merge` can only be applied once that
  repo is public, and so can a required reviewer on an environment.
- **safe-settings deletes any environment not listed** for `shotledger`. Declare
  a new environment in `product.yml` before (or with) the workflow that uses it.
- **A required status check that a repo never produces blocks every PR on it
  forever.** A new context must have run on a real PR before it is added.
- **There is no `DRY_RUN`.** The knob is `FULL_SYNC_NOP`, exposed as the `nop`
  dispatch input, and it crashes in 2.1.18, so it is not a usable preview. Diff
  intent against the live API instead.
- **Merging a settings PR is the apply.** There is no separate step.

Authentication is the org's safe-settings GitHub App: variable
`SAFE_SETTINGS_APP_ID` and secret `SAFE_SETTINGS_PRIVATE_KEY` on this repository.
The workflow runs only on `push` to `main`, on schedule and on manual dispatch,
so the key is never exposed to a fork.
