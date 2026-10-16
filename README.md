# kevinpinscoe/rpm

Fedora RPM repository for [kevinpinscoe](https://github.com/kevinpinscoe) Go tools, served via GitHub Pages.

## Install

```bash
sudo dnf config-manager addrepo \
  --from-repofile=https://kevinpinscoe.github.io/rpm/kevinpinscoe.repo
sudo dnf install get-wx
```

That is the `dnf5` syntax used by Fedora 41 and later, which rejects `--add-repo`. On a
`dnf4` system use `sudo dnf config-manager --add-repo https://kevinpinscoe.github.io/rpm/kevinpinscoe.repo`.

Or manually:

```bash
curl -sLO https://kevinpinscoe.github.io/rpm/kevinpinscoe.repo
sudo mv kevinpinscoe.repo /etc/yum.repos.d/
sudo dnf install get-wx
```

## Available packages

| Package | Description |
| --- | --- |
| `get-wx` | Eastern Tennessee weather forecast fetcher |
| `metar-tool` | METAR aviation weather decoder |
| `skills-tui` | Interactive TUI skill chooser |
| `aws-linux-memory-tools` | AWS Linux memory diagnostics |
| `bao-policy-editor` | Terminal editor for OpenBao HCL ACL policies (installs `bpe`) |

## GPG key

Packages and repo metadata are signed with the key at `gpg.key` in this repo.

Fingerprint: `8CD9AAACEE8B5AFB7607BC2B300FD9BDDA1BF809`

To import manually:

```bash
curl -sL https://kevinpinscoe.github.io/rpm/gpg.key | sudo gpg --import
sudo rpm --import https://kevinpinscoe.github.io/rpm/gpg.key
```

## How packages land here

Each Go tool repo dispatches a `new-release` event to this repo when a tag is pushed.
The `add-package.yml` workflow downloads the `.rpm` files from the GitHub release,
signs them with `rpmsign`, regenerates the repo metadata with `createrepo_c`,
signs `repomd.xml`, and pushes everything back to `main`.

Publications run one at a time (`concurrency: rpm-repo-publish`). A release dispatched while
another is publishing waits, then builds on the `main` the first one pushed. Three limits
remain:

- **One waiting run.** GitHub keeps only one pending run per concurrency group. A third
  release dispatched while one runs and one waits cancels the waiting run. It shows as
  *cancelled*, not failed, and has to be dispatched again.
- **Pushes by hand.** Pushing to `main` while an `Add package` run is in progress makes that
  run's push fail. Merge README and other changes only when no run is active.
- **Cache window.** Pages serves files with `max-age=600`. For up to ten minutes after a
  publish, a client can get a `repomd.xml` and `repomd.xml.asc` from different publishes.
  dnf then rejects the signature and skips the repository; it never installs anything
  unverified.

Old metadata is never deleted. The workflow runs `createrepo_c --update --retain-old-md 2`,
and with the `createrepo_c` on the GitHub runner (0.17.3, Ubuntu 24.04) any positive
`--retain-old-md` keeps **every** earlier generation of `repodata/`, not two. A cached
`repomd.xml` therefore always finds the files it names. The cost is about 42 KB per publish.
This does not fix the `repomd.xml` / `repomd.xml.asc` mismatch above, because those two files
keep fixed names. If the runner's `createrepo_c` version changes, check this behaviour again.
