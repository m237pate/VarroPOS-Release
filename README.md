# VarroPOS-Release

Release artifacts and the over-the-air update feed for VarroPOS (ADR-048).

**There is no source code here, and there never should be.** This repository is
public so that registers can reach the feed without credentials. The application
source lives in a separate private repository.

## Layout

```
feed/
  latest.manifest.json       signed release manifest for the `latest` channel
  latest.manifest.json.sig   ed25519 multi-signature envelope
  latest.yml                 the channel file electron-updater consumes
  beta.manifest.json         the same three, for the `beta` channel
  beta.manifest.json.sig
  beta.yml
  versions.json              published version ledger
  migrations-manifest.json   migration number -> sha256 -> first published version
```

Installers (`VarroPOS-Setup-<version>.exe` and its `.blockmap`) are attached as
**GitHub Release assets**, not committed here — the small signed documents are
served from the branch so their URLs are stable and independent of release tags.

## How a register decides to trust this

A register does **not** trust this repository. It trusts two ed25519 public keys
compiled into its own binary. Every manifest here is verified against those keys
**over the raw bytes, before anything parses them**, and is additionally checked
for channel, platform, product, expiry and a monotonic sequence number that
makes a replayed older manifest unusable.

So write access here is not by itself enough to ship code to a register. The
signing keys are held offline on removable media and are not in any repository,
public or private.

## The `latest` channel is not live

Until the P2 signing round trip passes its negative tests, no `latest.*` object
is published here at all, and `publish-release.sh` refuses the `latest` target.
That gate is mechanical on purpose: the absence of the file is the enforcement,
not a flag someone can forget to set.
