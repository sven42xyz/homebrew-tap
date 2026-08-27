# sven42xyz/homebrew-tap

Homebrew formulae for my tools.

```sh
brew tap sven42xyz/tap
brew install gitls
```

## Formulae

| Formula | Description | Upstream |
|---|---|---|
| `gitls` | A fast, minimal tool to inspect and act on multiple git repositories | [sven42xyz/gitools](https://github.com/sven42xyz/gitools) |

This tap ships **no bottles** — every formula is compiled from source on your
machine. That keeps the tap maintenance-free, but it does mean a broken or
unusual local toolchain surfaces as a build failure here.

## Troubleshooting

### `unsupported argument '<cpu>' to option '-march='`

```
clang: error: unsupported argument 'westmere' to option '-march='
```

Homebrew appends `-march=<cpu>` to every compiler invocation, derived from the
CPU it detects (`HOMEBREW_OPTFLAGS`, applied by the compiler shim — which is why
the flag does not appear in the compile line in the log). If the compiler
actually being run does not target that architecture, it rejects the name and
the build fails immediately.

This almost always means **your Homebrew installation does not match your CPU
architecture** — most commonly an Intel Homebrew (prefix `/usr/local`) left over
on an Apple Silicon Mac after a migration, alongside a native arm64 toolchain.

Check first:

```sh
brew config     # compare CPU / Rosetta / prefix
brew doctor
```

If the prefix is `/usr/local` on an Apple Silicon Mac, reinstall Homebrew
natively to `/opt/homebrew` — this affects **every** package you build from
source, not just this tap. A quick way to confirm it is not formula-specific:

```sh
brew install --build-from-source jq
```

If that fails the same way, the problem is your Homebrew installation.

### Installing without Homebrew

Every formula here just wraps an upstream `make install`, so you can always
bypass Homebrew entirely:

```sh
curl -L https://github.com/sven42xyz/gitools/archive/refs/tags/v0.5.1.tar.gz | tar xz
cd gitools-0.5.1
make && sudo make install PREFIX=/usr/local
```

Note that a binary installed this way is invisible to Homebrew — if you later
`brew install gitls`, you will have two copies on your `PATH`.

## Reporting problems

Build failures belong [here](https://github.com/sven42xyz/homebrew-tap/issues)
and the issue template asks for `brew config` and the build log — both are
needed to tell a packaging bug apart from a local toolchain problem.

Anything about how a tool *behaves* once installed belongs in its own
repository, e.g. [sven42xyz/gitools](https://github.com/sven42xyz/gitools/issues).
