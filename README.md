# vladonv/apt

Personal APT repository for `.deb` packages built across several projects - currently [MonAst](https://github.com/vladonv/monast) (`monast-daemon`, `monast-web`), its patched [starpy](https://github.com/vladonv/starpy) dependency (`python3-starpy`), and [tg2sip-webrtc](https://github.com/vladonv/tg2sip-webrtc). Not tied to any single project or Debian release - see "Repository layout" below. Built with `reprepro` and GPG-signed; hosted on GitHub Pages, rebuilt on a schedule (and on demand) by [`.github/workflows/rebuild.yml`](.github/workflows/rebuild.yml), which pulls the latest `.deb` from each source repo's GitHub Releases.

## Usage

Pick the codename matching your Debian release:

```sh
curl -fsSL https://vladonv.github.io/apt/pubkey.asc | sudo gpg --dearmor -o /usr/share/keyrings/vladonv-apt.gpg
echo "deb [signed-by=/usr/share/keyrings/vladonv-apt.gpg] https://vladonv.github.io/apt bookworm main" | sudo tee /etc/apt/sources.list.d/vladonv.list
# or: ... https://vladonv.github.io/apt trixie main ...
sudo apt update
sudo apt install monast-daemon monast-web
```

A `stable` codename also exists for back-compat with installs set up before per-release codenames existed - it only ever carries `Architecture: all` packages (monast/starpy), never anything Debian-release-specific. New installs should use `bookworm`/`trixie` directly.

## Repository layout

Standard `reprepro` tree, `main` component, `amd64` architecture. `conf/distributions` defines one `Codename` stanza per target Debian release (currently `bookworm`, `trixie`, plus the legacy `stable`) rather than a single shared one - `Architecture: all` packages (monast, starpy) get included into every codename, while packages that differ per release (currently only tg2sip-webrtc, compiled C++ with distro-specific `${shlibs:Depends}`) only go into the codename(s) they were actually built for. `rebuild.yml` figures out routing automatically from each `.deb`'s own `Version:` field: a build tagged for a specific release carries a `~<codename>` suffix there (the standard `dch --local` convention - see tg2sip-webrtc's own CI for where it's added), anything without one is treated as universal. Adding a new source project is a one-line addition to `rebuild.yml`'s repo list; adding a new target release (e.g. an Ubuntu suite) needs a new `Codename` stanza in `conf/distributions` plus adding that codename to `rebuild.yml`'s `codenames=(...)` array.

`db/`, `dists/`, `pool/` are committed to `main` alongside the config, since GitHub Pages serves this branch directly - there's no separate `gh-pages` branch. Large debug-symbol (`-dbgsym`) packages are deliberately excluded from this repo (stay on each project's GitHub Releases instead) - a git-committed pool isn't the right place for multi-hundred-MB files that would otherwise accumulate across every rebuild.
