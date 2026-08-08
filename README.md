# .NET iOS Runtime Lab

A deliberately small, manually gated GitHub Actions runner for private .NET iOS
runtime experiments.

The public repository contains no application source, proprietary input, signing
material, or build output. A short-lived GitHub App installation token checks out
one explicitly selected private repository only after an environment reviewer
approves the run. The private repository owns the build script and uploads its
result back to private storage.

## Security model

- Runs are manual only (`workflow_dispatch`).
- Environment secrets are withheld until `private-build` is approved.
- The GitHub App is installed on one private repository with only the repository
  permissions needed by its build script.
- Tokens expire automatically and are never stored in this repository.
- Public Actions artifacts and caches are intentionally unused.
- Pull requests and forks do not receive protected environment secrets.
- Third-party actions are pinned to full commit hashes.

Review the selected commit before approving a run. Code from the selected private
ref executes with the temporary installation token, so environment approval is the
main trust boundary.

## Required environment secrets

Create an environment named `private-build`, add a required reviewer, and store:

| Secret | Purpose |
| --- | --- |
| `BUILD_APP_ID` | GitHub App ID |
| `BUILD_APP_PRIVATE_KEY` | GitHub App private key contents |
| `PRIVATE_OWNER` | Owner of the private source repository |
| `PRIVATE_REPOSITORY` | Private source repository name |
| `PRIVATE_REF` | Exact private branch, tag, or commit to build |
| `INPUT_RELEASE` | Private input release tag |
| `INPUT_ASSET` | Private input asset name |
| `OUTPUT_RELEASE` | Private output release tag |

The private repository must provide `ci/hosted-device-build.sh`. It is responsible
for dependency preparation, compilation, packaging, and private result upload.

## License

MIT
