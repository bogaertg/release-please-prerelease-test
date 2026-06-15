# release-please prerelease test

Demonstrates prerelease support (and its limits) with `simple` release strategy.

## Case A — versioning-strategy: prerelease (works)

Config: `release-type: simple` + `versioning-strategy: prerelease` + `prerelease: true` + `prerelease-type: alpha`

Expected: versions get `-alpha` suffix (e.g. `0.0.1-alpha`). GitHub release IS marked as prerelease because `version.preRelease` is set.

## Case B — prerelease flag only, no suffix (broken)

Config: `release-type: simple` + `prerelease: true` (no `versioning-strategy: prerelease`)

Expected by user: GitHub release marked as prerelease.
Actual: GitHub release NOT marked as prerelease.

Root cause — `manifest.ts`:
```ts
prerelease:
  config.prerelease &&
  (!!release.tag.version.preRelease ||  // needs -alpha style suffix
    release.tag.version.major === 0),   // or major=0
```

Case B version stays `0.0.1` (major=0 → condition true for first release only).
Once version hits `1.0.0`, GitHub release will never be marked prerelease regardless of `prerelease: true`.

## Related issue

https://github.com/googleapis/release-please/issues/2565
