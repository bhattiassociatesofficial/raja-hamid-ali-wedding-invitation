# ROLLBACK

How to restore any known state. Written for a future Claude session: run the blocks as they are, and check every SHA-256 printed.

- **Restoring files never changes the live site.** Deploying, committing and pushing are separate steps that need the user's explicit approval.
- Never `git add -A` / `git commit -a` in this folder (see `PROJECT_STATUS.md`, Deploy workflow).
- Never delete or overwrite anything in `backups/`, `dist/baseline/` or `/Users/noumansafdar/dev/_project-merge-backups/`. Several of those folders are read-only on purpose.

```bash
P=/Users/noumansafdar/dev/raja-hamid-ali-wedding-invitation        # canonical project
M=/Users/noumansafdar/dev/_project-merge-backups/2026-10-08_100531  # pre-merge backups
```

> **WARNING (privacy, since Phase 6).** Every build, and every backup folder except `backups/2026-10-09-final-db45b198/` (and the tiny `2026-10-08-pre-barat-overlap-fix/`), contains the two hidden contact numbers: in `wedding-data.js` and therefore in the page source of every built page. `dist/baseline/`, the old folder (`_OLD_wedding-card_AFTER_MERGE`) and the merge backups contain them too. Restoring such a file into the working tree and rebuilding, or publishing such a page, **republishes the numbers**. The restore recipes below therefore end with a clean-up line; never skip it, and never publish an older build's HTML. Only `db45b198` is clean.

## 0. Always first: snapshot the current state

```bash
S="$P/backups/$(date +%Y-%m-%d-%H%M)-pre-rollback"
mkdir -p "$S/js" "$S/css" "$S/dist"
cp -p "$P"/js/*.js "$S/js/"; cp -p "$P/css/invitation.css" "$S/css/"
cp -p "$P/build.js" "$P/index.template.html" "$P/preview.jpg" "$S/"; cp -p "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html" "$S/dist/"   # NOT the root index.html: that is the deployed page (in git)
(cd "$S" && find . -type f ! -name SHA256SUMS -print0 | xargs -0 shasum -a 256 > SHA256SUMS && cat SHA256SUMS)
```

## 1a. The CURRENT build (db45b198, Phase 6, 2026-10-09)

Expected: `db45b1985149b36e1ac01355ec9bb8f50a7b0b55af582d2236ea53d72f4f2cd5`, 642,727 bytes; `preview.jpg` `167e95115e30b3188390471b6117443e42f83a630dbaab672d19a9d592c8bea6`.

Copies of it: `$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html`; `$P/index.html` (the root page, byte-identical; it is live once the Phase 6 Pages build is verified); git `HEAD:index.html` of the Phase 6 deployment commit; and the complete, read-only source + build backup `$P/backups/2026-10-09-final-db45b198/` (with `SHA256SUMS`).

```bash
B="$P/backups/2026-10-09-final-db45b198"
(cd "$B" && shasum -a 256 -c SHA256SUMS)    # every line must say OK
# rebuild from the backup's own sources in a scratch folder (leaves $P untouched):
T=$(mktemp -d); ditto "$B" "$T"; chmod -R u+w "$T"; (cd "$T" && node build.js --url https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/ && shasum -a 256 dist/Raja-Hamid-Ali-Wedding-Invitation.html)   # must print db45b198…
```

The build template is `index.template.html` (not `index.html`) since Phase 5; `build.js` reads it.

**Previous deployed build (Phase 5, `63ad01d2…c66b8`, 642,779 bytes):** backup `$P/backups/2026-10-09-final-63ad01d2/` (same recipe, expected `63ad01d2…`). It differs from the current build in 5 lines (4 hunks): the tab title, the two comment lines, the `contact` line and the `document.title` line. **Do not redeploy it** unless you really need that exact build, because it republishes the numbers in the page source.

## 1. Restore the previous local build (97767af4, Phase 3)

Expected: `97767af49294f3ffd50ff56bd5a0afe3f676d14738a7cdf4f520fa980331194b`, 641,898 bytes.

Copies of it:
1. `$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html` (current)
2. `$M/wedding-card-pre-merge/dist/Raja-Hamid-Ali-Wedding-Invitation.html`
3. `/Users/noumansafdar/dev/_OLD_wedding-card_AFTER_MERGE/dist/Raja-Hamid-Ali-Wedding-Invitation.html` (until that folder is deleted)

The full matching source is in `$M/wedding-card-pre-merge/`.

```bash
B="$M/wedding-card-pre-merge"
mkdir -p "$P/dist"
for f in js/app.js js/render.js js/ornaments.js js/wedding-data.js css/invitation.css build.js dist/Raja-Hamid-Ali-Wedding-Invitation.html; do cp "$B/$f" "$P/$f"; done
cp "$B/index.html" "$P/index.template.html"                                    # the pre-Phase-5 backups call the template index.html
sed -i '' 's#read("index.html")#read("index.template.html")#' "$P/build.js"    # and their build.js reads that old name
# NEVER copy a backup's index.html onto "$P/index.html": since Phase 5 that is the deployed page.
shasum -a 256 "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must print 97767af4…331194b (this dist file still holds the numbers: never publish it)
sed -i '' -E 's#contact: \[ /\*.*\*/ \],#contact: [],#' "$P/js/wedding-data.js"    # REQUIRED after restoring an old wedding-data.js: removes the contact-number comment (Phase 6)
```

To prove that the source rebuilds it, build in a scratch copy. This leaves `dist/` untouched. **Run this block BEFORE the last line (the clean-up `sed`) of the restore block above**, otherwise the hash will differ by that one line:

```bash
T=$(mktemp -d); for x in build.js index.template.html js css fonts assets; do ditto "$P/$x" "$T/$x"; done
node "$T/build.js" && shasum -a 256 "$T/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must print 97767af4… (plain build, no --url)
# Run this BEFORE the clean-up `sed` line above. After the clean-up the comment is gone and the hash is different: that is expected, and is no longer a reproduction of 97767af4.
```

## 2. Restore fa3992fa (before the final polish)

Expected: `fa3992faf309720c023f8acbcffc2b158b2ce171fb7cb7a1a703ea2e01c2c68b`, 634,396 bytes. The backup holds the code (js, css, build.js, template, dist) plus `SHA256SUMS`; `fonts/` and `assets/` come from `$P` (unchanged since 2026-10-06).

```bash
B="$P/backups/2026-10-07-pre-final-polish"
mkdir -p "$P/dist"
(cd "$B" && shasum -a 256 -c SHA256SUMS)   # the backup itself: all lines must say OK
for f in js/app.js js/render.js js/ornaments.js js/wedding-data.js css/invitation.css build.js dist/Raja-Hamid-Ali-Wedding-Invitation.html; do cp "$B/$f" "$P/$f"; chmod u+w "$P/$f"; done
cp "$B/index.html" "$P/index.template.html"; chmod u+w "$P/index.template.html"     # the old backups call the template index.html
sed -i '' 's#read("index.html")#read("index.template.html")#' "$P/build.js"         # and their build.js reads that old name
# NEVER copy a backup's index.html onto "$P/index.html": since Phase 5 that is the deployed page.
shasum -a 256 "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must print fa3992fa… (this dist file still holds the numbers: never publish it). A rebuild AFTER the clean-up line below differs from it by that one line (38 bytes smaller): expected.
sed -i '' -E 's#contact: \[ /\*.*\*/ \],#contact: [],#' "$P/js/wedding-data.js"    # REQUIRED after restoring an old wedding-data.js: removes the contact-number comment (Phase 6)
```

Copy only from inside the backup; never edit it (the folder is read-only). `chmod u+w` makes the restored working copies editable again.

## 3. Restore an earlier known-good backup

Each folder holds `dist/Raja-Hamid-Ali-Wedding-Invitation.html` and the sources that existed then. `fonts/` and `assets/` have not changed since 2026-10-06. The template (`index.html` in these backups) is today's `index.template.html` except for its `<title>` line (Phase 6; `build.js` overwrites that element anyway). `build.js` changed in Phase 5 (it reads `index.template.html` and writes the share metadata) and again in Phase 6 (`const title = data.groomName`).

| Backup folder (`$P/backups/…`) | dist SHA-256 | State it holds |
|---|---|---|
| `2026-10-07-pre-final-polish/` | `fa3992faf309720c023f8acbcffc2b158b2ce171fb7cb7a1a703ea2e01c2c68b` | End of Phase 1 (engine audit). Complete, with `SHA256SUMS` |
| `2026-10-07-pre-svh-fix/` | `f8d163bbeabd00034dd6abe2f6f0e4a225b4d110c949df52720c4c7bc15956c2` | After the wheel fix, before `lookAgain` |
| `2026-10-07-pre-wheel-fix/` | `4d6662171bc8a3da97d4054ef677e5ea38fe8e05e32d73bba2eb3c928d1fd6ae` | After the stale-line fix |
| `2026-10-07-pre-stale-line-fix/` | `e0b8a2ee6204940a71e5a481ea1df029164dad8f1e25352aa45657a2d4b29c9a` | End of the compatibility audit |
| `2026-10-07-pre-lid-fix/` | `c44b71811daf73a9fc3c6df5806d118b4861665a54e13b9caab060883eeb21e8` | Compat audit, first two fixes |
| `2026-10-07-pre-compat-fixes/` | `95e275aa48726861b31f87e36f0d4a66b36b843bda5a06419c60f5edb5c0880c` | Forensic polish (has the closed-lid regression) |
| `2026-10-07-pre-forensic-polish/` | `7adb692ec20aae3cbe56379b547882231924053aa0839f0b0e30c19003c4faf5` | Luxury pass reverted + final guest list |
| `2026-10-07-luxury-transition-pass/` | `9eabff78025fd8d955e61d9480a79558edf2ab4c8196997c8384019aa28a5a12` | REJECTED luxury pass (it was the live page until the Phase 5 deploy). Has only `app.js` + CSS. **Restore its dist only**; mixing its `app.js` with today's other files gives an untested state |
| `2026-10-09-final-db45b198/` | `db45b1985149b36e1ac01355ec9bb8f50a7b0b55af582d2236ea53d72f4f2cd5` | The Phase 6 build: complete sources (number-free), `preview.jpg`, `SHA256SUMS` |
| `2026-10-09-pre-privacy-cleanup/` | (no build) | Old `wedding-data.js`, `app.js`, `build.js`, template, the deployed page `63ad01d2` and the docs as they were before Phase 6. They still contain the contact numbers: keep local, never publish |
| `2026-10-09-final-63ad01d2/` | `63ad01d2f49bbfee909745606c63d3d2242e10a36221e79a895ee18c3dbc66b8` | The Phase 5 build (live until the Phase 6 deploy; its page source contains the numbers): complete sources (incl. `index.template.html`, `build.js`, fonts, assets), `preview.jpg`, `SHA256SUMS` |
| `2026-10-09-pre-share-preview/` | (no build) | `build.js.before` (old share metadata, template read from `index.html`) and `preview.jpg.before` (`ce30ec50…`, with the bride's name and dates) |
| `2026-10-08-pre-barat-pocket-depth-fix/` | `5f017edb8fb4b276fc9bcf9979e690bf389a9ad5272e3ebbd257f74d22d5dee4` | Candidate before the pocket-depth fix (97767af4 + z-clearance). Complete, with `SHA256SUMS` |
| `2026-10-08-pre-barat-overlap-fix/` | (no build) | `app.js.before` = 97767af4's `app.js` before the z-clearance (`6b2b5ebd…` is the 5f017edb one, see the folder above) |
| `2026-10-08-pre-consolidation/` | (no build) | Only `docs/CLAUDE_HANDOFF.md` as it was before the merge (`5dfdc7a8…`) |
| `$P/dist/baseline/…verified-2026-10-07.html` | `de031a2db17ef242996fc5be9aef6f5500c90e89fd7156e8d82f210c0ac8ac5f` | Verified pre-polish baseline (dist only, read-only) |

```bash
B="$P/backups/<folder-from-the-table>"
cp "$B/dist/Raja-Hamid-Ali-Wedding-Invitation.html" "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"; chmod u+w "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"
for f in js/app.js js/render.js js/ornaments.js js/wedding-data.js css/invitation.css; do [ -f "$B/$f" ] && cp "$B/$f" "$P/$f" && chmod u+w "$P/$f"; done   # skip this line for the luxury backup
sed -i '' -E 's#contact: \[ /\*.*\*/ \],#contact: [],#' "$P/js/wedding-data.js"    # REQUIRED after restoring an old wedding-data.js: removes the contact-number comment (Phase 6)
# the restored dist HTML still holds the numbers: use it for comparison only, never publish it
# (skip this whole block for the rows marked "(no build)": they have no dist file)
shasum -a 256 "$P/dist/Raja-Hamid-Ali-Wedding-Invitation.html"   # must match the table
```

The old rejected page is also kept in git history. Like every older page it is for looking at only: never publish it.

## 4. Restore the pre-merge project folders

> These are complete copies from before Phase 6: they contain the contact numbers everywhere (sources, builds, notes). Restoring them undoes the Phase 6 clean-up; repeat it afterwards (the `sed` line in sections 1–3 for `js/wedding-data.js`, and never publish any HTML from them). A restored root `index.html` there is the old page: `git add index.html`, `git commit -a` or a push would publish the numbers again.

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
curl -s "https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/?v=$(date +%s)" | shasum -a 256   # db45b198… once the Phase 6 Pages build is live (63ad01d2… until then; the plain URL can lag up to 10 minutes)
curl -s "https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/preview.jpg?v=$(date +%s)" | shasum -a 256   # 167e9511…
gh api repos/bhattiassociatesofficial/raja-hamid-ali-wedding-invitation/pages/builds/latest --jq '{status,commit}'
```

**Roll the live site back without rewriting history** (never force-push): revert the deployment commit, then push normally.

```bash
git -C "$P" log --oneline -3                      # find the deployment commit
git -C "$P" revert --no-edit <deployment-commit>   # restores index.html to the PREVIOUS page. WARNING: for the Phase 6 commit that is the 63ad01d2 page, which still has the old comment, so a revert republishes the contact numbers. Prefer fixing forward (a new commit with a corrected build).
git -C "$P" push origin main
```

To put an older build live instead: **do not**, unless you accept republishing the two hidden contact numbers: every page older than `db45b198` has them in its source. If an older look is truly needed, restore its sources, apply the clean-up line from sections 1–3, rebuild with `--url`, verify the page and the share tags, **grep the rebuilt HTML for the contact numbers (expect 0 hits)**, then copy the rebuilt HTML over `$P/index.html`, commit only `index.html` (and `preview.jpg` if it changed) and push normally.
