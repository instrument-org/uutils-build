# uutils-build

Our patched builds of [uutils](https://github.com/uutils) for Instrument: the Rust reimplementations of GNU coreutils, findutils, diffutils and grep, which Instrument's agent runs for its text commands. Each release has one archive per platform Instrument ships (macOS arm64 and x64, Windows x64 and arm64, Linux x64 and arm64 on musl), with a checksum beside each and a `SHA256SUMS` file covering all of them. Instrument downloads a release at build time and checks it against pinned SHA-256 sums.

Each archive holds `coreutils`, `find`, `xargs`, `diffutils` and `grep`, plus a `licenses/` folder with each project's license texts (MIT, and Apache-2.0 as well for diffutils).

## Versions

`versions.env` names the upstream release of each project. To move to a new one, change the line, push, and check that the workflow's build and smoke test pass. A patch that no longer applies fails the build; refresh it from its upstream PR branch.

## Patches we carry

| Patch | What it does | Upstream | Drop it when |
| --- | --- | --- | --- |
| `patches/findutils/0001-uutils-path-separator.patch` | On Windows, `UUTILS_PATH_SEPARATOR=/` (or `slash`) makes `find` print and match paths with `/` | [uutils/findutils#907](https://github.com/uutils/findutils/pull/907), issue [#906](https://github.com/uutils/findutils/issues/906) | a findutils release includes #907 and `versions.env` points at it |
| `patches/grep/0001-uutils-path-separator.patch` | The same for the paths a recursive `grep` walks | [uutils/grep#143](https://github.com/uutils/grep/pull/143) | a grep release includes #143 and `versions.env` points at it |

coreutils and diffutils are built unpatched. When no patches are left, Instrument can download upstream's own releases instead, except for any platform upstream does not publish (today: Windows arm64 for findutils, diffutils and grep, and musl for diffutils and grep).

## Releasing

Every push builds and smoke-tests all six platforms. Pushing a tag (`git tag 2026.10.15 && git push --tags`) also publishes the archives as a GitHub release. macOS and Windows signing happens later, when Instrument packages the app.
