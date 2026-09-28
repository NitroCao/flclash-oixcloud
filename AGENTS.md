# flclash-oixcloud

AUR package repackaging the **dler.io custom FlClash build** (`https://dl.dler.io/flclash-linux-amd64.deb`).

## Rules

- Never check GitHub releases (chen08209/FlClash) for updates — the deb's version does not follow GitHub tags.
- `pkgver` is the deb's `Version:` field (`control.tar.*/control`) copied verbatim: `<flclash-version>+<buildstamp>`, e.g. `0.8.99+2026092815`.
- `url` in PKGBUILD intentionally points at the maintainer's site (https://oixcloud.com), not upstream.

## Update procedure

1. `curl -sI` the deb URL — if `last-modified` is not newer than the build stamp in the current `pkgver`, stop.
2. Download the deb, `bsdtar -xf` it, read `Version:` from `control.tar.*/control`.
3. Bump `pkgver` and replace the deb's sha256sum (the second sum) in PKGBUILD.
4. `makepkg --printsrcinfo > .SRCINFO`
5. `makepkg -sf` to verify; keep the built `*.pkg.tar.zst` — the installer is usually wanted next.
6. Commit only `PKGBUILD` and `.SRCINFO`, message `Update to <pkgver>`.

## Deb layout assumptions

`prepare()` / `package()` require in `data.tar`:

- `usr/share/applications/FlClash.desktop`
- `usr/share/icons/hicolor/128x128/apps/FlClash.png` and `256x256` (only these sizes)
- payload under `usr/share/FlClash/` (binary + `lib/`)

Verify with `bsdtar -tf data.tar.*` on every update — if paths moved, the PKGBUILD functions need real changes, not just a version bump. If the deb's `Depends:` changed, revisit the `depends` array.
