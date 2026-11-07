# contributing

copy an example into `mods/<author>.<name>/listing.json`. keep the id identical to
ur manifest. replace every placeholder with ur immutable release tag, source
commit, artifact bytes, sha256, author key id and detached signature. use the
modkit schema in `schema/`. don't include mod code or private keys.

host ur `.bfmod` in ur own github release. list exact manifest permissions with
reasons, license and source provenance in the pr. author signatures cover modkit's
canonical `{id,version,sha256,api,app}` payload. first author keys need maintainer
review; a key change needs old-key endorsement or another review. index signing
doesn't replace author-key binding or package validation.

run `betterfinder-mod validate path/to/release.bfmod --listing path/to/listing.json`
with the reviewed betterfinder cli. ci downloads changed listings' artifacts with
size/hash checks and runs the same validator. placeholder example urls deliberately
fail if submitted as changed releases. deleted entries need no artifact download.

optional `icon.png` and `screenshots/` are review-only. avoid unrelated edits.
maintainers build the deterministic feed using the same generatedAt/serial/key
for reproducible output. never submit generated index or signature edits.
