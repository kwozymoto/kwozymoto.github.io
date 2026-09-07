# kwozymoto.github.io

The **user site** for this account. It exists for one technical reason, and
carries a landing page because a bare 404 at the account root is scruffy.

## Why it exists

The Koine Greek app is a *project* Pages site at
`https://kwozymoto.github.io/koine-greek/`. Its **origin** is
`https://kwozymoto.github.io` — origins have no path — and Android's Digital
Asset Links must be served from the origin root:

```
https://kwozymoto.github.io/.well-known/assetlinks.json
```

A project site cannot serve that path. Only the user site, this repo, can.
Without it the Trusted Web Activity still installs and runs, but Chrome
cannot verify that the app and the website are the same publisher, so it
keeps a visible URL bar across the top.

## `.nojekyll` is load-bearing — do not delete it

GitHub Pages runs Jekyll by default, and **Jekyll does not copy directories
whose names begin with a dot**. Without `.nojekyll` at the repo root, the
`.well-known/` directory is silently dropped from the build and
`assetlinks.json` returns 404 — with the repo looking completely correct.

That failure is quiet at every step: the file is committed, the build is
green, and only domain verification fails, with no message saying why. The
probe file beside this README is here so the path can be tested at any time:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://kwozymoto.github.io/.well-known/probe.txt
```

`200` means dot-directories are being served. `404` means `.nojekyll` has
gone missing and `assetlinks.json` will not work either.

## Filling in assetlinks.json

Not yet written, deliberately — it needs a real SHA-256 certificate
fingerprint, and a file claiming an association it cannot back is worse than
no file.

**The fingerprint is the one from Play Console, not the local keystore.**
With Play App Signing enabled — the default — Google re-signs the upload with
their own key, so the fingerprint the device checks is Google's, not
Bubblewrap's. Putting the local keystore's fingerprint here is the classic
reason verification fails after a build that worked when sideloaded.

Take it from **Play Console → your app → Test and release → Setup → App
integrity → App signing key certificate → SHA-256 certificate fingerprint**.

Listing both the app signing key and the upload key is worth doing, so the
app verifies whether it came from Play or was sideloaded during testing:

```json
[
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "REPLACE.with.the.applicationId",
      "sha256_cert_fingerprints": [
        "AA:BB:…  ← Play Console app signing key",
        "CC:DD:…  ← the local upload key, for sideloaded test builds"
      ]
    }
  }
]
```

Verify it before trusting it:

```
https://developers.google.com/digital-asset-links/tools/generator
```
