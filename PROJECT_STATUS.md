# PROJECT STATUS (MASTER)

| | |
|---|---|
| **PROJECT** | Raja Hamid Ali Wedding Invitation (Raja Hamid Ali & Laiba Abbasi; Barat 17 Oct 2026, Walima 18 Oct 2026) |
| **CANONICAL PROJECT PATH** | `/Users/noumansafdar/dev/raja-hamid-ali-wedding-invitation` (the ONLY project folder; develop here only) |
| **CURRENT BUILD** | `dist/Raja-Hamid-Ali-Wedding-Invitation.html`, built with `node build.js --url https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/`; deployed byte-identical as the root `index.html`, plus `preview.jpg` |
| **CURRENT SHA-256** | `63ad01d2f49bbfee909745606c63d3d2242e10a36221e79a895ee18c3dbc66b8` |
| **CURRENT SIZE** | 642,779 bytes (`preview.jpg`: 213,574 bytes, `167e9511…592c8bea6`) |
| **CURRENT STATUS** | **DEPLOYED** to GitHub Pages from `main` by the commit that adds this text (`git log -1 -- preview.jpg`). A commit cannot contain its own SHA: the commit SHA and the live verification are recorded in the local copy of this file after the push (§ "Deployment record") |
| **GUEST SHARE URL** | https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/ (HTTPS, 200, no redirect when shared with the trailing slash) |
| **GITHUB** | `bhattiassociatesofficial/raja-hamid-ali-wedding-invitation`, branch `main`, Pages from `main` `/` (legacy build). Root `index.html` = the deployed build. Before this deploy: HEAD = origin/main = `e502203` (the rejected `9eabff78`) |
| **Last updated** | 2026-10-09 (Phase 5: Barat pocket-depth fix, share preview, deployment) |

> **Live = local since Phase 5.** The live page was `9eabff78…` (the rejected luxury build, commit `e502203`) until this deploy. If you ever doubt it, fetch the live page and hash it (see `docs/ROLLBACK.md` §5).

## Deploy workflow (decided 2026-10-09, replaces the old "copy dist over index.html")

GitHub Pages serves the root `index.html` of `main`. In this folder the build **template** used to be the root `index.html`, so the old method would have destroyed it. Now:

- The template is `index.template.html` (identical to the old root `index.html`, SHA `b0f401ac…0296c2f`). `build.js` reads it. It is a local source file and is **not** in git (see "What is in git" below).
- `node build.js --url https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/` writes `dist/Raja-Hamid-Ali-Wedding-Invitation.html` and copies `preview.jpg` to `dist/`.
- **Deploy = verify, then copy the build over the root page and commit exactly these files:** `index.html` (= the build, byte for byte) and `preview.jpg`. Check `shasum -a 256 index.html` equals the build SHA before committing, and fetch the live page afterwards.
- Never `git add -A` / `git commit -a`: `backups/`, `docs/` evidence and `dist/` are not for the public repo.

### What is in git, and why

Committed (public repo): `index.html`, `preview.jpg`, `PROJECT_STATUS.md`, `docs/CHANGELOG.md`, `docs/ROLLBACK.md`.
**Not pushed, on purpose:** the source tree (`js/`, `css/`, `build.js`, `index.template.html`, `fonts/`, `assets/`) and `docs/CLAUDE_HANDOFF.md`. Reason: `js/wedding-data.js` (line 90) and `docs/CLAUDE_HANDOFF.md` (§ contact) contain the two phone numbers the user chose to hide, as plain text. The deployed HTML already carries them inside a JS comment (known finding 8 below), but publishing them as readable repository files in a public repo would spread them further. Decision for the user: either approve removing the comment (changes the SHA) and then push the sources, or approve pushing them as they are.

---

## Phase table

| PHASE | STATUS | BUILD/SHA | DATE | WHAT CHANGED | TESTS | KNOWN ISSUES | ROLLBACK POINT | NEXT ACTION |
|---|---|---|---|---|---|---|---|---|
| 0a — Verified baseline | Done (frozen) | `de031a2d…0ac8ac5f`, 631,094 B | 2026-10-07 | Zoom crash fixed, plus the staged-name and ornament zoom fixes; old transitions | Chrome zoom composition and reverse checks | (superseded) | `dist/baseline/…verified-2026-10-07.html` (read-only) | Comparison baseline only |
| 0b — Luxury transition pass | **DEPLOYED, then REJECTED** | `9eabff78…aa28a5a12`, 634,671 B | 2026-10-07 | Held-stack choreography; pushed as commit `e502203` | Chrome | User preferred the previous transition | `backups/2026-10-07-luxury-transition-pass/`; git `e502203` | Replaced by Phase 5 |
| 0c — Revert + final guest list | Done | `7adb692e…4faf5`, 631,354 B | 2026-10-07 | `app.js` and `.farewell` CSS restored from the baseline; the user's final 12 names; contacts hidden | Reverse, zoom, opening/glide, cards, names, Thank You, reduced motion | — | `backups/2026-10-07-pre-forensic-polish/` | — |
| 0d — Forensic polish + QA | Done | `95e275aa…5c0880c`, 632,028 B | 2026-10-07 | Names ×1.18 when settled; panel lift; `paper` easing; ending re-timed | Chrome text audit, 3D intersections, WebKit depth, real-WebKit renders | Lid regression (fixed in Phase 1) | `backups/2026-10-07-pre-compat-fixes/` | — |
| **1 — Compatibility / engine audit** | Done | `e0b8a2ee…` → **`fa3992fa…01c2c68b`**, 634,396 B | 2026-10-07 | Compat: closed-lid lift .0045, rotation keep, rotation race, Firefox close-up exit. Engine: half-written lines on zoom, desktop-Safari mouse wheel, WebKit svh lag (`lookAgain`) | Chrome 28/28. Firefox 28/28 (headless zoom timing artefact only). WebKit 28/28 (with waits). Rotation 65/65 per engine. Open-again loop OK | WebKit ≈3 px text shift on zoom sharpen; no real devices | `backups/2026-10-07-pre-lid-fix/`, `-pre-stale-line-fix/`, `-pre-wheel-fix/`, `-pre-svh-fix/`, `-pre-final-polish/` (= fa3992fa) | — |
| **2 — Numerical measurement** | Done (no code change) | `fa3992fa…` (unchanged) | 2026-10-07 | Measurement only: `docs/compat-evidence-2026-10-07/FINAL-MEASUREMENTS-REPORT.md` | Dimensions, text sizes, timing, velocity, frame performance, collisions, landscape | RED: Thank You overlap (landscape); 6–9 px landscape text; ~200 ms mid-transition halts; ending ~2× faster; opening speed jump; 194–624 restyles per frame | n/a | Feeds Phase 3 |
| **3 — Final surgical polish** | Done | `97767af4…331194b`, 641,898 B | 2026-10-08 | P1–P8 (table below): `css/invitation.css`, `js/app.js`, `js/render.js`, `js/wedding-data.js` (rotate hint text) | Chrome 28/28. WebKit 10/10. Firefox 10/10 (+ headed 9/9). Open-glide bit-identical 402/402 × 3 sizes. Closed lid 100% visible. 0 card intersections | P3 decision pending; GPU raster limit at 1×; no real devices | `backups/2026-10-07-pre-final-polish/` (fa3992fa) | — |
| **4 — Project consolidation** | Done | `97767af4…` (unchanged, re-verified) | 2026-10-08 | Merged `dev/wedding card` into this repo folder; the old folder renamed `_OLD_wedding-card_AFTER_MERGE`; added the status, changelog, rollback and merge docs | SHA re-verified; rebuild reproduces `97767af4` byte-for-byte; 0 of 348 dev files lost; `.git` byte-identical | Deploy workflow needed a decision (now decided) | `/Users/noumansafdar/dev/_project-merge-backups/2026-10-08_100531/`; `backups/2026-10-08-pre-consolidation/` | User: approve deleting the old folder |
| **5a — Barat overlap: z-clearance** | Done (superseded by 5b) | `5f017edb…d5dee4`, 641,894 B | 2026-10-08 | `asideRest` z = `D + Ti + 2.4` (was `D + Ti/2 + 0.5`): Barat lying on the left panel clears it | Improved but did not remove the overlay over Main | Overlay remained over Main's frame band (p 0.9267–0.9382, and on extraction 0.2245–0.2405) | `backups/2026-10-08-pre-barat-overlap-fix/` (app.js only), `backups/2026-10-08-pre-barat-pocket-depth-fix/` (complete: 5f017edb) | — |
| **5b — Barat pocket-depth fix** | **Done, deployed** | `038f4f38…972ca71`, 642,227 B | 2026-10-09 | `T.barat = insertTrack(slotB - 0.0045 * Hi, …)`: Barat is seated as deep as its raised panel is high | Over-Main pixels 1,058–8,450 → **0** (Chromium + Firefox, 390×844 + 1280×800); see § Phase 5 | Pre-existing raster noise only (below) | `backups/2026-10-08-pre-barat-pocket-depth-fix/` | — |
| **5c — Share preview + deployment** | **Done, deployed** | **`63ad01d2…c66b8`**, 642,779 B | 2026-10-09 | New `preview.jpg` (only "Wedding Invitation" + "Raja Hamid Ali"); share metadata rewritten in `build.js`; template moved to `index.template.html`; deployed from `main` | A/B regression below; live verification in the deployment record | `<title>` still names both people (it is the invitation page's own tab title) | `backups/2026-10-09-pre-share-preview/` (build.js, old preview.jpg); git `e502203` = the old live page | User: decide about pushing sources / handoff (privacy), P3 |

### Phase 5 — what was found, fixed and tested

**Cause (exact).** Barat is the front card of the pocket: `slotB = D − 1 − Ti/2`, so its stock face sits 1 px behind Main's front face. Every card's printed panel (`.mount`) is raised `0.0045 · h` off its card (2.91 px at 390×844), so Barat's panel ended **+1.91 px in front of Main's face** (1.00 px behind Main's own panel). While Barat slid in or out of the pocket, its panel was correctly depth-sorted in front of Main's top frame band. Walima is seated `Ti + 1.2` px deeper, so its panel is 3.17 px behind Main's face and never showed. No sorting ambiguity: the planes are parallel (rx = 0) and the order is constant from p = 0.92436.
Where: `goBack(T.barat, M.bBack)` (`bBack = [0.89147, 0.93846]`), move 4 `outP → pocket` (p 0.92342–0.93846, `sine`), z = `slot` throughout; first overlay pixel p = 0.92670, last p = 0.93820 (Barat is hidden from 0.93825). The earlier "clean at 0.9365" was only the ornament leaving: the blank white panel strip stayed over Main's band until 0.9382. The same constant caused the mirror overlay on extraction (p 0.2245–0.2405).
**Fix.** One expression in `buildTracks()` (`js/app.js`): `insertTrack(slotB - 0.0045 * Hi, …)`. Walima, Family, camera, timing, sizes and typography are untouched; the 5a z-clearance is kept.
Full report: `docs/barat-pocket-depth-2026-10-08/DIAGNOSIS.md` (local evidence; not in git).

**Share preview.** `preview.jpg` is the existing card with the panel repainted: it shows only `WEDDING INVITATION` (small caps) and `Raja Hamid Ali` (script) with the two flourishes; the "&", the bride's name, "Barat", "Walima" and both dates are gone. Florals, frame and artwork are the original pixels. 1200×630, baseline JPEG, no metadata. Original kept in `backups/2026-10-09-pre-share-preview/preview.jpg.before`.
**Share metadata (`build.js`, built with `--url`).** `og:title` / `twitter:title` = "Wedding Invitation"; `og:description` / `twitter:description` / `description` = "Raja Hamid Ali"; `og:image` / `twitter:image` = `…/preview.jpg`; `og:image:type|width|height|alt`; `og:url`; `link rel=canonical`; `twitter:card` = summary_large_image. No bride name or date in any share tag. The `<title>` and the page itself still carry both names (that is the invitation).

**Tests (A/B against the 5f017edb candidate, same harness, transitions frozen; self-test: 0 differing px).**
| Check | Result |
|---|---|
| Barat pixels over Main's face (shown vs hidden, interior of Main), p 0.9340–0.9370 step 0.00025 | candidate 1,058–1,809 px (390×844), 2,055–8,450 px (1280×800) → **final 0 px at all 13 samples** in Chromium and Firefox, both viewports. Walima, same region: 0 px before and after |
| Whole return slide (p 0.915–0.939, step 0.0005) / whole extraction (p 0.220–0.252) | pocketing samples with Barat over Main: candidate 22–23 of 31–32 (return), 33+ (extraction) → **0 of 32 / 0 of 64**. The only remaining overlap is the deliberate carry, while Barat is in the air in front of Main (z up to +61 px, p ≤ 0.922): identical before and after |
| Other cards and every other frame | 461 frames per engine/viewport (whole story): **224/224 frames outside Barat's two windows are bit-identical to the candidate** (closed Invitation lid, Main, Walima, Guests / Looking Forward, Thank You closed lid, the glides between them) in Chromium and Firefox, 390×844 and 1280×800; inside Barat's windows **237/237 frames are bit-identical with Barat hidden** (nothing else moved) |
| Card dimensions, typography | 7 stages × 4 combos: CSS size variables, card boxes and the computed type of all 794 text elements identical |
| 3D intersections (exact quad–quad, Barat vs every other surface; 149 extraction + 243 return frames per build, 390×844 and 1280×800) | no new intersecting pair; two glancing contacts with the lid's hinge strip disappeared. The pocketed Barat is 2.91 px deeper, hidden behind Main |
| Transition jumps | largest change between consecutive frames (step 0.0005) identical before/after; mean lower after (the overlay is gone) |
| Reverse scrolling | `render(p)` is a pure function of p; real forward-then-back scrolling gives identical frames in Firefox (both viewports, 40 positions, 0 differing px) and in Chromium landscape (max 2/255); in Chromium portrait 3 of 40 positions differ (one 9-px edge line up to 56/255) — **the same positions, with the same pixels, in the candidate** (pre-existing raster noise, Walima-return phase, p 0.891–0.923); the Barat slide is bit-identical both ways |
| Final pocketed position | `visibility:hidden`, y = −1, z = `D − 1 − Ti/2 − 0.0045·Hi` (error ≤ 0.005 px) for p ≥ 0.9385 |
| Runtime | 0 JS errors, 0 failed requests, 0 external requests (4 combos); static scan: no `http(s)` URL except the SVG namespace; no `<script src>`, `<link href>`, `@import`, `fetch`/XHR, `<img>`; fonts and artwork are `data:` URIs |
| Reproducibility | three builds (two in place, one in a scratch copy of the sources) give `63ad01d2…c66b8`; `preview.jpg` is byte-identical in `dist/` |
**Not verified:** real WebKit/Safari (the headless Playwright WebKit available here rendered the scene at the wrong scale, so it gave no usable evidence), real phones, and WhatsApp's own card (the preview was checked from the live tags and the live image, see the deployment record).

---

### Phase 3 items

| Item | Result |
|---|---|
| P1 performance | **FIXED** (CPU side: restyle per frame 503–528 → 77–97 elements; GPU raster limit at 1× documented, not changed) |
| P2 Thank You landscape overlap | **FIXED** (−31…−39 px → +37…+47 px clear) |
| P3 closed Invitation landscape layering | **NOT REPRODUCED / NO CHANGE** (lid 100% visible in every engine; only the lid's own leaves touch the final "n" on the 0.89 card, awaiting the user's decision) |
| P4 transition middle stop | **FIXED** (200 ms → 0 ms in Barat→Walima and Walima→Guests) |
| P5 ending speed spike | **FIXED** (peaks 1,026–1,307 → 620–646 px/s) |
| P6 landscape readability hint | **FIXED** (rotate hint: landscape phones, height ≤ 595, coarse pointer) |
| P7 opening speed jump | **ACCEPTED / UNCHANGED** (opening and glide kept bit-identical) |
| P8 minor polish | **NONE** |

---

## Known unresolved items

1. ~~Barat-card top overlap during extraction/return~~ **FIXED in Phase 5b** (0 px over Main at every sample; see above).
2. **P3 decision:** the lid's leaves touch the end of "Invitation" on the 0.89 lid (landscape + desktop). Screenshot, or "move the word".
3. Main → Barat keeps a 200 ms near-stop (YELLOW; outside P4's scope).
4. GPU raster is the frame-rate limit at 1× on this Mac (paper textures, shade overlay, band glint).
5. Landscape text is 4.5–9 px (the hint covers it); WebKit shifts fully written lines ≈3 px when zoom sharpens.
6. Tap-to-open glide is ~9.3 s: the user's decision; it is treated as protected.
7. **Real devices: never tested** (iPhone Safari, Android Chrome, WhatsApp in-app). The share preview was verified from the live HTML tags and the live `preview.jpg`, not in WhatsApp itself.
8. **Privacy finding (NOT changed):** the two hidden contact numbers are commented out in `js/wedding-data.js` (`contact: [ /* … */ ]`). `build.js` copies scripts verbatim, so the comment, and the numbers, are in the HTML source of the build AND of the live page. They are not shown on screen, but anyone who views the source can read them. To remove them, delete the comment and rebuild (changes the SHA; needs approval). This is also why the sources and `docs/CLAUDE_HANDOFF.md` were not pushed.
9. Pre-existing renderer raster noise: scrolling forward vs back to the same position over p 0.891–0.923 (Walima's return) differs by ≤ 2/255 on thousands of pixels, and by up to 56/255 on a 9-pixel edge line at p = 0.8936. Identical in the candidate and the final build; the Barat slide (p ≥ 0.9248) is bit-identical both ways.
10. The public repo's description still names both people (GitHub repository setting; not changed). The browser-tab `<title>` names both people.

## Pending decisions / next actions (user)

1. **Push the source tree and `docs/CLAUDE_HANDOFF.md`?** Needs the privacy decision in finding 8 first (strip the comment and rebuild, or accept publishing the numbers). Until then they exist only in this folder (plus the local backups).
2. **Shorter guest URL (optional).** The current URL is reliable and needs no change. The only genuinely shorter GitHub option is a new repository named exactly `bhattiassociatesofficial.github.io` (URL `https://bhattiassociatesofficial.github.io/`): it would take over this account's root site and need a second copy of the page kept in sync. Renaming this repository would shorten the path but break the existing URL (GitHub does not redirect Pages). Neither was done.
3. P3 decision (finding 2); real-phone check, including a WhatsApp send to yourself to see the share card.
4. After verifying this folder: approve permanently deleting `/Users/noumansafdar/dev/_OLD_wedding-card_AFTER_MERGE`.
5. `deployment/og-meta-draft.diff` and `deployment/index.og-draft-on-rejected-9eabff78.html` are obsolete (the share metadata is now generated by `build.js`); kept on disk only.

## Where things are

- Handoff for a new Claude session: `docs/CLAUDE_HANDOFF.md` (read first; local only)
- Change log: `docs/CHANGELOG.md` · Rollback: `docs/ROLLBACK.md`
- Phase 5 diagnosis, tools and raw evidence: `docs/barat-pocket-depth-2026-10-08/` (`DIAGNOSIS.md`, `tools/`, `logs/`, `evidence/`, `preview-work/`)
- Merge report: `docs/compat-evidence-2026-10-07/PROJECT-MERGE-REPORT.md`
- Polish report: `docs/compat-evidence-2026-10-07/FINAL-POLISH-REPORT.md`
- Measurements: `docs/compat-evidence-2026-10-07/FINAL-MEASUREMENTS-REPORT.md`
- Phase 1: `docs/compat-evidence-2026-10-07/phase1/PHASE1-REPORT.md`
- In-project backups: `backups/` (build snapshots, `2026-10-08-pre-consolidation/`, `2026-10-08-pre-barat-pocket-depth-fix/`, `2026-10-09-pre-share-preview/`) · frozen baseline: `dist/baseline/`
- Pre-merge backups: `/Users/noumansafdar/dev/_project-merge-backups/2026-10-08_100531/`

## Deployment record

Deployed by the commit "fix: finalize Barat overlap and wedding invitation share preview" (find it with `git log -1 -- preview.jpg`), pushed normally to `origin main`: no force push, no history rewrite. It changes `index.html` (the build `63ad01d2…c66b8`, byte-identical to `dist/`), adds `preview.jpg`, and adds `PROJECT_STATUS.md`, `docs/CHANGELOG.md`, `docs/ROLLBACK.md`.
A commit cannot contain its own SHA, so the commit SHA, the GitHub Pages build result and the live verification (hash of the live page and of the live `preview.jpg`, the live share tags, the live Chrome test of the Barat return) are recorded in the local copy of this file after the push.
