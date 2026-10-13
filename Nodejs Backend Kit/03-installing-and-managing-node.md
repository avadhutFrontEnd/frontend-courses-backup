---
title: "Installing and Managing Node"
source: written for this kit (not from geekyfrontend.in)
course: Node.js Backend Kit
tags: [geeky-frontend, nodejs-backend-kit]
---

# Installing and Managing Node

*16 min read · Updated 7 Oct 2026 · Topic 3 of 74 · added to this kit, not part of the original course*

**Source:** written for this kit — examples verified on Node v24.19.0

---

## Why it matters

Before you write a line of server code, something has to put a `node` binary on your machine and make your shell find it. How you do that decides several things you will hit later: whether you can run two projects that need different Node majors, whether `npm install -g` works without `sudo`, whether your laptop, CI and production run the same version, and how painful the next upgrade is.

Most "Node is broken" tickets on a new machine come from this layer, not from JavaScript. Typical ones are `EACCES: permission denied` on a global install, `node -v` printing an old version after you "upgraded", a CLI that "disappeared" after switching versions, or a CI job that passes on Node 24 while a teammate on Node 22 gets `ReferenceError`. Each has a mechanical cause (a directory you cannot write to, an earlier `PATH` entry, a shell cache, a per-version global folder). Once you know the parts, each is a two-minute fix.

## The concept, simply

A Node installation is a **directory** plus a **`PATH` entry** that points into it. The official Linux tarball for v24.21.0 contains, among the docs and headers:

- `bin/node`: the runtime, one executable file of about 120 MB.
- `bin/npm`, `bin/npx`: small launchers for `lib/node_modules/npm`, the bundled npm package.
- `bin/corepack`: the package-manager shim tool. Node 24 still ships it; Node 25 and later do not (shown below).
- `include/node/`: C headers used when native addons are compiled.

Everything else is about **who creates that directory, where it lives, and how your shell is told about it**. There are three families:

1. **Official installer or tarball** from nodejs.org. One version, installed to a fixed system location chosen by the installer, or wherever you unpack the tarball (for example `/usr/local`).
2. **OS package manager** (`apt`, `dnf`, Homebrew, winget, …). One version, chosen and patched by the distribution, updated with the rest of the system.
3. **Version manager** (nvm, fnm, Volta, …). Many versions side by side under your home directory. The manager changes `PATH` (or routes through a shim) so that `node` means the version a project asks for.

On top of that sits npm's **global prefix**: the directory where `npm install -g` puts packages (`<prefix>/lib/node_modules`) and their command links (`<prefix>/bin` on Linux and macOS). If you cannot write to the prefix, you get `EACCES`.

## How it works

What happens when you type `node app.js` in a terminal:

1. **The shell looks up `node`.** It walks the directories in `$PATH` from left to right and runs the first executable named `node` it finds. Bash also remembers the result in a hash table, so later lookups skip the walk until the table is cleared (`hash -r`) or `PATH` is reassigned.
2. **A version manager may have rewritten `PATH`.** `nvm use 22` prepends `~/.nvm/versions/node/v22.x.y/bin`. fnm prepends a per-shell symlink directory that it repoints on `fnm use`. Volta puts `~/.volta/bin` first once, and `node` there is a shim that reads the nearest `package.json` on every call.
3. **The runtime starts.** `process.execPath` holds the absolute path of the binary that actually ran. When `node -v` surprises you, check this value.
4. **npm reads its prefix.** npm takes its config from (among others) `npm_config_*` environment variables, `~/.npmrc` and the built-in default. The default prefix is the directory the running Node was installed into. With a system install that is a root-owned directory such as `/usr` or `/usr/local`. With a version manager it is the version's own folder under your home.

**Choosing an install method.** The trade-offs follow from the three families:

| Method | Versions at once | Who updates it | Global installs need root? | Typical use |
| --- | --- | --- | --- | --- |
| nodejs.org installer / tarball | one | you, by re-running the installer | yes for system locations (`/usr/local`) | a single-purpose machine, a quick start |
| OS package manager | one | the distro, on its own schedule | yes (`/usr`) | servers managed with the rest of the OS packages |
| Version manager | many | you, per project | no (everything is under `$HOME`) | development machines with several projects |

Distribution packages often trail the upstream release line. On this machine (Ubuntu 26.04 LTS), the archive offers a Node 22 build that is older than the newest 22.x on nodejs.org (v22.23.3):

bash

```
$ apt-cache policy nodejs | head -3
nodejs:
  Installed: (none)
  Candidate: 22.22.1+dfsg+~cs22.19.15-1ubuntu1
```

**Version managers compared.** These three were installed into a throwaway `HOME` for this lesson and exercised (outputs in the examples below):

| | nvm | fnm | Volta |
| --- | --- | --- | --- |
| Version tested | 0.40.8 | 1.39.0 | 2.0.2 |
| What it is | a POSIX shell script you `source`; `nvm` is a shell function | a single Rust binary; `eval "$(fnm env)"` sets up the shell | Rust binaries plus a `volta-shim` executable |
| How `node` is switched | rewrites `PATH` in the current shell on `nvm use` | repoints a per-shell symlink dir that is on `PATH` | `~/.volta/bin/node` is a link to the shim, which picks a version on each run |
| Reads | `.nvmrc` only (a lone `.node-version` gave `No version provided and no .nvmrc file found`) | `.node-version`, `.nvmrc`, and `engines.node` in `package.json` when neither file exists (`--resolve-engines`, on by default) | the `"volta"` key in `package.json` |
| Auto-switch on `cd` | not by default (the nvm README has a shell hook you can add) | `fnm env --use-on-cd` | always, because the shim resolves per directory |
| Windows | no (nvm-windows is a separate project) | yes (per its README) | yes (per its README) |
| Maintenance status | active (v0.40.8, Sep 2026) | active (v1.39.0, Mar 2026) | **unmaintained**: the README says "Volta is unmaintained" and recommends migrating to `mise` |

> ⚠️ Unverified: the Windows rows for fnm and Volta come from their READMEs; no Windows machine was available to run them.

**Pinning a version per project.** Each manager reads a different file, so pick the file your team's tool understands, or commit more than one:

- `.nvmrc`: one line, for example `22` or `v24.21.0`. Read by nvm and fnm.
- `.node-version`: one line, same format. Read by fnm (and other tools); **ignored by nvm**.
- `"volta": { "node": "22.23.3" }` in `package.json`. Written by `volta pin node@22`, which resolves `22` to an exact version. Read by Volta.
- `"engines": { "node": ">=22" }` in `package.json` states compatibility, but it does not select a version and does not run at runtime. npm only warns on a mismatch unless `engine-strict=true` (see topic 14).

**LTS vs Current.** Every Node major starts as **Current** for about six months. Until Node 26, odd majors then went end-of-life and even majors became **Active LTS** (about one year), then **Maintenance LTS** (critical and security fixes only), for about 30 months of support in total. The nodejs.org releases page says the cycle changes from Node 27: one major per year, each with six months of Alpha and six months of Current, and every major moves to LTS. Production should run Active LTS or Maintenance LTS only. The schedule in `nodejs/Release/schedule.json`, read on 7 Oct 2026:

| Line | Released | Active LTS from | Maintenance from | End of life | Status on 2026-10-07 |
| --- | --- | --- | --- | --- | --- |
| v20 (Iron) | 2023-04-18 | 2023-10-24 | 2024-10-22 | 2026-04-30 | EOL |
| v22 (Jod) | 2024-04-24 | 2024-10-29 | 2025-10-21 | 2027-04-30 | Maintenance LTS |
| v24 (Krypton) | 2025-05-06 | 2025-10-28 | 2026-10-20 | 2028-04-30 | Active LTS (latest v24.21.0) |
| v25 | 2025-10-15 | — | 2026-04-01 | 2026-06-01 | EOL |
| v26 | 2026-05-05 | 2026-10-28 | 2027-10-20 | 2029-04-30 | Current (latest v26.10.0) |
| v27 | alpha 2026-10-28, release 2027-04-22 | not yet listed | 2027-10-20 | 2030-04-30 | not released |

So in three weeks, v24 drops to Maintenance and v26 becomes the Active LTS. Floating aliases such as nvm's `lts/*` or the Docker tag `node:lts-slim` will move to 26 on that day without any change on your side.

**Corepack.** Corepack is a small tool that reads the `"packageManager"` field of `package.json` (for example `"pnpm@10.34.6"`), downloads exactly that pnpm or Yarn version into a cache, and runs it through shims. The Node v24 docs mark it Stability 1 (Experimental) and say: "Corepack will no longer be distributed starting with Node.js v25. Users currently depending on the bundled corepack executable from Node.js can switch to using the userland-provided corepack module." The tarballs confirm it: v24.21.0 contains `bin/corepack`, and the v25.0.0 and v26.10.0 tarballs contain no file with "corepack" in its path. On Node 25 and later, install it from npm (`corepack` 0.36.0 at the time of writing) or install pnpm or Yarn directly.

**Checksums and signatures.** Every release directory on `nodejs.org/dist/vX.Y.Z/` has `SHASUMS256.txt` (one SHA-256 line per file), `SHASUMS256.txt.sig` (a detached signature) and `SHASUMS256.txt.asc` (the same list, clearsigned). A checksum match only proves the file equals what the list says. The signature, checked against the release keys in `nodejs/release-keys`, proves a Node releaser produced the list. Version managers do part of this for you: nvm printed `Checksums matched!` during every install in this lesson.

**Global installs and `EACCES`.** `npm install -g` writes to `<prefix>/lib/node_modules` and links commands into `<prefix>/bin`. If the prefix is a root-owned system directory, the `mkdir` fails with `EACCES` (errno -13). Running npm with `sudo` makes that error go away, but it runs package install scripts as root and leaves root-owned files in your cache. The fixes that npm's docs recommend are either a version manager (the prefix moves under `$HOME`) or pointing the prefix at a directory you own and adding its `bin` to `PATH`.

**Upgrading safely.** A patch or minor upgrade within one LTS line (24.19.0 → 24.21.0) keeps the native-addon ABI (`process.versions.modules` stays `137`). A major upgrade changes it (`127` on v22, `137` on v24), so compiled addons must be rebuilt (`npm rebuild`) and anything installed globally has to be installed again for the new version. The safe order: install the new version next to the old one, run the test suite on it, update every place that pins the version together (`.nvmrc` / `.node-version` / `"volta"`, `engines`, CI matrix, Docker `FROM`), then remove the old version.

**Docker as an alternative.** The official `node` images bundle a Node version with a Linux userland, so the version is pinned by the image tag (`node:24.21.0-slim`) instead of by a tool on the host. Tags are moving pointers. `node:24-slim` follows the latest v24, and `node:lts-slim` currently has the same digest as `node:24-slim` but will follow 26 after 2026-10-28. Pin a full version, or a digest, for reproducible builds.

## Code examples

**Example 1 — verify a Node download before you trust it.** This follows the commands in the nodejs/node README ("Verifying binaries"), run in an empty directory with a throwaway `GNUPGHOME`.

bash

```
$ VERSION=v24.21.0
$ curl -fsSO "https://nodejs.org/dist/$VERSION/node-$VERSION-linux-x64.tar.xz"
$ curl -fsSO "https://nodejs.org/dist/$VERSION/SHASUMS256.txt.asc"
$ curl -fsSLo nodejs-keyring.kbx "https://github.com/nodejs/release-keys/raw/HEAD/gpg/pubring.kbx"

# 1. Check the signature and extract the signed checksum list
$ gpgv --keyring="$PWD/nodejs-keyring.kbx" --output SHASUMS256.txt < SHASUMS256.txt.asc
gpgv: Signature made Wed 09 Sep 2026 03:19:15 AM IST
gpgv:                using EDDSA key 5BE8A3F6C8A5C01D106C0AD820B1A390B168D356
gpgv: Good signature from "Antoine du Hamel <antoine.duhamel@platformatic.dev>"
gpgv:                 aka "Antoine du Hamel <duhamelantoine1995@gmail.com>"

# 2. Check the tarball against its line in that list
$ grep " node-$VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c -
node-v24.21.0-linux-x64.tar.xz: OK

# 3. A single extra byte is enough to fail
$ cp node-v24.21.0-linux-x64.tar.xz bad.tar.xz && printf 'x' >> bad.tar.xz
$ grep " node-$VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sed 's/node-v24.21.0-linux-x64.tar.xz/bad.tar.xz/' | sha256sum -c -
bad.tar.xz: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
$ echo $?
1
```

The `grep` keeps only the one line you need. Without it, `sha256sum -c SHASUMS256.txt` complains about every other file in the list, unless you add `--ignore-missing`. On macOS the equivalent tool is `shasum -a 256 -c`.

> ⚠️ Unverified: `shasum -a 256 -c` on macOS was not run (no macOS machine available).

**Example 2 — nvm with `.nvmrc`.** nvm was installed with `HOME` set to a throwaway directory and `PROFILE=/dev/null`, so no real shell files were edited. The shell was started with `env -i` so the machine's own Node was not on `PATH` (paths trimmed with `…`).

bash

```
$ . "$NVM_DIR/nvm.sh" && nvm --version
0.40.8
$ nvm ls-remote --lts | tail -3
       v24.19.0   (LTS: Krypton)
       v24.20.0   (LTS: Krypton)
       v24.21.0   (Latest LTS: Krypton)

# In a folder that has only .node-version:
$ cat .node-version
22
$ nvm use
No version provided and no .nvmrc file found
Please see `nvm --help` or https://github.com/nvm-sh/nvm#nvmrc for more information.

# In a folder with .nvmrc:
$ echo 22 > .nvmrc
$ nvm use
Found '…/p-nvm/.nvmrc' with version <22>
Now using node v22.23.3 (npm v10.9.9)
$ command -v node
…/nvmhome/.nvm/versions/node/v22.23.3/bin/node
$ nvm which 24
…/nvmhome/.nvm/versions/node/v24.21.0/bin/node
```

`nvm use` changes only the current shell. A new terminal gets the `default` alias (`nvm alias default 24`).

**Example 3 — fnm reads `.node-version`, `.nvmrc` and `engines`.** Same throwaway-`HOME` setup; the fnm binary lives outside `/tmp`.

bash

```
$ eval "$(fnm env --shell bash)"
$ cat .node-version
22
$ fnm install
Installing Node v22.23.3 (x64)

$ fnm use
Using Node v22.23.3

# a folder with only .nvmrc (containing 22)
$ fnm use
Using Node v22.23.3

# a folder with neither file, only engines
$ cat package.json
{ "name": "eng", "engines": { "node": "22" } }
$ fnm use
Using Node v22.23.3
$ fnm list
* v22.23.3
* v24.21.0 default
* system
```

**Example 4 — Volta pins inside `package.json`.** `VOLTA_HOME` was a throwaway directory; `volta install` printed nothing in this non-interactive shell.

bash

```
$ volta --version
2.0.2
$ volta install node@24
$ node -v
v24.21.0

$ cd vproj && volta pin node@22
$ cat package.json
{
  "name": "vproj",
  "version": "1.0.0",
  "volta": {
    "node": "22.23.3"
  }
}
$ node -v
v22.23.3
$ ls -l $VOLTA_HOME/bin/node
lrwxrwxrwx … node -> …/volta/bin/volta-shim
$ cd .. && node -v
v24.21.0
```

The same `node` path gives a different version depending on the directory, because the shim makes the choice on each run, not the shell.

**Example 5 — corepack on Node 24, and its absence on 25+.**

bash

```
$ node -v
v24.19.0
$ corepack --version
0.35.0

# Inside a throwaway nvm install of v24.21.0, a project with "packageManager": "pnpm@10.34.6"
$ corepack --version
0.36.0
$ corepack enable pnpm
$ ls -l $(dirname $(command -v node))/pnpm
lrwxrwxrwx … pnpm -> ../lib/node_modules/corepack/dist/pnpm.js
$ pnpm --version
10.34.6

# Count files named corepack in the official tarballs
$ curl -sSf https://nodejs.org/dist/v25.0.0/node-v25.0.0-linux-x64.tar.xz | tar -tJ | grep -c corepack
0
$ curl -sSf https://nodejs.org/dist/v26.10.0/node-v26.10.0-linux-x64.tar.xz | tar -tJ | grep -c corepack
0
```

`corepack enable` writes shims into the bin directory of the Node that runs it. That is why it was run only inside the throwaway install.

**Example 6 — reproduce `EACCES` and fix it with the npm prefix.** The "system" prefix here is a throwaway directory with mode `555` (read-only), so nothing real is touched. `HOME` is also a throwaway, so `npm config set` writes a throwaway `.npmrc`.

bash

```
$ mkdir ro-prefix && chmod 555 ro-prefix
$ npm_config_prefix=$PWD/ro-prefix npm install -g semver@7.7.2
npm error code EACCES
npm error syscall mkdir
npm error path …/npmdemo/ro-prefix/lib
npm error errno -13
npm error Error: EACCES: permission denied, mkdir '…/npmdemo/ro-prefix/lib'
…
$ echo $?
243

# Fix for one shell: a prefix you own, and its bin on PATH
$ export npm_config_prefix=$PWD/rw-prefix
$ npm prefix -g
…/npmdemo/rw-prefix
$ npm install -g semver@7.7.2

added 1 package in 592ms
$ command -v semver || echo "semver: not on PATH yet"
semver: not on PATH yet
$ export PATH="$npm_config_prefix/bin:$PATH"
$ semver 1.2.3 -i minor
1.3.0

# Permanent fix: store the prefix in ~/.npmrc (HOME was a throwaway here)
$ unset npm_config_prefix
$ npm config set prefix "$HOME/.npm-global"
$ cat $HOME/.npmrc
prefix=…/npmdemo/home/.npm-global
$ npm prefix -g
…/npmdemo/home/.npm-global
```

In real use you would then add `export PATH="$HOME/.npm-global/bin:$PATH"` to your shell startup file. Do not combine this with nvm. A `prefix` in `~/.npmrc` would stop global packages from following the active version, so nvm refuses to switch:

bash

```
$ echo "prefix=$HOME/g" > ~/.npmrc      # throwaway HOME
$ nvm use 22
Your user’s .npmrc file (${HOME}/.npmrc)
has a `globalconfig` and/or a `prefix` setting, which are incompatible with nvm.
Run `nvm use --delete-prefix v22.23.3` to unset it.
$ echo $?
11
```

**Example 7 — edge case: you installed a newer Node and `node -v` still prints the old one.** Bash caches where it found `node`. Putting a new `node` into a directory that comes earlier on `PATH` does not reset that cache. (Reassigning `PATH` does.) Run in a clean shell whose `PATH` starts with an empty `early-bin` directory, followed by the nvm v22 bin:

bash

```
$ type -a node
node is …/nvmhome/.nvm/versions/node/v22.23.3/bin/node
$ node -v
v22.23.3
$ ln -s …/v24.21.0/bin/node early-bin/node
$ node -v
v22.23.3
$ hash -t node
…/nvmhome/.nvm/versions/node/v22.23.3/bin/node
$ hash -r; node -v
v24.21.0
$ type -a node
node is …/pathdemo/early-bin/node
node is …/nvmhome/.nvm/versions/node/v22.23.3/bin/node
```

`type -a` lists every `node` on `PATH` in order, so it is the first command to run when the version is wrong. `hash -r` (bash) or `rehash` (zsh) clears the cache, and so does opening a new terminal.

**PATH troubleshooting by OS.** The Linux commands below were run on this machine; the others were not.

bash

```
# Linux (bash): which node runs, every node on PATH, and the PATH entries in order
$ command -v node
/home/anish.r/.nvm/versions/node/v24.19.0/bin/node
$ type -a node
node is /home/anish.r/.nvm/versions/node/v24.19.0/bin/node
$ node -p "process.execPath"
/home/anish.r/.nvm/versions/node/v24.19.0/bin/node
$ npm prefix -g
/home/anish.r/.nvm/versions/node/v24.19.0
$ echo "$PATH" | tr ':' '\n'
…
```

bash

```
# macOS (zsh): same idea; zsh uses `rehash` instead of `hash -r`
which -a node
echo $PATH | tr ':' '\n'
rehash
# PATH for login shells is assembled by /usr/libexec/path_helper from /etc/paths and /etc/paths.d/*
cat /etc/paths
```

> ⚠️ Unverified: the macOS commands were not run (no macOS machine available).

text

```
# Windows (PowerShell)
Get-Command node -All          # every node.exe on PATH, in order
where.exe node                 # same, from cmd.exe or PowerShell
$env:Path -split ';'           # PATH entries in order
npm prefix -g                  # global packages live directly in this folder on Windows
```

> ⚠️ Unverified: the Windows commands were not run (no Windows machine available). After changing PATH in System Settings, already-open terminals keep the old value; open a new one.

**Example 8 — upgrading across a major: run the tests on the new version first.** A project pinned to 22 (`.nvmrc`) uses `URLPattern`, which is a global only since v24.0.0. `nvm exec` runs one command on another version without switching the shell:

bash

```
$ nvm exec 24 node --test --test-reporter=spec
Running node v24.21.0 (npm v11.19.0)
✔ matches /users/:id (0.763361ms)
ℹ pass 1
ℹ fail 0
$ nvm exec 22 node --test --test-reporter=spec
Running node v22.23.3 (npm v10.9.9)
✖ matches /users/:id (0.700426ms)
ℹ pass 0
ℹ fail 1
…
  ReferenceError: URLPattern is not defined

# The addon ABI number differs between majors, so native addons need `npm rebuild`
$ …/v22.23.3/bin/node -p "process.versions.modules"
127
$ …/v24.21.0/bin/node -p "process.versions.modules"
137

# Global CLIs belong to one version; copy them across explicitly
$ nvm use 22 && npm install -g semver@7.7.2

added 1 package in 535ms
$ nvm use 24
Now using node v24.21.0 (npm v11.19.0)
$ command -v semver || echo missing
missing
$ nvm reinstall-packages 22
…
$ npm ls -g --depth=0
…/nvmhome/.nvm/versions/node/v24.21.0/lib
+-- corepack@0.36.0
+-- npm@11.19.0
`-- semver@7.7.2
```

The same test also tells you what the test does not cover: once the project uses a v24-only global, `.nvmrc`, `engines`, the CI matrix and the Docker `FROM` line must all say 24 in the same commit.

**Example 9 — Docker instead of a host install.** Docker was not available on this machine, so only the tag lookups were run.

bash

```
$ curl -s https://hub.docker.com/v2/repositories/library/node/tags/24-slim | python3 -c "import json,sys;d=json.load(sys.stdin);print(d['name'],d['last_updated'])"
24-slim 2026-10-06T05:39:52.054532Z
$ for t in 24-slim lts-slim 26-slim; do curl -s https://hub.docker.com/v2/repositories/library/node/tags/$t | python3 -c "import json,sys;print('$t',json.load(sys.stdin)['digest'])"; done
24-slim sha256:d6aa754f16b3197301076f047b5def2f02ea1dbbc2ca920407d46d7ec7f87b20
lts-slim sha256:d6aa754f16b3197301076f047b5def2f02ea1dbbc2ca920407d46d7ec7f87b20
26-slim sha256:930557a230abacbc3f4fd9b8648abf8f4bee1e17cb72195dcdfb2f709bc85b33
```

bash

```
# Run one command on a pinned Node without installing it on the host
docker run --rm node:24.21.0-slim node -v

# Run the project's tests in the container, with the project mounted
docker run --rm -v "$PWD":/app -w /app node:24.21.0-slim npm test
```

> ⚠️ Unverified: the two `docker run` commands were not executed (docker was not installed), so their output is not shown.

## Common mistakes & gotchas

- **Fixing `EACCES` with `sudo npm install -g`.** It hides the cause (a root-owned prefix), runs package scripts as root, and leaves root-owned files in `~/.npm` that break later non-root installs. Move the prefix or use a version manager.
- **Committing `.node-version` for a team that uses nvm.** nvm reads only `.nvmrc` and ignores `.node-version` (Example 2). If the team mixes tools, commit both with the same content.
- **Thinking `engines` selects the Node version.** It does not. npm only warns unless `engine-strict=true`, and nothing checks it at runtime. fnm is the exception that reads it, and only when no version file exists.
- **Expecting global CLIs to survive a version switch.** With nvm and fnm, each version has its own `lib/node_modules`. After `nvm use 24`, CLIs installed under 22 are gone until you reinstall them (`nvm reinstall-packages 22`).
- **"I upgraded but `node -v` is old."** Usually an earlier `PATH` entry (a distro or installer Node in `/usr/bin` or `/usr/local/bin`) or the shell's command cache. Run `type -a node`, then `hash -r`.
- **Depending on bundled corepack.** It is gone from Node 25 onward. A CI step that runs `corepack enable` breaks when the runner moves to 26 unless corepack is installed from npm first.
- **Treating a checksum as proof of origin.** If an attacker can replace the tarball, they can replace `SHASUMS256.txt` too. Verify the signature on the list, then the checksum.
- **Using floating tags or aliases where you need repeatability.** `lts/*`, `node:lts-slim` and `node:24-slim` move. `lts-slim` becomes 26 on 2026-10-28.
- **Starting a new project on Volta.** It still works, but its README says it is unmaintained and will not track new OS releases.

## Best practices

- **Use a version manager on development machines.** It keeps everything under `$HOME` (no `sudo`), allows several versions at once, and makes per-project pinning automatic.
- **Pin the version in one committed file and derive the rest from it.** For example `.nvmrc` with a major (`24`), plus `engines.node`, a CI matrix and a Docker `FROM` that agree with it, all changed in the same commit.
- **Run production on Active or Maintenance LTS.** On 7 Oct 2026 that means 24 (or 22 until April 2027). Plan the move to 26 after it becomes LTS on 28 Oct 2026.
- **Verify manual downloads.** Run `gpgv` on `SHASUMS256.txt.asc` with the nodejs/release-keys keyring, then `sha256sum -c` on the file's line.
- **Never `sudo` npm.** Fix the prefix instead (Example 6), or let a version manager own it.
- **Upgrade side by side.** Install the new version, run `nvm exec <new> npm test` (or the CI matrix), `npm rebuild` native addons, reinstall globals, then switch the pin and remove the old version.
- **In containers, pin a full version tag or a digest.** Use `node:24.21.0-slim` or `node:24-slim@sha256:…`, not `node:latest`.

## ⭐ Interview lens

> **Q: “Why does `npm install -g` fail with EACCES, and what is the right fix?”**  
> A: npm writes global packages to `<prefix>/lib/node_modules` and links binaries into `<prefix>/bin`. With a system install the prefix is root-owned (`/usr` or `/usr/local`), so the `mkdir` fails with errno -13. Fix it by pointing the prefix at a directory you own (`npm config set prefix ~/.npm-global` and add its `bin` to `PATH`), or use a version manager, whose prefix is the version folder under `$HOME`. *Trap:* answering "use sudo". That runs install scripts as root and leaves root-owned files in the cache.

> **Q: “How do nvm, fnm and Volta switch Node versions, mechanically?”**  
> A: nvm is a sourced shell function that rewrites `PATH` in the current shell to point at `~/.nvm/versions/node/vX/bin`. fnm puts a per-shell symlink directory on `PATH` and repoints it on `fnm use` (or on `cd` with `--use-on-cd`). Volta puts a shim named `node` on `PATH`, and on each run the shim reads the nearest `package.json` `"volta"` key and executes that version. nvm reads `.nvmrc`; fnm reads `.node-version`, `.nvmrc` or `engines.node`; Volta reads only its `package.json` key.

> **Q: “What is the difference between LTS and Current, and what would you run in production today?”**  
> A: A new major is Current for about six months. Up to Node 26, even majors then become Active LTS and later Maintenance LTS (about 30 months in all), and odd majors reach end of life. From Node 27, every annual major becomes LTS. In production use Active or Maintenance LTS only. On 7 Oct 2026 that is v24 (Active until 20 Oct, then Maintenance), and v26 becomes Active LTS on 28 Oct.

> **Q: “A colleague says they upgraded Node but `node -v` still shows the old version. How do you debug it?”**  
> A: Run `type -a node` (or `which -a`, or `Get-Command node -All` on Windows) to see every `node` on `PATH` in order. Compare with `node -p process.execPath`. Common causes are an older system install earlier on `PATH`, a version manager's `default` alias, and the shell's command hash. `hash -r` or `rehash` clears the hash, as does a new terminal.

> **Q: “How do you verify a Node binary you downloaded?”**  
> A: Download `SHASUMS256.txt.asc`, verify it with `gpgv` against the nodejs/release-keys keyring, then check the file with `sha256sum -c` against its line in the verified list. The checksum alone only proves the file matches a list that may itself have been replaced.

## Cheat-card

- **An install is:** a directory (`bin/node`, `bin/npm`, `bin/npx`, `lib/node_modules/npm`) plus a `PATH` entry.
- **Three ways to install:** nodejs.org installer or tarball (one version, often root-owned), OS package (distro version, often behind upstream), version manager (many versions, all under `$HOME`).
- **Pin files:** `.nvmrc` (nvm, fnm), `.node-version` (fnm; nvm ignores it), `"volta"` in `package.json` (Volta). `engines` does not pick a version.
- **Volta:** unmaintained per its README; the README points to `mise`.
- **Release lines on 2026-10-07:** 24 Active LTS → Maintenance 2026-10-20; 26 Current → LTS 2026-10-28; 22 Maintenance until 2027-04-30; 20 and 25 EOL.
- **Corepack:** bundled through Node 24 (`corepack --version` → 0.35.0 on v24.19.0); not shipped from v25; install from npm if needed.
- **Verify:** `gpgv --keyring=… --output SHASUMS256.txt < SHASUMS256.txt.asc`, then `grep <file> SHASUMS256.txt | sha256sum -c -`.
- **EACCES:** unwritable npm prefix → `npm config set prefix ~/.npm-global` + `PATH`, or a version manager. Never `sudo`.
- **Wrong version:** `type -a node`, `node -p process.execPath`, `hash -r`.
- **Major upgrade:** ABI changes (127 → 137), so `npm rebuild`, reinstall globals, test with `nvm exec`, and move every pin together.
- **Docker:** pin `node:24.21.0-slim` or a digest; `lts`/`24` tags move.

---

## Learn More

- [Node.js Releases nodejs.org](https://nodejs.org/en/about/previous-releases)
- [Download Node.js nodejs.org](https://nodejs.org/en/download)
- [Verifying binaries github.com/nodejs/node](https://github.com/nodejs/node#verifying-binaries)
- [nodejs/release-keys github.com](https://github.com/nodejs/release-keys)
- [nvm github.com](https://github.com/nvm-sh/nvm)
- [fnm github.com](https://github.com/Schniz/fnm)
- [Understanding Volta docs.volta.sh](https://docs.volta.sh/guide/understanding)
- [Corepack (v24 docs) nodejs.org](https://nodejs.org/docs/latest-v24.x/api/corepack.html)
- [Resolving EACCES permissions errors when installing packages globally docs.npmjs.com](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)
- [node official image hub.docker.com](https://hub.docker.com/_/node)
