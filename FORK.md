# Fork notes (zhimanai/munim-computer-use)

This is a read-mostly fork of [munimtechnologies/munim-computer-use](https://github.com/munimtechnologies/munim-computer-use).
Upstream tags and releases are canonical; this fork adds only release-mirroring
infrastructure.

## Syncing with upstream

`main` is kept as a fast-forward mirror of upstream. From a local clone:

```bash
git remote add upstream https://github.com/munimtechnologies/munim-computer-use.git
git fetch upstream
git merge --ff-only upstream/main
git push origin main
```

GitHub also offers a one-click **Sync fork** button on the repository home
page: it appears in the banner above the file list whenever `main` is behind
upstream ("This branch is N commits behind …"). If the banner is missing, the
fork is already up to date; the button only renders when a sync is possible.

Keep local changes additive (new files only, no edits to upstream files) so
syncs stay conflict-free. `origin` points at `zhimanai`, `upstream` at the
original repository.

## OSS release mirror

`.github/workflows/upload-oss.yml` mirrors an upstream release into the shared
installer bucket on manual dispatch:

```bash
gh workflow run upload-oss.yml -f tag=v0.13.0   # empty tag = upstream latest
```

- Destination: `https://micius-desktop-installer.oss-cn-beijing.aliyuncs.com/munim-computer-use/<tag>/` (region `cn-beijing`)
- Auth: GitHub Actions OIDC → RAM role `munim-release-publisher` (policy
  `MunimReleasePublish`, scoped to the `munim-computer-use/*` prefix; no
  delete or bucket administration). The repo holds **no** cloud secrets — only
  `DOWNLOAD_*` repo variables.
- Verification: GitHub assets are checked against `SHA256SUMS.txt` before
  upload; a fully identical existing mirror is skipped; a partial or differing
  mirror aborts unless run with `-f overwrite=true`; after upload every object
  is re-downloaded from OSS and re-checked against `SHA256SUMS.txt`.
