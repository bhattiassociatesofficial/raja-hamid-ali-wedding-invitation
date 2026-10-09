# CHANGELOG

Every major phase, newest first. SHA-256 values are of `dist/Raja-Hamid-Ali-Wedding-Invitation.html` (the single-file build).
Paths are relative to the canonical folder `/Users/noumansafdar/dev/raja-hamid-ali-wedding-invitation/` unless absolute.
How to restore any of these: `docs/ROLLBACK.md`.

---

## 2026-10-10 — Phase 7: Tap-to-advance experience (one tap, one page)

- **OLD BUILD (live before):** `db45b1985149b36e1ac01355ec9bb8f50a7b0b55af582d2236ea53d72f4f2cd5` (642,727 bytes; scroll-driven)
- **NEW BUILD:** `5aa24e824fbab1c13de1293f382ba3f449b0f444e35dede8363cf6eee39da27b` (**652,706 bytes**), built with `node build.js --url https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/`; three builds (two in place, one in a scratch copy of the sources) identical. `preview.jpg` unchanged (`167e9511…592c8bea6`, 213,574 bytes).
- **WHY:** guests did not discover that the invitation had to be scrolled. Scrolling is no longer how the story moves.
- **CHANGES:**
  - **Story controller (`js/app.js`).** The scene is still a pure function of the progress `p`; what moves `p` is now a small state machine, not the page scroll. Six pages: 0 closed Invitation, 1 Main, 2 Barat, 3 Walima, 4 Guests (Looking Forward, all 12 names), 5 Thank You. A tap plays the story from the page that is showing to the next one (the transition, then the writing, then a short settle) and stops there; nothing advances by itself. `go()` accepts a tap only at rest, so taps during a movement are ignored (never queued), and one tap can only ever advance one page. Back plays the same stage in reverse (writing un-written 3.3× faster). The page cannot scroll (`.js, .js body { overflow: hidden }`, `.track` hidden), so wheel, swipe and trackpad can never skip a page; without JavaScript the flat cards still scroll as before.
  - **Interface.** The closed card keeps "Tap to open" (shown only once a tap is accepted). From the Main page on, "Tap to continue" (Guests: "Tap to finish") appears at the foot of the screen only when the page is complete and a tap would be accepted; it fades out in 0.2 s when used, breathes slowly (0.64↔1 over 4.8 s) and is hidden in the close-up and in reduced motion. A tap anywhere on the scene (also on the hint text) advances. A Back control (bottom left, 44×44 target) and the chapter marks (progress only, no longer buttons). "Scroll to explore" and its drip line are gone. Keys: Enter/Space/→/PageDown advance, ←/PageUp go back.
  - **Pace (`PACE`, `PLAN`).** Motion 2.8 s per screen of the story (lid, a card leaving or entering the pocket); settle 2.6 s/screen; names 2.2 s each (written large, held, set in place); a tap becomes available 0.7 s after the last movement. Per page: Main 18.3 s, Barat 13.3 s, Walima 13.3 s, Guests 35.7 s, Thank You 8.4 s (about 89 s in all). Writing starts only after the card has arrived and settled. Body lines are written at 25–28 glyphs/s (they were 25–43), headings 8–16, venue/names 6–10 (`KIND.l` per-glyph weight 0.025 → 0.04).
  - **Zoom.** Kept: the magnifier button or Z enters the close-up, drag/wheel pan, ✕ / Esc / a tap leaves it on the same page. Tapping the card no longer enters the close-up (a tap now means "next page"). While zoomed no tap, key or drag advances; the story carries on underneath and the hint returns on leaving.
  - **Reduced motion.** Each page is shown whole and a tap changes the page with a short cross-fade (0.3 s out, 0.5 s in); every page is still reachable by tap.
  - **Resize / rotation** only re-measure; the page and the movement in progress continue exactly where they were.
  - **Fixed in this phase, found while testing:** (1) on desktop the hover "peek" lifts the lid 9°, and the first tap snapped it to 0° before opening (a visible drop): the tap now takes the lift over (`story.peekHold`), the lid opens monotonically from where it was; (2) "Open again" appears where "Tap to finish" was, so the extra taps of an impatient guest during the closing could restart the invitation: the button now answers only after the farewell has been resting 1.5 s and never while the ending moves; (3) the hint's fade-out is 0.2 s (was 0.4 s) so it is gone while a tap would be ignored; (4) the hint and Back are a little larger on large pointer screens.
  - Cards, typography, artwork, the Barat pocket-depth fix, the camera and card choreography, zoom geometry, content (names, dates, venues), `preview.jpg` and all share metadata are unchanged.
- **TESTS** (local evidence: `docs/barat-pocket-depth-2026-10-08/`; tools `tap-suite.js`, `extra-checks.js`, `tap-fuzz.js`, `visual-regress.js`, `join-speed.js`, `motion-probe.js`; real mouse/touch/keyboard input; see `PROJECT_STATUS.md` § Phase 7 for the numbers):
  - full real-time journey with real input, 111–115 checks per run, **all pass**: Chromium 390×844 (111; with real touch input 115), 844×390 (112), 1280×800 (111); Firefox (headed, GPU) 390×844 (111), 1280×800 (111); Playwright WebKit 390×844 (111; interaction logic only: its 3D rendering is wrong on this Mac)
  - extra checks (real touch tap and CDP swipes, zoom during a movement, Open-again guard, desktop hover lid): pass in Chromium (390×844 16/16, 1280×800 20/20) and Firefox (390×844 11/11, 1280×800 15/15)
  - random-input attack (`tap-fuzz.js`, invariants checked on every frame): 8 seeds, 3 viewports, Chromium and Firefox, all pass
  - A/B against the deployed Phase 6 build with the new build placed at the old build's actual `p`: outside the writing ranges Chromium 273/273 (390×844) and 272/273 (1280×800), Firefox 199/199 (390×844) and 198/199 (1280×800) frames identical, all 6 rest pages identical in both engines (the one frame differs by one glyph at ≤ 10/255 at the very start of the writing)
  - negative controls: the new checks fail on the previous build exactly where the defects were (lid drop of 9.000° at 1280×800, invitation restarted by taps at 390×844)
  - not verified: real Safari/WebKit rendering, real phones, WhatsApp in-app
- **ROLLBACK LOCATION:** `backups/2026-10-09-final-db45b198/` (the previous, scroll-driven, deployed build and sources), `backups/2026-10-10-pre-tap-wip/` (the uncommitted tap-driven work in progress found at the start of this phase), new complete backup `backups/2026-10-10-final-5aa24e82/`.
- **DEPLOYMENT:** (recorded after the push)

## 2026-10-09 — Phase 6: Privacy cleanup and browser-tab title

- **OLD BUILD (live before):** `63ad01d2f49bbfee909745606c63d3d2242e10a36221e79a895ee18c3dbc66b8` (642,779 bytes)
- **NEW BUILD:** `db45b1985149b36e1ac01355ec9bb8f50a7b0b55af582d2236ea53d72f4f2cd5` (**642,727 bytes**), built with `node build.js --url https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/`; three builds (two in place, one in a scratch copy) identical. `preview.jpg` unchanged (`167e9511…592c8bea6`, 213,574 bytes).
- **CHANGES** (5 changed lines in 4 hunks of the built HTML differ from `63ad01d2`):
  - **Contact numbers:** `js/wedding-data.js` kept the two hidden numbers in a comment inside the empty `contact: [ … ]` list. The comment is removed (`contact: []`, with a comment on how to add numbers). The invitation never displayed them (an empty list renders no contact block), so no visible content changed. The handoff's contact paragraph was rewritten without the digits; the three local downloads of the old live page were removed. Old backups were left untouched.
  - **Still public:** the numbers stay readable in `index.html` of earlier commits of the public repository (and in the Pages artifact(s) of earlier deployments, sign-in required, expiring); history is not rewritten. Details and options: `PROJECT_STATUS.md` § Phase 6. The exact commits and URLs are kept in the local handoff only.
  - **Browser-tab title** is exactly `Raja Hamid Ali`: `document.title = data.groomName` (`js/app.js`), `const title = data.groomName` (`build.js`), template `<title>`. The share tags are unchanged (`og:title` "Wedding Invitation", `og:description` "Raja Hamid Ali", `og:image` = the approved `preview.jpg`).
  - No animation, card, typography or preview change. Barat fix (Phase 5b) intact.
- **TESTS** (evidence local: `docs/barat-pocket-depth-2026-10-08/`):
  - exposure audit: five independent read-only agents plus a critic searched the live site, git history (all commits, the public GitHub copies), the publishable files and the local copies, in every common format and encoding
  - A/B against the deployed `63ad01d2`: Chromium and Firefox, 390×844 and 1280×800, 273 frames each, **all bit-identical** (including Barat's extraction and return with Barat shown); dimensions and the typography of 794 text elements identical; 0 JS errors, 0 failed requests, 0 external requests
  - title / share-tag / contact check, 18 points × 4 combinations, 72/72 pass: runtime and `<title>` exactly `Raja Hamid Ali`; share tags unchanged and free of the bride's name and dates; the invitation's rendered text identical at all 7 stages; the Guests card has no contact block in either build; no phone-like digits in the rendered text or the raw HTML (the same check fails on the old build, as it should)
  - Barat probes: 0 px over Main at all 13 fine samples, 32 pocketing samples of the return and 64 of the 65 extraction samples, both engines, both viewports
  - not verified: real WebKit/Safari, real phones, WhatsApp's own preview card
  - reverse scrolling and the Chromium-portrait raster noise: unchanged from Phase 5 (`PROJECT_STATUS.md` finding 9)
  - three builds identical
- **ROLLBACK LOCATION:** `backups/2026-10-09-pre-privacy-cleanup/` (old `wedding-data.js`, `app.js`, `build.js`, template, deployed page and docs; they still hold the numbers, keep them local), `backups/2026-10-09-final-63ad01d2/` (Phase 5 build); new complete backup `backups/2026-10-09-final-db45b198/`.
- **DEPLOYMENT:** commit `95140afd1b36067eac9702200871bb224ae0281d` (`acc5747..95140af`, normal push to `origin main`); Pages build `built`; live page sha256 `db45b198…2cd5` (642,727 B) and live `preview.jpg` `167e9511…` verified by fetching the live URLs; live `<title>` `Raja Hamid Ali`; 0 occurrences of the contact numbers in the live page and docs; live checks 17/17 in Chromium and Firefox at both viewports, live Chromium A/B 273/273 bit-identical, live Barat probes 0 px. Details: `PROJECT_STATUS.md` § Deployment record. Guest URL: https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/

## 2026-10-09 — Phase 5: Barat pocket-depth fix, share preview, deployment

- **OLD BUILD (live before):** `9eabff78025fd8d955e61d9480a79558edf2ab4c8196997c8384019aa28a5a12` (634,671 bytes, the rejected luxury pass, commit `e502203`)
- **OLD BUILD (local candidate):** `5f017edb8fb4b276fc9bcf9979e690bf389a9ad5272e3ebbd257f74d22d5dee4` (641,894 bytes; = `97767af4` + the Phase 5a z-clearance)
- **INTERMEDIATE:** `038f4f3896223521ebcf829e0e28efceb045b2e7b79d40cc3822581dd972ca71` (642,227 bytes; the pocket-depth fix, no `--url`)
- **NEW BUILD (deployed):** `63ad01d2f49bbfee909745606c63d3d2242e10a36221e79a895ee18c3dbc66b8` (**642,779 bytes**), built with `node build.js --url https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/`; reproduced byte-for-byte by three builds (two in place, one in a scratch copy)
- **`preview.jpg`:** `167e95115e30b3188390471b6117443e42f83a630dbaab672d19a9d592c8bea6` (213,574 bytes, 1200×630, baseline JPEG, no metadata). Before: `ce30ec50…eba3c2d2` (216,859 bytes)
- **CHANGES:**
  - **Barat overlap, root cause:** Barat's stock face sits 1 px behind Main's face, but its printed panel is raised `0.0045·h` (2.91 px at 390×844), so the panel stood +1.91 px in front of Main's face (1.00 px behind Main's panel) and was drawn over Main's top frame band while Barat slid into / out of the pocket. Walima is seated `Ti + 1.2` px deeper (panel −3.17 px), so it never showed. Not a sorting ambiguity: constant parallel planes from p = 0.92436. Overlay p 0.92670–0.93820 (return) and 0.2245–0.2405 (extraction). The "clean at 0.9365" seen earlier was only the ornament leaving; the blank white panel strip stayed until 0.9382.
  - **Fix:** `js/app.js`, `buildTracks()`: `T.barat = insertTrack(slotB - 0.0045 * Hi, …)` (one expression + comment). Panel now 1.00 px behind Main's face at every size. Nothing else in the animation changed.
  - **Share preview:** `preview.jpg` shows only "WEDDING INVITATION" and "Raja Hamid Ali" (with the flourishes). The "&", the bride's name, "Barat", "Walima" and both dates were removed; florals/frame/artwork are the original pixels.
  - **Share metadata (`build.js`):** `og:title`/`twitter:title` = "Wedding Invitation"; `og:description`/`twitter:description`/`description` = "Raja Hamid Ali"; `og:image`/`twitter:image` = `…/preview.jpg` (+ type, width, height, alt); `og:url`; `link rel=canonical`. The old tags carried both names and both dates. Orphaned `when()`/`description` removed.
  - **Deploy workflow:** the build template moved from the root `index.html` to `index.template.html` (identical bytes; `build.js` now reads it), so the root `index.html` can be the deployed page. Deploy = copy the verified build to `index.html`, commit `index.html` + `preview.jpg` (+ the docs), push `main`.
  - Not pushed at the time (privacy): source tree and `docs/CLAUDE_HANDOFF.md` (they contained the two hidden phone numbers as plain text); resolved in Phase 6.
- **TESTS** (evidence: `docs/barat-pocket-depth-2026-10-08/`, local):
  - over-Main pixels (Barat shown vs hidden, restricted to Main's face): 1,058–1,809 px (390×844) / 2,055–8,450 px (1280×800) → **0 px** at every sample of p 0.9340–0.9370 step 0.00025, at 32 pocketing samples of the return and 64 of the extraction; Chromium + Firefox, both viewports; Walima same region 0 px before and after
  - exact 3D quad-intersection check (Barat vs every other surface, extraction + return): no new intersecting pair (149 + 243 frames per build, both viewports); two glancing contacts with the lid's hinge strip disappeared
  - A/B regression vs `5f017edb`: Chromium and Firefox, 390×844 and 1280×800, 461 frames each: 224/224 frames outside Barat's two windows bit-identical (closed Invitation lid, Main, Walima, Guests, Thank You lid, all glides); 237/237 frames inside them bit-identical with Barat hidden; dimensions and typography identical (7 stages, 794 text elements); real forward/back scrolling identical in Firefox, ≤ 2/255 noise in Chromium landscape, and in Chromium portrait the same 3 pre-existing raster-noise positions as the candidate; largest frame-to-frame change identical before/after; 0 JS errors, 0 failed requests, 0 external requests
  - not verified: real WebKit/Safari (headless WebKit here gave an unusable render), real phones, WhatsApp's own card
  - static scan of the build: no `http(s)` URL other than the SVG namespace; no `<script src>`, `<link href>`, `@import`, `fetch`/XHR, `<img>`; all fonts and artwork are `data:` URIs
- **ROLLBACK LOCATION:** `backups/2026-10-08-pre-barat-pocket-depth-fix/` (5f017edb, complete, read-only, `SHA256SUMS`), `backups/2026-10-09-pre-share-preview/` (old `build.js`, old `preview.jpg`); the old live page: see git history
- **DEPLOYMENT:** commit `acc57471e3fd0f4c1008aa48396457db91ca5c94` (`e502203..acc5747`, normal push to `origin main`); Pages build `built`; live page sha256 `63ad01d2…c66b8` (642,779 B) and live `preview.jpg` `167e9511…` verified by fetching the live URLs; live share tags carry no bride name or date; live Barat probes (Chromium + Firefox, both viewports): 0 px over Main. Details: `PROJECT_STATUS.md` § Deployment record. Guest URL: https://bhattiassociatesofficial.github.io/raja-hamid-ali-wedding-invitation/

## 2026-10-08 — Phase 4: Project consolidation

- **OLD BUILD:** `97767af49294f3ffd50ff56bd5a0afe3f676d14738a7cdf4f520fa980331194b`
- **NEW BUILD:** same (no build change)
- **SHA-256:** `97767af49294f3ffd50ff56bd5a0afe3f676d14738a7cdf4f520fa980331194b` (641,898 bytes), re-verified after the merge
- **CHANGES:**
  - Merged the development project `/Users/noumansafdar/dev/wedding card` (source, dist, docs, evidence, backups) into this git repository folder, which is now the only project folder.
  - `.git`, the remote, `main` and the history were left untouched.
  - The root `index.html` is now the build template (from `wedding card`). The deployed page stays in git history (`e502203`).
  - The uncommitted social-preview draft was moved to `deployment/index.og-draft-on-rejected-9eabff78.html`, plus `deployment/og-meta-draft.diff`.
  - Root `preview.jpg` = the designed social card (216,859 B). The older closed-lid preview from `wedding card` moved to `deployment/preview.old-closed-lid-render.jpg`.
  - Added `PROJECT_STATUS.md`, `docs/CHANGELOG.md`, `docs/ROLLBACK.md` and `docs/compat-evidence-2026-10-07/PROJECT-MERGE-REPORT.md`; updated `docs/CLAUDE_HANDOFF.md`.
  - Old folder renamed `/Users/noumansafdar/dev/_OLD_wedding-card_AFTER_MERGE` (kept intact).
  - Not deployed, not committed, not pushed.
- **TESTS:**
  - full SHA-256 manifests: of the 348 development files, 0 were lost:
    - 345 byte-identical at the same path
    - 1 moved (`preview.jpg` → `deployment/preview.old-closed-lid-render.jpg`)
    - 1 intentionally updated (`docs/CLAUDE_HANDOFF.md`; its pre-merge copy is in `backups/2026-10-08-pre-consolidation/`)
    - `.DS_Store` left out
  - `.git` byte-identical
  - `node build.js` on a scratch copy of the merged source reproduces `97767af4…` byte-for-byte
  - `git fsck` clean; remote and HEAD unchanged
  - log: `docs/compat-evidence-2026-10-07/PROJECT-MERGE-VERIFICATION.log`
- **ROLLBACK LOCATION:**
  - `/Users/noumansafdar/dev/_project-merge-backups/2026-10-08_100531/` (`wedding-card-pre-merge/`, `raja-hamid-ali-wedding-invitation-pre-merge/`, plus a `.SHA256SUMS` manifest for each)
  - `backups/2026-10-08-pre-consolidation/` (the pre-merge handoff)

## 2026-10-08 — Phase 3: Final surgical polish + performance

- **OLD BUILD:** `fa3992faf309720c023f8acbcffc2b158b2ce171fb7cb7a1a703ea2e01c2c68b` (634,396 B)
- **NEW BUILD:** `97767af49294f3ffd50ff56bd5a0afe3f676d14738a7cdf4f520fa980331194b` (641,898 B)
- **SHA-256:** `97767af49294f3ffd50ff56bd5a0afe3f676d14738a7cdf4f520fa980331194b`
- **CHANGES** (report: `docs/compat-evidence-2026-10-07/FINAL-POLISH-REPORT.md`):
  - P1 performance FIXED: CSS stops for the per-frame light variables on `.stock`, `.sheet` and `.st-gap`; restyle per frame 503–528 → 77–97 elements.
  - P2 Thank You landscape overlap FIXED: compact one-line farewell.
  - P3 closed-lid landscape layering NOT REPRODUCED / NO CHANGE.
  - P4 transition middle stop FIXED; P5 ending speed spike FIXED: `moves()`/`at()`, `turn()`/`emerge()`/`goBack()`, `sine`.
  - P6 landscape readability hint FIXED (rotate hint).
  - P7 opening speed jump ACCEPTED / UNCHANGED. P8 NONE.
  - Files: `css/invitation.css`, `js/app.js`, `js/render.js`, `js/wedding-data.js`.
- **TESTS:**
  - Chrome 154: 28/28. WebKit 26.4: 10/10. Firefox 150: 10/10, plus headed GPU 9/9.
  - Open-again loop passes.
  - Opening/glide bit-identical (402/402 points × 3 sizes; 9,272 ms).
  - Closed lid 100% visible (9 sizes, 3 engines). 0 card intersections.
  - Real-WebKit visual pass. No real devices.
- **ROLLBACK LOCATION:** `backups/2026-10-07-pre-final-polish/` (fa3992fa, complete sources + `SHA256SUMS`)

## 2026-10-07 (late night) — Phase 2: Numerical measurement

- **OLD BUILD / NEW BUILD:** `fa3992faf309720c023f8acbcffc2b158b2ce171fb7cb7a1a703ea2e01c2c68b` (unchanged; measurement only)
- **CHANGES:** none to code. Report: `docs/compat-evidence-2026-10-07/FINAL-MEASUREMENTS-REPORT.md`, evidence in `final-measurements/`.
- **TESTS / FINDINGS (RED):**
  - Thank You overlaps the closed card on landscape phones (32–38 px)
  - landscape essential text 6–9 px
  - ~200 ms mid-transition halts
  - ending ~2× faster than the other transitions
  - opening camera speed jump at ~1.1 s
  - 194–624 elements restyled per frame
- **ROLLBACK LOCATION:** n/a (no change)

## 2026-10-07 (night) — Phase 1b: Engine-level audit (after the Mac restarted)

- **OLD BUILD:** `e0b8a2ee6204940a71e5a481ea1df029164dad8f1e25352aa45657a2d4b29c9a` (633,339 B)
- **NEW BUILD:** `fa3992faf309720c023f8acbcffc2b158b2ce171fb7cb7a1a703ea2e01c2c68b` (634,396 B)
  - intermediate: `4d6662171bc8a3da97d4054ef677e5ea38fe8e05e32d73bba2eb3c928d1fd6ae` (after the stale-line fix)
  - intermediate: `f8d163bbeabd00034dd6abe2f6f0e4a225b4d110c949df52720c4c7bc15956c2` (after the wheel fix)
- **CHANGES:**
  - half-written lines jumped when zoom sharpened (`measure()` clears `it.last`)
  - desktop-Safari mouse wheel (the close-up wheel listener is added only while zoomed)
  - WebKit stale card size after rotation (svh lag; `lookAgain`)
- **TESTS:**
  - Chrome 28/28. Firefox 28/28 (headless zoom timing artefact only). WebKit 28/28 (with waits).
  - Every-stage rotation 65/65 per engine. Open-again loop OK. Glide 9.3–9.5 s.
  - Report: `docs/compat-evidence-2026-10-07/phase1/PHASE1-REPORT.md`
- **ROLLBACK LOCATION:**
  - `backups/2026-10-07-pre-stale-line-fix/` (e0b8a2ee)
  - `backups/2026-10-07-pre-wheel-fix/` (4d666217)
  - `backups/2026-10-07-pre-svh-fix/` (f8d163bb)

## 2026-10-07 (evening) — Phase 1a: Compatibility audit

- **OLD BUILD:** `95e275aa48726861b31f87e36f0d4a66b36b843bda5a06419c60f5edb5c0880c` (632,028 B)
- **NEW BUILD:** `e0b8a2ee6204940a71e5a481ea1df029164dad8f1e25352aa45657a2d4b29c9a` (633,339 B)
  - intermediate: `c44b71811daf73a9fc3c6df5806d118b4861665a54e13b9caab060883eeb21e8` (after the first two fixes)
- **CHANGES:**
  - closed-lid regression fixed (`.mount` lift `.007` → `.0045`, must stay below `--Tc`)
  - rotation keeps the story moment
  - rotation race (`remeasuring()`)
  - rotate-mid-glide (`tween.max`)
  - Firefox close-up exit
- **TESTS:**
  - closed screens pixel-identical to the baseline in Firefox (22 sizes) and ≥ 45.8 dB in Chrome
  - lift margins measured at 11 sizes
  - evidence `docs/compat-evidence-2026-10-07/01…10-*`
- **ROLLBACK LOCATION:**
  - `backups/2026-10-07-pre-compat-fixes/` (95e275aa)
  - `backups/2026-10-07-pre-lid-fix/` (c44b7181)

## 2026-10-07 (day) — Forensic polish + QA

- **OLD BUILD:** `7adb692ec20aae3cbe56379b547882231924053aa0839f0b0e30c19003c4faf5` (631,354 B)
- **NEW BUILD:** `95e275aa48726861b31f87e36f0d4a66b36b843bda5a06419c60f5edb5c0880c` (632,028 B)
- **CHANGES:**
  - names ×1.18 when settled (staging cap 1.95); family subtitle ×1.15
  - `.mount` lift (regressed; fixed in 1a)
  - `settle`/`out` → `paper`
  - ending re-timed (close 2.4 → 2.8 screens)
- **TESTS:** Chrome text audit, 3D intersection detector, WebKit depth check, real-WebKit renders (CLAUDE_HANDOFF §8c)
- **ROLLBACK LOCATION:** `backups/2026-10-07-pre-forensic-polish/` (7adb692e)

## 2026-10-07 (late night) — Luxury pass reverted + final guest list

- **OLD BUILD:** `9eabff78025fd8d955e61d9480a79558edf2ab4c8196997c8384019aa28a5a12` (634,671 B; live)
- **NEW BUILD:** `7adb692ec20aae3cbe56379b547882231924053aa0839f0b0e30c19003c4faf5` (631,354 B)
- **CHANGES:**
  - `js/app.js` and the `.farewell` CSS restored byte-for-byte from `dist/baseline/`
  - the user's final 12-name guest list; contacts hidden
- **TESTS:** reverse, zoom, opening/glide, cards, names, Thank You, reduced motion; 0 errors
- **ROLLBACK LOCATION:** `backups/2026-10-07-luxury-transition-pass/` (9eabff78: app.js, CSS, dist, handoff); git commit `e502203`

## 2026-10-07 — Luxury transition pass (DEPLOYED, later REJECTED)

- **OLD BUILD:** `de031a2db17ef242996fc5be9aef6f5500c90e89fd7156e8d82f210c0ac8ac5f` (631,094 B; verified baseline)
- **NEW BUILD:** `9eabff78025fd8d955e61d9480a79558edf2ab4c8196997c8384019aa28a5a12` (634,671 B)
- **CHANGES:** held-stack choreography, scroll-driven Thank You, `velvet` easing. Deployed to GitHub Pages as commit `e502203` (2026-10-07 03:06 +0500). **Still the live page as of 2026-10-08.**
- **TESTS:** Chrome
- **ROLLBACK LOCATION:** `dist/baseline/Raja-Hamid-Ali-Wedding-Invitation.verified-2026-10-07.html` (de031a2d, read-only)

## 2026-10-07 — Verified baseline (zoom fixes)

- **BUILD:** `de031a2db17ef242996fc5be9aef6f5500c90e89fd7156e8d82f210c0ac8ac5f` (631,094 B), frozen read-only at `dist/baseline/`
- **CHANGES:** zoom crash (`el.world`), staged-name room scaled by `Z`, header `min-width` scaled by `--z`
- **TESTS:** Chrome zoom composition; reverse at 121 points
