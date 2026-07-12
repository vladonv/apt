# vladonv/apt

APT repository for [MonAst](https://github.com/vladonv/monast) (`monast-daemon`, `monast-web`) and its patched [starpy](https://github.com/vladonv/starpy) dependency (`python3-starpy`). Built with `reprepro` and GPG-signed; hosted on GitHub Pages, rebuilt on a schedule (and on demand) by [`.github/workflows/rebuild.yml`](.github/workflows/rebuild.yml), which pulls the latest `.deb` from each source repo's GitHub Releases.

## Usage (Debian 12/13, amd64)

```sh
curl -fsSL https://vladonv.github.io/apt/pubkey.asc | sudo gpg --dearmor -o /usr/share/keyrings/vladonv-apt.gpg
echo "deb [signed-by=/usr/share/keyrings/vladonv-apt.gpg] https://vladonv.github.io/apt stable main" | sudo tee /etc/apt/sources.list.d/vladonv.list
sudo apt update
sudo apt install monast-daemon monast-web
```

## Repository layout

Standard `reprepro` tree - `conf/distributions` defines a single `stable` suite, `main` component, `amd64` architecture (binaries are all `Architecture: all`, but `reprepro` needs at least one real architecture to anchor the `Packages` files against). `db/`, `dists/`, `pool/` are committed to `main` alongside the config, since GitHub Pages serves this branch directly - there's no separate `gh-pages` branch.
