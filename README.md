# QEMU archive for Abuild

This repository preserves QEMU revisions that were used while developing
[Abuild](https://github.com/mweidle73/abuild). The authoritative QEMU upstream
is [qemu-project/qemu](https://gitlab.com/qemu-project/qemu).

The repository is an archive and compatibility aid, not a new QEMU upstream.
The branches have deliberately separate roles:

- `master` mirrors the authoritative upstream `master` branch.
- `abuild` identifies QEMU 2.4.1, the final revision used by the accepted
  Abuild master history before the dependency was removed.
- `abuild-gh` adds only this README and GitHub maintenance automation to the
  `abuild` source revision.

## Historical Abuild pins

| Abuild QEMU commit | Preservation status | Context |
| --- | --- | --- |
| `76e4e1d23711750f777333654f13cf6baf8d01f1` | Official tag `v0.15.0` | Initial Abuild import |
| `f2770f9e4d64750ff039dafd16b235376da0951f` | Not yet recovered | Historical secunet branch used by accepted Abuild master |
| `32d24131b2d1d98384b96c80f6cd3482550dc68a` | `abuild` and official tag `v2.4.1` | Final accepted Abuild master pin |
| `6689d018e9ae7495640f12ef15d0bd45d16093c3` | Not yet recovered | `abuild-stable-2.4` experiment on `theil-buster-wip` |
| `1562906acfd7f298b0fc58b3c31fa950801e1690` | Not yet recovered | Final `theil-buster-wip` experiment pin |

The three unrecovered commits are absent from the current official QEMU
history, GitHub's global commit index and the externally accessible Factory
mirror. They must be restored from an old internal clone before this archive
can claim complete historical pin coverage.

## How Abuild used QEMU

Abuild configured static `i386-softmmu` and `x86_64-softmmu` targets and used
the resulting emulator in boot-image and ISO unit tests. QEMU was a build and
test dependency; the Abuild release archive did not contain it. The source was
removed from accepted Abuild master in December 2021.

The GitHub Trixie job builds the same two system-emulator targets, runs the
historical upstream test suite and checks the staged executables. It links
against current distribution libraries rather than attempting to reproduce
the former fully static Jessie-era dependency stack. QEMU 2.4.1 predates
Python 3 support, so the CI image builds the final Python 2.7 release from its
official source archive and verifies its SHA-256 digest before use.

Current GLib releases reject duplicate test paths that the original QEMU
2.4.1 suite still used. The check therefore applies upstream QEMU commit
`deb847bfba7ee0ab8151842f5e9cb12d4daad3a3` to a temporary, private source
copy before building. The archived `abuild` source and this branch's QEMU
sources remain byte-identical to the official `v2.4.1` tree.

Run the same check locally from this checkout with:

```sh
.github/ci/run .github/ci/check
```

The container runs without network access, capabilities or root privileges.
It uses Docker's native architecture by default: GitHub validates x86_64,
while an Apple-Silicon developer validates ARM64 without Rosetta. This avoids
Rosetta's incomplete Linux syscall emulation without excluding any QEMU test.
Set `DOCKER_PLATFORM` only for an intentional cross-architecture run; for
example, `DOCKER_PLATFORM=linux/amd64` selects x86_64 explicitly.

The weekly upstream monitor reports drift in the official branch and final
release tags; it never updates this archive automatically.
