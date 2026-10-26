# brielov/homebrew-tap

[Homebrew](https://brew.sh) formulae for my projects. Works on macOS and Linux.

```sh
brew install brielov/tap/<formula>
```

Or tap once and install by name:

```sh
brew tap brielov/tap
brew install <formula>
```

## Formulae

| Formula | Description |
|---|---|
| [`crosscheck`](https://github.com/brielov/crosscheck) | Have two coding agents review a plan against your repository |

## How it works

Formulae are generated, not hand-edited. [`packages.json`](packages.json) lists each project. The [update workflow](.github/workflows/update.yml) runs hourly, and you can also start it by hand. For each project it:

1. reads the project's latest GitHub release and its `checksums.txt`;
2. regenerates `Formula/<name>.rb` with [`scripts/update-formulae.ts`](scripts/update-formulae.ts);
3. runs `brew style`, `brew install` and `brew test` on every changed formula, on Linux and macOS;
4. commits only after those pass.

To pick up a release right away:

```sh
gh workflow run update.yml -R brielov/homebrew-tap                    # all formulae
gh workflow run update.yml -R brielov/homebrew-tap -f package=crosscheck
```

## Adding a project

The project's releases must follow these conventions (crosscheck's [release workflow](https://github.com/brielov/crosscheck/blob/main/.github/workflows/release.yml) is a working example):

- **Tag:** `v<version>`, e.g. `v1.2.3`.
- **Archives:** one per supported platform, named `<name>-<version>-<os>-<arch>.tar.gz`, where `os` is `darwin` or `linux` and `arch` is `arm64` or `x64`. Each contains the executable, optionally inside a single top-level directory. Missing platforms are simply left out of the formula.
- **Checksums:** a `checksums.txt` asset in `sha256sum` format covering the archives.
- **Version flag:** the executable prints its version with `--version`. The formula test checks that.

Then add an entry to `packages.json`:

```json
{
  "name": "mytool",
  "repo": "brielov/mytool",
  "desc": "One-line description, no trailing period",
  "license": "MIT",
  "dependencies": [],
  "binaries": ["mytool"]
}
```

`binaries` defaults to `[name]`, and `homepage` defaults to the repository URL. Run the workflow, and the new formula is committed once it installs and tests cleanly on both platforms.

A project that doesn't fit these conventions can get a hand-written formula in `Formula/`. The workflow only touches formulae listed in `packages.json`.

## License

[MIT](LICENSE)
