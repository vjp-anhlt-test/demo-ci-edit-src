# demo-ci-edit-src

Minimal demo: a workflow in this repo (the **source**) uses the GitHub App
**Larry VJP Test Bot** to push a branch and open a pull request in another repo
(the **target**) in the same org, `vjp-anhlt-test`.

The default `GITHUB_TOKEN` can only write to the repo the workflow runs in, so the
workflow exchanges the App's private key for a short-lived installation token
scoped to the target repo ([`actions/create-github-app-token`](https://github.com/actions/create-github-app-token)).
The commit and the PR are authored by `larry-vjp-test-bot[bot]`.

## Setup

1. **App permissions** — in the App settings → *Permissions & events* →
   *Repository permissions*, set:
   - Contents: **Read and write**
   - Pull requests: **Read and write**

   Then accept the updated permissions on the org installation
   (https://github.com/organizations/vjp-anhlt-test/settings/installations/168340201).

2. **Target repo** — [`vjp-anhlt-test/demo-ci-edit-dest`](https://github.com/vjp-anhlt-test/demo-ci-edit-dest)
   needs at least one commit on its default branch, and must be in the App
   installation's *Repository access* list (or use *All repositories*).

3. **This repo** — under *Settings → Secrets and variables → Actions*:

   | Kind     | Name              | Value                                      |
   |----------|-------------------|--------------------------------------------|
   | Variable | `APP_ID`          | `5204947`                                  |
   | Variable | `TARGET_REPO`     | `demo-ci-edit-dest` (repo name, no owner)  |
   | Secret   | `APP_PRIVATE_KEY` | contents of the App's `.pem` private key   |

   Generate the private key under the App settings → *Private keys*.

## Run

*Actions → Open PR in target repo → Run workflow*, or:

```bash
gh workflow run open-pr.yml -R vjp-anhlt-test/demo-ci-edit-src -f message="hi"
gh pr list -R vjp-anhlt-test/demo-ci-edit-dest
```

## Notes

- The token is limited to the target repo and to `contents:write` +
  `pull-requests:write`, even if the App is granted more.
- If the PR would change files under `.github/workflows/`, the App also needs the
  **Workflows: Read and write** permission (and `permission-workflows: write`).
- PRs opened with an App token *do* trigger workflows in the target repo, unlike
  PRs opened with `GITHUB_TOKEN`.
