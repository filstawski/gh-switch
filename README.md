# gh-switch

Switch your **active GitHub CLI user** and keep **global Git** in sync: **`user.name`**, **`user.email`**, and **HTTPS credentials** for the same account.

The **`gh switch`** command wraps [`gh auth switch`](https://cli.github.com/manual/gh_auth_switch), then runs [`gh auth setup-git`](https://cli.github.com/manual/gh_auth_setup-git) for that host and updates your global Git identity from the API. That way you are not stuck with `gh` on one account while Git still commits or authenticates as another—see [this `cli/cli` discussion](https://github.com/cli/cli/issues/8678).

**Repository:** [github.com/filstawski/gh-switch](https://github.com/filstawski/gh-switch)

## Requirements

- [GitHub CLI](https://cli.github.com/) (`gh`) — log in to each account with [`gh auth login`](https://cli.github.com/manual/gh_auth_login) first
- [Git](https://git-scm.com/)
- **Bash** on your PATH when `gh` runs the extension (included with **Git for Windows**; see [GitHub’s extension docs](https://docs.github.com/github-cli/github-cli/creating-github-cli-extensions))

## Install

```bash
gh extension install filstawski/gh-switch
```

You get a new top-level command **`gh switch`** (the repo ships the `gh-switch` executable, as required for extensions).

Upgrade:

```bash
gh extension upgrade switch
```

**From a local clone** (contributing or pinning your own copy):

```bash
gh extension install .
```

## Usage

Same flags as **`gh auth switch`**:

```bash
# Pick host and account interactively
gh switch

# Choose account explicitly
gh switch --hostname github.com --user monalisa

# GitHub Enterprise Server
gh switch --hostname github.example.com --user my-username
```

Full flag reference:

```bash
gh switch --help
```

## Behavior

1. **`gh auth switch`** — passes your arguments through (including prompts).
2. **Host** — `--hostname` if you set it; otherwise the active host from **`gh auth status -a`**, then **`github.com`** if needed.
3. **`gh auth setup-git --hostname …`** — Git over HTTPS uses the same credential helper as GitHub CLI for that host.
4. **Name and email** — from **`gh api user`**; if the profile email is empty, tries **`user/emails`** for the primary address.
5. **Fallback email** — if there is still no usable address (for example **`user/emails`** needs the **`user`** OAuth scope), uses **`{id}+{login}@users.noreply.github.com`**.
6. **`git config --global user.name`** and **`user.email`** — set from the resolved display name and email.

## Optional: primary private email from the API

If you want **`user/emails`** to succeed when your public profile hides email, add the **`user`** scope and refresh:

```bash
gh auth refresh -h github.com -s user
```

## Notes

- Only **global** Git config is changed (`--global`), not per-repo settings.
- **`gh switch`** is this extension; **`gh auth switch`** is still the built-in subcommand on `gh`.

## License

See the license in the [GitHub repository](https://github.com/filstawski/gh-switch) (for example the `LICENSE` file if present on the default branch).
