# kwozymoto.github.io

A landing page. **The Digital Asset Links reason this repo was created no
longer applies** — see below, because the earlier version of this file said
the opposite and would send you to the wrong place.

## What changed

This repo was made to serve
`https://kwozymoto.github.io/.well-known/assetlinks.json`, because the app
was a *project* Pages site at `/koine-greek/` and an origin has no path, so
only the user site could answer the origin root.

The app then moved to its own domain, **https://everydaykoine.app**. A
custom domain serves its repo at the domain root, so the origin the Android
app verifies against is now `everydaykoine.app`, and its assetlinks file
lives in the `koine-greek` repo instead:

```
https://everydaykoine.app/.well-known/assetlinks.json   ← the one that matters
```

`kwozymoto/koine-greek` carries its own `.nojekyll` and its own
`.well-known/probe.txt` for the same reason described below.

## What this repo still does

- Serves a landing page at the account root, which otherwise 404s
- Keeps `kwozymoto.github.io` resolving for anyone who has it bookmarked.
  `kwozymoto.github.io/koine-greek/` is 301-redirected to the new domain by
  Pages automatically

## `.nojekyll` is load-bearing — do not delete it

Pages runs Jekyll, and **Jekyll does not copy directories whose names begin
with a dot**. Without `.nojekyll` the `.well-known/` directory is dropped
from the build silently: the file is committed, the build is green, and only
the fetch fails.

The probe file is here so the path can be tested:

```bash
curl -s -o /dev/null -w "%{http_code}\n" https://kwozymoto.github.io/.well-known/probe.txt
```

`200` means dot-directories are served. `404` means `.nojekyll` has gone.

Worth keeping even though assetlinks moved: if the app ever moves back to a
github.io URL, this is the path that has to work again.
