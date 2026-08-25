# mdns-browser snap

Builds [mdns-browser](https://github.com/hrzlgnm/mdns-browser) as a Snap. The
snap source is pulled directly from the upstream repository at the tag being
packaged; this repository only contains packaging metadata and CI.

## Install

Once published:

```console
sudo snap install mdns-browser
```

Local build (requires LXD):

```console
sg lxd -c "snapcraft --use-lxd"
sudo snap install ./mdns-browser_*.snap --dangerous
```

## Releasing

1. Tag a release on `hrzlgnm/mdns-browser` (e.g. `v1.20.0`) and let its
   release workflow finish.
2. Run this repository's **Release snap** workflow with `tagName` set to that
   tag.
3. CI builds the snap in LXD, smoke tests it in an Ubuntu 24.04 container, and
   uploads it to the `stable` channel of the Snap Store.

## Store setup (one-time)

- Register the name: `snapcraft register mdns-browser`
- Create exportable store credentials and add them as the
  `SNAPCRAFT_STORE_CREDENTIALS` repository secret:

  ```console
  snapcraft login --with export  # then: snapcraft export-login --snaps=mdns-browser --channels=stable -
  ```

If publish credentials are absent, CI still builds and smoke tests the snap;
the publish step is skipped.

## Notes

- Confinement is `strict`; mDNS discovery works through the `network`
  interface plug.
- In-snap auto-updates are disabled by design (upstream self-disables the
  updater for non-bundled binaries); updates are delivered via snap refresh.
