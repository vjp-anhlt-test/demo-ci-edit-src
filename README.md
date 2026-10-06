# demo-ci-edit-src

On every push to `main`, [`release.yml`](.github/workflows/release.yml):

1. **build** pushes `ghcr.io/vjp-anhlt-test/demo-ci-edit-src:<sha>` using `GITHUB_TOKEN`.
2. **bump** gets a token for the GitHub App **Larry VJP Test Bot**, runs
   `kustomize edit set image` in [demo-ci-edit-dest](https://github.com/vjp-anhlt-test/demo-ci-edit-dest),
   and opens a PR there as `larry-vjp-test-bot[bot]`.

`GITHUB_TOKEN` can't write to other repos, which is why the App is needed.

## Setup

- App permissions: Contents and Pull requests **Read and write**; installed on `demo-ci-edit-dest`.
- Actions variables: `APP_ID` = `5204947`, `TARGET_REPO` = `demo-ci-edit-dest`.
- Actions secret: `APP_PRIVATE_KEY` = the App's `.pem` private key.
