# mdns-browser snap

Packages [mdns-browser](https://github.com/hrzlgnm/mdns-browser) as a Snap.
Nothing is compiled: the packaged app is the prebuilt, unbundled Linux binary
(`mdns-browser_linux_x64`) downloaded from the upstream release matching the
pinned tag. This repository only contains packaging metadata and CI:

1. [`snap/snapcraft.yaml`](snap/snapcraft.yaml) points at an upstream
   `source-tag`; CI rewrites this line via the
   [pin-upstream-tag](.github/actions/pin-upstream-tag) action (defaulting to
   the latest upstream release).
2. Snapcraft clones the upstream repo at that tag only for metadata (icon, man
   page); the binary itself is downloaded from that tag's GitHub release and
   verified against the inline `sha256` digest GitHub computes for every
   release asset.

## Install

Once published:

```console
sudo snap install mdns-browser
```

## Releasing

1. Tag a release on `hrzlgnm/mdns-browser` (e.g. `v1.20.0`) and let its
   release workflow finish.
2. Run this repository's **Release snap** workflow with `tagName` set to that
   tag (or empty to package the latest upstream release).
3. CI pins `snapcraft.yaml` to that tag, packages the snap in LXD, smoke tests
   it in an Ubuntu 24.04 container (`mdns-browser --help` must print usage),
   and uploads it to the `stable` channel of the Snap Store.

## Store setup (one-time)

- Register the name: `snapcraft register mdns-browser`
- Create exportable store credentials and add them as the
  `SNAPCRAFT_STORE_CREDENTIALS` repository secret:

  ```console
  snapcraft export-login --snaps=mdns-browser --channels=stable login-file
  gh secret set SNAPCRAFT_STORE_CREDENTIALS < login-file
  rm login-file
  ```

  Note: snapcraft 9 removed support for exporting to stdout (`-`); always
  pass a file name.

If publish credentials are absent, CI still packages and smoke tests the snap;
the publish step is skipped.

## Notes

- Only `amd64` is built, matching the upstream prebuilt Linux binary asset.
- Confinement is `strict`; mDNS discovery works through the `network`
  interface plug.
- In-snap auto-updates are disabled by design (upstream self-disables the
  updater for non-bundled binaries); updates are delivered via snap refresh.
