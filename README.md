# voidcode-mac

**macOS installers for [VoidCode](https://github.com/). This repository holds no source code.**

Every release here is a `.dmg` built from the VoidCode monorepo and a `SHA256SUMS.txt` listing its
checksums. Nothing else. The application, its issue tracker and its history are in the main
repository; this one exists so that downloading VoidCode for a Mac is one page with one file on it.

→ **[Latest release](../../releases/latest)**

## Which file

| Your Mac | File |
|---|---|
| Apple silicon (M1, M2, M3, M4) | `VoidCode-<version>-mac-arm64.dmg` |
| Intel (pre-2020) | `VoidCode-<version>-mac-x64.dmg` |

Apple menu → **About This Mac**. "Chip: Apple M…" is Apple silicon; "Processor: Intel" is the Intel
build. Downloading the wrong one costs you a file that will not open, so it is worth the ten seconds.

## Installing

1. Open the `.dmg` and drag **VoidCode** to Applications.
2. The first launch is refused: *"VoidCode cannot be opened because Apple cannot check it for
   malicious software."*
3. Open **System Settings → Privacy & Security**, find the message about VoidCode, and choose
   **Open Anyway**.

On macOS 15 and later the old Control-click → **Open** shortcut no longer clears this. System
Settings is the documented path.

### Why that prompt appears

The build is **ad-hoc signed and not notarised**, and the distinction matters.

Notarisation needs a paid Apple developer account. Without it Gatekeeper shows the dialog above —
that is Apple's documented behaviour for an unnotarised app, and clicking through it is expected.

The ad-hoc signature is a separate thing and it is why the app launches at all. Apple silicon
refuses to execute a binary with *no* signature, and an unsigned build told Apple silicon users the
app was **"damaged"** — with no **Open Anyway**, because that refusal happens in the kernel before
Gatekeeper's policy check and is indistinguishable from a corrupted download. The build now runs
`codesign --force --deep --sign -`: a signature with no identity, which satisfies the kernel and
leaves exactly the Gatekeeper prompt described above.

So: `Signature=adhoc` is the intended state, and CI asserts it. An *identity* appearing there would
mean a certificate had leaked into a public build.

**One honest caveat.** Nobody on this project has a Mac. The prompt wording and the System Settings
path are Apple's documentation, not something observed on a machine here. If what you see differs,
please open an issue on the main repository — that is genuinely useful information.

## Verifying what you downloaded

An unsigned installer means you cannot tell who built it from the file itself. The checksums are the
substitute, and they are worth thirty seconds because they are the only provenance available.

```bash
cd ~/Downloads
shasum -a 256 -c SHA256SUMS.txt --ignore-missing
```

`--ignore-missing` because `SHA256SUMS.txt` lists every asset in the release and you have downloaded
one. Without it, every file you did not download reports as failed and the one that matters is lost
in the noise.

Expect `VoidCode-<version>-mac-arm64.dmg: OK`. Anything else — and especially `FAILED` — means do
not install it.

## Releases are drafts until a human publishes them

CI attaches the installers here as a **draft**. A person reads the build, checks it, and publishes.
That is deliberate: the download page reads `releases/latest`, which excludes drafts, so a bad build
cannot become the thing visitors are handed before anyone has looked at it.

## What is not here

- **No source code.** It is in the main repository, under Apache-2.0.
- **No installers in git.** They are release assets. A repository carrying binaries grows without
  bound and makes `git clone` a download of every version ever shipped.
- **No Windows or Linux builds.** Windows is in the Windows repository; Linux (AppImage and `.deb`)
  is attached to the main repository's own releases.

## Licence

Apache-2.0. See [LICENSE](LICENSE) and [NOTICE](NOTICE).
