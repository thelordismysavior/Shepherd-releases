# Shepherd release artifacts

This repository publishes **immutable signed Shepherd Android App Release APKs**.
It is not the application source repository.

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

## Latest App Release: `shepherd-1.0.0-2`

Published artifact:
[`shepherd-1.0.0-2.apk`](https://github.com/thelordismysavior/Shepherd-releases/releases/tag/shepherd-1.0.0-2)

This App Release includes the first updater-enabled production build
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
- **Database migrations** — accompanying Supabase schema/security/collaboration
  migrations must be applied from the main Shepherd repository before relying
  on the collaboration and hardened server paths (see below)

Full notes:
[Release shepherd-1.0.0-2](https://github.com/thelordismysavior/Shepherd-releases/releases/tag/shepherd-1.0.0-2)

## Operator note: database migrations

The APK alone does not migrate Supabase. Operators should apply the Shepherd
repository’s versioned migrations (or equivalent schema) before expecting this
App Release’s collaboration and hardened server behavior in production. Areas
covered by the accompanying migration set include:

- Normalized planning data and collaborative Planning Change delivery
- Offline-safe planning conflict/authority and live-delivery support
- Signup-role enforcement and SECURITY DEFINER grant hardening
- Internal-table RLS deny-all boundaries
- Performance Advisor foreign-key indexes and RLS init-plan rewrites

Use the main repository’s migration workflow (`supabase migration list`,
`supabase db push`, and the documented replay-safe baseline) after backing up
the hosted database. Publishing an APK to this repository never runs those
migrations.

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
