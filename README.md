# 3Waves-Publisher (compatibility stub)

This repository exists for one reason: to keep the URL
`https://luna-labs-us.github.io/3Waves-Publisher/` alive.

It is not the publishing repo. The real one is
[3Waves-Public](https://github.com/Luna-Labs-US/3Waves-Public).

## Why this exists

The publishing repo used to be named `3Waves-Publisher`, and GitHub Pages served
it at `luna-labs-us.github.io/3Waves-Publisher/`. The Software Manager checks for
updates by fetching `{updateUrl}/version.json`, and that URL is the compiled-in
default (`ConfigManager.cs`, `UpdateUrl` property initializer).

When the repo was renamed to `3Waves-Public`, its Pages site moved with it.
Pages builds its hostname and path from the repo name and does not follow the
rename redirect the way the rest of GitHub does, so the old URL started
returning 404. Every Software Manager in the field kept asking for the old
address and got nothing.

Renaming the repo back was not an option, so this stub takes the old name and
republishes the two manifest files at the path those clients already request.
It holds no packages. The download URLs inside the manifests point at R2, which
is where the actual payloads live.

## What is here

| File | Purpose |
|---|---|
| `version.json` | JSON update manifest. What the Software Manager reads first. |
| `update.xml` | XML manifest. Fallback path used by the update flow. |
| `.nojekyll` | Serve files verbatim, no Jekyll processing. |

## Keeping it in sync

These two files must match the ones at the root of `3Waves-Public` on every
release, except that the URLs inside them point at R2 rather than at a Pages
path. If they drift, clients on the legacy URL will be offered a version that
does not match what the website advertises.

The release build writes the canonical manifests. Copy them here, repoint the
URLs at R2, commit, and push. Pages redeploys on push to `main`.

Verify after any change:

```bash
curl -sI https://luna-labs-us.github.io/3Waves-Publisher/version.json
curl -sI https://luna-labs-us.github.io/3Waves-Publisher/update.xml
```

Both should return 200. A 404 means Pages is off or the branch is wrong.

## Retiring this repo

This stub can go away once no installed Software Manager still points at the old
URL. That requires a released Software Manager whose default `UpdateUrl` is the
R2 bucket, plus confidence that every field install has taken that update.
Until then, deleting this repo breaks update checks for those clients silently.
They do not error loudly, they just stop seeing new versions.
