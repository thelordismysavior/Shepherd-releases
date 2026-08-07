# Shepherd release artifacts (developers)

This repository publishes **immutable signed Shepherd Android App Release APKs**.
It is not the application source repository.

The public [README](./README.md) is written for end users. This page keeps the
previous technical documentation for developers and operators.

## What ships here

Each GitHub Release holds one universal APK for package
`com.maxidiayni.shepherd`, named `shepherd-<versionLabel>-<releaseNumber>.apk`.
Release assets are never overwritten. The public updater manifest
(`latest.json` in Supabase Storage) points at the current immutable APK URL in
this repository.

| Field | Meaning |
| --- | --- |
| `versionLabel` | Manual `major.minor.patch` label shown to users |
| `releaseNumber` | Android `versionCode`; the only value used to decide “newer” |
| APK URL | Absolute HTTPS URL to this repository’s Release asset |
| Size + MD5 | Corruption check before Android verifies package identity and signature |

## In-app auto updates

Updater-enabled Shepherd builds check the public manifest after the normal Sign
In or Home screen appears:

1. If `releaseNumber` is higher than the installed App Release, Shepherd offers
   **Update** or **Later**.
2. **Update** downloads the APK with visible progress, verifies size and MD5,
   then opens Android’s package installer. Shepherd never silently installs.
3. **Later** dismisses the prompt for the current process; a cold launch offers
   the same newer release again.
4. Verified downloads are reused across retries; cancelled, corrupt, superseded,
   or already-installed cache files are cleaned up.
5. If install-from-source permission is missing, Shepherd guides the user to
   Settings and keeps the verified APK for retry.

Development builds, Expo Go, iOS, and web do not fetch or install App Releases.
Existing devices need one manual bootstrap install of an updater-enabled APK;
later releases can be offered from inside Shepherd.

## Latest App Release: `shepherd-1.0.1-3`

Published artifact:
[`shepherd-1.0.1-3.apk`](https://github.com/thelordismysavior/Shepherd-releases/releases/tag/shepherd-1.0.1-3)

This App Release (`versionLabel` `1.0.1`, `releaseNumber` `3`) updates
Shepherd 1.0.0 (release 2) in place and includes the product fixes that landed
with it in Shepherd:

- **Cleaner planning updates** — the saved notice now appears as a small
  floating StatusChip that hides itself after a few seconds
- **Role edits fix the right person** — editing a role renames or reassigns
  the existing person instead of creating a duplicate
- **Clearer assigned-Person delete errors** — removing a person from a role
  shows the business error instead of a connection retry message
- **Hardened error handling** — assigned-Person delete and review follow-up
  fixes

Public release notes:
[Shepherd 1.0.1 (release 3)](https://github.com/thelordismysavior/Shepherd-releases/releases/tag/shepherd-1.0.1-3)

Technical release notes for this build are included below.

## Previous release: `shepherd-1.0.0-2`

Published artifact:
[`shepherd-1.0.0-2.apk`](https://github.com/thelordismysavior/Shepherd-releases/releases/tag/shepherd-1.0.0-2)

This App Release included the first updater-enabled production build
(`versionLabel` `1.0.0`, `releaseNumber` `2`) and the product work that landed
with it in Shepherd:

- **Auto updates** — optional in-app App Release detection, download,
  verification, recoverable installation, and verified-cache reuse
- **Realtime planning collaboration** — ordered Planning Changes for Events,
  Programs, Roles, People, templates, and recurring patterns; offline pending
  replay; conflict authority; editing presence; resilient live delivery
- **Deepened core modules** — stronger synchronization, Event planning, and
  Account-access seams behind the existing planner experience
- **Security hardening** — tighter SECURITY DEFINER grants, explicit deny-all
  RLS on internal tables, and Performance Advisor index/RLS init-plan fixes

Public release notes:
[Shepherd 1.0.0 (release 2)](https://github.com/thelordismysavior/Shepherd-releases/releases/tag/shepherd-1.0.0-2)

Technical release notes for this build are included below.
## Install bootstrap

1. Download the current APK from the
   [latest GitHub Release](https://github.com/thelordismysavior/Shepherd-releases/releases/latest).
2. Install it in place over existing Shepherd. Decline any Android prompt that
   asks to uninstall first; investigate package identity or signing credentials
   instead.
3. Enable **Allow from this source** for Shepherd if Android blocks the install.
4. Confirm Shepherd opens and local Planning Data remains present.
5. Later updater-enabled launches can offer newer App Releases from the public
   manifest.

## Integrity

- Package name remains `com.maxidiayni.shepherd`.
- APKs keep the existing EAS-managed signing identity so Android can update in
  place without removing data.
- MD5 in `latest.json` is a corruption check only; Android verifies package
  name, signing certificate, release number, and final installation.
- Recovery from a bad release always moves forward with a higher
  `releaseNumber`; do not republish or downgrade an existing asset.

---

## Technical notes: `shepherd-1.0.0-2`

Immutable universal APK for `com.maxidiayni.shepherd`.

| | |
| --- | --- |
| Version label | `1.0.0` |
| Release number (`versionCode`) | `2` |
| Artifact | [`shepherd-1.0.0-2.apk`](https://github.com/thelordismysavior/Shepherd-releases/releases/download/shepherd-1.0.0-2/shepherd-1.0.0-2.apk) |
| Size | `81552196` bytes |
| MD5 | `d3c64d70084c466d7943c739ac3da296` |

This is the first updater-enabled production App Release. Existing devices need
one manual bootstrap install of this APK (or another updater-enabled build);
later releases can be offered from inside Shepherd.

### Auto updates

- Checks the public `latest.json` manifest after Sign In / Home appears
- Offers optional **Update** / **Later** when `releaseNumber` is newer
- Downloads with progress, verifies size + MD5, then opens Android’s installer
- Recovers cancelled downloads, missing install-from-source permission, and
  installer cancellation without forcing a re-download of a verified APK
- Reuses verified cached APKs and cleans stale, corrupt, superseded, or
  already-installed files
- Development builds, Expo Go, iOS, and web remain unchanged

Related Shepherd work: PRs #36, #37, #39, #40 / issues #32–#35.

### Realtime planning collaboration

- Ordered, server-authoritative Planning Changes for Events, Programs, Roles,
  Person assignments, People, templates, and recurring patterns
- Field-level merges for compatible concurrent edits; explicit unresolved
  conflicts with Planning Authority
- Durable offline Pending Changes with ordered replay and idempotent retries
- Editing presence (advisory, non-locking) and grouped live-delivery activity
- Resilient delivery with catch-up, polling fallback, session guards, and
  degradation visibility

Related Shepherd work: PRs #7–#11, #13–#18, #20, #21, #23, #24.

### Deepened core modules

- Stronger shared synchronization seam for persistence, audit queues, conflict
  recovery, realtime adoption, and settings migration
- Event planning transitions concentrated behind a deeper Event / Program /
  Role / assignment module
- Account access workflows for canonical Account projections, Person access
  grant/revoke, Role Change Requests, and Account state transitions

Related Shepherd work: PR #6 and follow-on deepen-core-module commits.

### Security hardening and fixes

- Revoked inherited `PUBLIC` / broad execute access on SECURITY DEFINER
  functions; granted only required callers
- Fixed `enforce_role_change_authorization` search path and catalog
  qualification
- Explicit deny-all RLS on internal tables that are RPC/trigger-only (no direct
  Data API access)
- Performance Advisor remediation: foreign-key indexes, RLS init-plan rewrites,
  consolidated `profiles` UPDATE policies
- Signup metadata can no longer grant elevated roles; migration replay/repair
  paths fail closed where required

Related Shepherd work: PRs #19, #29, #30, #38 / issues #25, #26, #28.
