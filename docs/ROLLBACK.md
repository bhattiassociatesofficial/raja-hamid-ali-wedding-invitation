# ROLLBACK

How to restore any known state. Written for a future Claude session: run the blocks as they are, and check every SHA-256 printed.

- **Restoring files never changes the live site.** Deploying, committing and pushing are separate steps that need the user's explicit approval.
- Never `git add -A` / `git commit -a` in this folder (see `PROJECT_STATUS.md`, Pending).
- Never delete or overwrite anything in `backups/`, `dist/baseline/` or `/Users/noumansafdar/dev/_project-merge-backups/`. Several of those folders are read-only on purpose.

```bash
P=/Users/noumansafdar/dev/raja-hamid-ali-wedding-invitation        # canonical project
M=/Users/noumansafdar/dev/_project-merge-backups/2026-10-08_100531  # pre-merge backups
```

## 0. Always first: snapshot the current state

```bash
S="$P/backups/$(date +%Y-%m-%d-%H%M)-pre-rollback"
mkdir -p "$S/js" "$S/css" "$S/dist"
cp -p "$P"/js/*.js "$S/js/"; cp -p "$P/css/invitation.css" "$S/css/"
cp -p "$P/build.js" "$P/index.template.html" "$P/preview.jpg" "$S/"; cp -p "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html" "$S/dist/"   # NOT the root index.html: that is the deployed page (in git)
(cd "$S" && find . -type f ! -name SHA256SUMS -print0 | xargs -0 shasum -a 256 > SHA256SUMS && cat SHA256SUMS)
```

## 1a. The CURRENT build (63ad01d2, deployed 2026-10-09)

Expected: `63ad01d2f49bbfee909745606c63d3d2242e10a36221e79a895ee18c3dbc66b8`, 642,779 bytes; `preview.jpg` `167e95115e30b3188390471b6117443e42f83a630dbaab672d19a9d592c8bea6`.

Copies of it: `$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html`; `$P/index.html` (the deployed copy, byte-identical); git `HEAD:index.html` of the deployment commit; and the complete, read-only source + build backup `$P/backups/2026-10-09-final-63ad01d2/` (with `SHA256SUMS`).

```bash
B="$P/backups/2026-10-09-final-63ad01d2"
(cd "$B" && shasum -a 256 -c SHA256SUMS)    # every line must say OK
# rebuild from the backup's own sources in a scratch folder (leaves $P untouched):
T=$(mktemp -d); ditto "$B" "$T"; chmod -R u+w "$T"; (cd "$T" && node build.js --url https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/ && shasum -a 256 dist/Raja-Hamid-Ali-Wedding-Invitation.html)   # must print 63ad01d2…
```

The build template is `index.template.html` (not `index.html`) since Phase 5; `build.js` reads it.

## 1. Restore the previous local build (97767af4, Phase 3)

Expected: `97767af49294f3ffd50ff56bd5a0afe3f676d14738a7cdf4f520fa980331194b`, 641,898 bytes.

Copies of it:
1. `$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html` (current)
2. `$M/wedding-card-pre-merge/dist/Raja-Hamid-Ali-Wedding-Invitation.html`
3. `/Users/noumansafdar/dev/_OLD_wedding-card_AFTER_MERGE/dist/Raja-Hamid-Ali-Wedding-Invitation.html` (until that folder is deleted)

The full matching source is in `$M/wedding-card-pre-merge/`.

```bash
B="$M/wedding-card-pre-merge"
for f in js/app.js js/render.js js/ornaments.js js/wedding-data.js css/invitation.css build.js dist/Raja-Hamid-Ali-Wedding-Invitation.html; do cp "$B/$f" "$P/$f"; done
cp "$B/index.html" "$P/index.template.html"                                    # the pre-Phase-5 backups call the template index.html
sed -i '' 's#read("index.html")#read("index.template.html")#' "$P/build.js"    # and their build.js reads that old name
# NEVER copy a backup's index.html onto "$P/index.html": since Phase 5 that is the deployed page.
shasum -a 256 "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must print 97767af4…331194b
```

To prove that the source rebuilds it, build in a scratch copy. This leaves `dist/` untouched:

```bash
T=$(mktemp -d); for x in build.js index.template.html js css fonts assets; do ditto "$P/$x" "$T/$x"; done
node "$T/build.js" && shasum -a 256 "$T/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must print 97767af4… (plain build, no --url)
```

## 2. Restore fa3992fa (before the final polish)

Expected: `fa3992faf309720c023f8acbcffc2b158b2ce171fb7cb7a1a703ea2e01c2c68b`, 634,396 bytes. The backup holds the complete source plus `SHA256SUMS`.

```bash
B="$P/backups/2026-10-07-pre-final-polish"
(cd "$B" && shasum -a 256 -c SHA256SUMS)   # the backup itself: all lines must say OK
for f in js/app.js js/render.js js/ornaments.js js/wedding-data.js css/invitation.css build.js dist/Raja-Hamid-Ali-Wedding-Invitation.html; do cp "$B/$f" "$P/$f"; chmod u+w "$P/$f"; done
cp "$B/index.html" "$P/index.template.html"; chmod u+w "$P/index.template.html"     # the old backups call the template index.html
sed -i '' 's#read("index.html")#read("index.template.html")#' "$P/build.js"         # and their build.js reads that old name
# NEVER copy a backup's index.html onto "$P/index.html": since Phase 5 that is the deployed page.
shasum -a 256 "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must print fa3992fa…
```

Copy only from inside the backup; never edit it (the folder is read-only). `chmod u+w` makes the restored working copies editable again.

## 3. Restore an earlier known-good backup

Each folder holds `dist/Raja-Hamid-Ali-Wedding-Invitation.html` and the sources that existed then. `fonts/` and `assets/` have not changed since 2026-10-06. The template (`index.html` in these backups) is unchanged too: it is today's `index.template.html`. `build.js` changed in Phase 5 (it reads `index.template.html` and writes the share metadata).

| Backup folder (`$P/backups/…`) | dist SHA-256 | State it holds |
|---|---|---|
| `2026-10-07-pre-final-polish/` | `fa3992faf309720c023f8acbcffc2b158b2ce171fb7cb7a1a703ea2e01c2c68b` | End of Phase 1 (engine audit). Complete, with `SHA256SUMS` |
| `2026-10-07-pre-svh-fix/` | `f8d163bbeabd00034dd6abe2f6f0e4a225b4d110c949df52720c4c7bc15956c2` | After the wheel fix, before `lookAgain` |
| `2026-10-07-pre-wheel-fix/` | `4d6662171bc8a3da97d4054ef677e5ea38fe8e05e32d73bba2eb3c928d1fd6ae` | After the stale-line fix |
| `2026-10-07-pre-stale-line-fix/` | `e0b8a2ee6204940a71e5a481ea1df029164dad8f1e25352aa45657a2d4b29c9a` | End of the compatibility audit |
| `2026-10-07-pre-lid-fix/` | `c44b71811daf73a9fc3c6df5806d118b4861665a54e13b9caab060883eeb21e8` | Compat audit, first two fixes |
| `2026-10-07-pre-compat-fixes/` | `95e275aa48726861b31f87e36f0d4a66b36b843bda5a06419c60f5edb5c0880c` | Forensic polish (has the closed-lid regression) |
| `2026-10-07-pre-forensic-polish/` | `7adb692ec20aae3cbe56379b547882231924053aa0839f0b0e30c19003c4faf5` | Luxury pass reverted + final guest list |
| `2026-10-07-luxury-transition-pass/` | `9eabff78025fd8d955e61d9480a79558edf2ab4c8196997c8384019aa28a5a12` | REJECTED luxury pass (= live). Has only `app.js` + CSS. **Restore its dist only**; mixing its `app.js` with today's other files gives an untested state |
| `2026-10-09-final-63ad01d2/` | `63ad01d2f49bbfee909745606c63d3d2242e10a36221e79a895ee18c3dbc66b8` | The deployed build: complete sources (incl. `index.template.html`, `build.js`, fonts, assets), `preview.jpg`, `SHA256SUMS` |
| `2026-10-09-pre-share-preview/` | (no build) | `build.js.before` (old share metadata, template read from `index.html`) and `preview.jpg.before` (`ce30ec50…`, with the bride's name and dates) |
| `2026-10-08-pre-barat-pocket-depth-fix/` | `5f017edb8fb4b276fc9bcf9979e690bf389a9ad5272e3ebbd257f74d22d5dee4` | Candidate before the pocket-depth fix (97767af4 + z-clearance). Complete, with `SHA256SUMS` |
| `2026-10-08-pre-barat-overlap-fix/` | (no build) | `app.js.before` = 97767af4's `app.js` before the z-clearance (`6b2b5ebd…` is the 5f017edb one, see the folder above) |
| `2026-10-08-pre-consolidation/` | (no build) | Only `docs/CLAUDE_HANDOFF.md` as it was before the merge (`5dfdc7a8…`) |
| `$P/dist/baseline/…verified-2026-10-07.html` | `de031a2db17ef242996fc5be9aef6f5500c90e89fd7156e8d82f210c0ac8ac5f` | Verified pre-polish baseline (dist only, read-only) |

```bash
B="$P/backups/<folder-from-the-table>"
cp "$B/dist/Raja-Hamid-Ali-Wedding-Invitation.html" "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"; chmod u+w "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"
for f in js/app.js js/render.js js/ornaments.js js/wedding-data.js css/invitation.css; do [ -f "$B/$f" ] && cp "$B/$f" "$P/$f" && chmod u+w "$P/$f"; done   # skip this line for the luxury backup
shasum -a 256 "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must match the table
```

The exact live page is also in git: `git -C "$P" show e502203:index.html` (9eabff78).

## 4. Restore the pre-merge project folders

The backups at `$M` are complete copies of both folders, taken 2026-10-08 10:05 before anything was changed. The deployment folder's copy includes its full `.git`. Manifests: `$M/wedding-card-pre-merge.SHA256SUMS` and `$M/raja-hamid-ali-wedding-invitation-pre-merge.SHA256SUMS`.

**Old development folder.** If `_OLD_wedding-card_AFTER_MERGE` still exists, renaming it back is enough:

```bash
mv "/Users/noumansafdar/dev/_OLD_wedding-card_AFTER_MERGE" "/Users/noumansafdar/dev/wedding card"
```

Otherwise copy it back from the backup:

```bash
ditto "$M/wedding-card-pre-merge" "/Users/noumansafdar/dev/wedding card"
```

**Deployment folder, exactly as before the merge.** Move the merged folder aside (never delete it), then copy the backup back:

```bash
mv "$P" "/Users/noumansafdar/dev/raja-hamid-ali-wedding-invitation.merged-$(date +%Y%m%d-%H%M%S)"
ditto "$M/raja-hamid-ali-wedding-invitation-pre-merge" "$P"
```

**Verify both.** Each command prints IDENTICAL when the folder matches its manifest. A Finder-created `.DS_Store` would show as one extra line; that is harmless.

```bash
(cd "$P" && find . -type f -print0 | sort -z | xargs -0 shasum -a 256) | diff - "$M/raja-hamid-ali-wedding-invitation-pre-merge.SHA256SUMS" && echo IDENTICAL
(cd "/Users/noumansafdar/dev/wedding card" && find . -type f -print0 | sort -z | xargs -0 shasum -a 256) | diff - "$M/wedding-card-pre-merge.SHA256SUMS" && echo IDENTICAL
git -C "$P" status --short   # expect: " M index.html" and "?? preview.jpg" (the pre-merge state)
```

## 5. Roll back, or check, the LIVE site

Pages serves `main` `/` (legacy build). The live page is the root `index.html` of `main`; the share image is the root `preview.jpg`.

**Check what is live** (cache-busting query; the plain URL can be cached up to 10 minutes):

```bash
curl -s "https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/?v=$(date +%s)" | shasum -a 256   # current build: 63ad01d2…
curl -s "https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/preview.jpg?v=$(date +%s)" | shasum -a 256   # 167e9511…
gh api repos/bhattiassociatesofficial/raja-hamid-ali-wedding-invitation/pages/builds/latest --jq '{status,commit}'
```

**Roll the live site back without rewriting history** (never force-push): revert the deployment commit, then push normally.

```bash
git -C "$P" log --oneline -3                      # find the deployment commit
git -C "$P" revert --no-edit <deployment-commit>   # restores index.html to the previous page (and removes preview.jpg and the docs that commit added)
git -C "$P" push origin main
```

To put an older build live instead, copy its verified HTML over `$P/index.html` (check the SHA first), commit only `index.html` (and `preview.jpg` if it matters), and push. The page from before Phase 5 (9eabff78, rejected) is `git show e502203:index.html`.
