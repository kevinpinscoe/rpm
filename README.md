# kevinpinscoe/rpm

Fedora RPM repository for [kevinpinscoe](https://github.com/kevinpinscoe) Go tools, served via GitHub Pages.

## Install

```bash
sudo dnf config-manager \
  --add-repo https://kevinpinscoe.github.io/rpm/kevinpinscoe.repo
sudo dnf install get-wx
```

Or manually:

```bash
curl -sLO https://kevinpinscoe.github.io/rpm/kevinpinscoe.repo
sudo mv kevinpinscoe.repo /etc/yum.repos.d/
sudo dnf install get-wx
```

## Available packages

| Package | Description |
|---|---|
| `get-wx` | Eastern Tennessee weather forecast fetcher |
| `metar-tool` | METAR aviation weather decoder |
| `skills-tui` | Interactive TUI skill chooser |
| `aws-linux-memory-tools` | AWS Linux memory diagnostics |

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

## Contributing & Reporting Issues

Bug reports, feature requests, security disclosures, and contributions are all
welcome. I keep these guidelines in one place for all my projects:

- **How to contribute or report an issue:** https://github.com/kevinpinscoe/how-to-contribute
- **Report a security vulnerability:** do not open a public issue. Use the
  **"Report a vulnerability"** button on this repository's **Security** tab, or
  see the [security policy](https://github.com/kevinpinscoe/how-to-contribute/blob/main/SECURITY.md).
