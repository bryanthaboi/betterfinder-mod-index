# betterfinder mod index

[![validate](https://github.com/bryanthaboi/betterfinder-mod-index/actions/workflows/validate.yml/badge.svg)](https://github.com/bryanthaboi/betterfinder-mod-index/actions/workflows/validate.yml)
[![publish](https://github.com/bryanthaboi/betterfinder-mod-index/actions/workflows/publish.yml/badge.svg)](https://github.com/bryanthaboi/betterfinder-mod-index/actions/workflows/publish.yml)

metadata only. ur code and assets stay in ur release repo. submit one folder,
get it reviewed, let betterfinder find it. [how to submit](CONTRIBUTING.md).

| mod | status |
| --- | --- |
| alice.image-dimensions | example, placeholder release |
| alice.markdown-previewer | example, placeholder release |
| alice.pinned-folders | example, placeholder release |
| alice.recent-downloads | example, placeholder release |
| alice.zip-selection | example, placeholder release |

these five entries mirror modkit examples. urls, hashes and author signatures
are placeholders. don't install them. replacing one with a real release must
pass artifact validation; ci never exempts examples from changed-release checks.

`mods/<author>.<name>/listing.json` is required. `icon.png` and `screenshots/`
are optional review material, not downloaded by the client. no executable code
belongs here. the schema is copied unchanged from modkit.

`index.json` is sorted by id. each entry includes a sha256 of modkit canonical
listing json (not whitespace in the source file). the feed carries apiVersions,
generatedAt, expiresAt and a monotonic serial. `index.json.sig` is a raw 64-byte
ed25519 signature of the exact feed bytes. no base64 wrapper. expiry is seven days.

published feeds are release assets: `releases/latest/download/index.json` and
`index.json.sig`. configure the client with that url after bootstrap; the raw
main url is only the locally signed bootstrap snapshot and isn't refreshed by
release publication. private github assets may require an authenticated api
asset url with `Accept: application/octet-stream`; don't ship an owner token.

## owner setup

1. commit the betterfinder packages first. set repository variable
   `BETTERFINDER_VALIDATOR_REF` to that full 40-character reviewed commit sha.
2. add `BETTERFINDER_TOKEN`: fine-grained read-only contents access to the private
   `bryanthaboi/betterfinder` repo. no write scope. checkout doesn't persist it.
3. on the owner's mac/self-hosted signing runner, run `scripts/generate-key`.
   it creates `~/.config/betterfinder-index/signing.key` with mode 0600 and prints
   only the public key. existing keys are never overwritten. back it up privately.
4. replace the scratch public key in `TrustedIndexKey.pinned` with that public key
   and appropriate validity dates. rebuild the app and update the validator sha.
   the checked-in pin is a test key; its private half exists only in scratch.
5. protect main and the `index-signing` environment. allow only main into that
   environment, require owner approval, and register the owner's runner with
   `self-hosted, macOS, ARM64, betterfinder-index-signing`. install swift 6 and gh.
   never run pr jobs on this runner. restrict who can change actions/variables.
6. bootstrap with `INDEX_GENERATED_AT=$(date -u +%Y-%m-%dT%H:%M:%SZ)
   INDEX_SERIAL=$(date +%s) scripts/build-index` on one shell line. the source repo
   defaults to the adjacent `betterfinder`; override `BETTERFINDER_SOURCE` if needed.
   use `INDEX_SIGNING_KEY` only for testing or a different private key location.
7. the lead adds origin `git@github.com:bryanthaboi/betterfinder-mod-index.git`,
   commits these files, and makes the first push to main. no commit/push was made
   during implementation. enable actions and release write permission. publish
   builds on a disposable mac, then signs only feed bytes in a separate job.
   daily publication keeps feeds fresh. check the first release and its signature.

for rotation, ship the new public key in the app before changing the signing key.
keep both pins during an overlap, each with explicit validity windows; retire the
old pin in a later app release. feed content cannot add a trusted key. revocations
require an app pin update. signatures prove provenance, not safety.

pr validation uses pull_request_target so workflows/scripts come from reviewed
base code; submitted scripts are never run. the validator source is commit-pinned.
artifacts are bounded, sha256 checked and passed to the validator without tokens
and with network denied. output is commented on the pr. the initial empty repo
must receive the trusted workflow before accepting submissions.
