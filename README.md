# You’re In On It (YIOI)

**Some jokes never die. Good ones let you in.**

A cooperative Name–Place–Animal–Thing game for 2–8 friends or family members. The current appearance, five colour palettes, reading preferences, answer table, scoring discussion and shared story space are preserved.

## Publish — no GitHub or local tools

1. Sign in to your Netlify account.
2. Open **https://app.netlify.com/drop**.
3. Drop **netlify-ready-game.zip** onto the page. If your browser asks for a folder instead, unzip it and drop the folder containing `netlify.toml` and `package.json`.
4. Wait for Netlify to install the locked dependencies, build the project, and deploy the game function. Open the resulting site address.

Use the signed-in, source-project Drop flow so Netlify runs the included build configuration. Anonymous static uploads do not run the backend build. No GitHub connection, Terminal commands, environment-variable configuration, API keys, external database, or manual database setup is needed. Netlify automatically provides the function’s access to its own site-wide Blobs store.

After deployment, create a game, share its invite link and five-letter code, and join from another browser or device. Both players must open the same deployed site. Netlify project visibility must allow the people you invite to visit it.

## Gameplay

The host can customise four categories and the time limit. Each of three rounds assigns a different letter. Players privately type one answer per category. After everyone submits, or time expires, the whole table is revealed: one row per player. Unique fitting answers start at 10 points, repeated answers at 5 each, and blanks or letter mismatches at 0. Players suggest scores for others’ answers; the host records and confirms the group’s decision. Anyone can add a joke or story. Confirmed scores contribute to one shared total.

The final result deliberately remains cooperative: **all players share the room’s group title**, by default **Keepers of the Lore**. There is no new individual leaderboard or competitive winner. A `winner` result explicitly identifies all group members as co-winners. Selected answers and shared stories can be exported from the final archive.

## Included project

- `netlify.toml`: automatic production build and function settings. No custom API routing.
- `package.json` and `package-lock.json`: exact tested dependencies and build scripts.
- `public/`: editable frontend source; `dist/`: already-built frontend.
- Root `index.html` and assets: a ready-built frontend copy for upload detection.
- `netlify/functions/game.ts`: the sole standard function entry point.
- `lib/`: game engine and request/storage helpers, outside the functions directory.
- `artifacts/functions/game.zip`: self-contained packaged function produced by Netlify’s bundler.
- `artifacts/functions/manifest.json`: portable function build manifest.
- `artifacts/verification.json`: packaged-function playthrough results.
- `tests/` and `scripts/`: reproducible source, concurrency, build, and package checks.

The outer project ZIP has no development `node_modules`, caches, logs, local room records, credentials, or environment files. The packaged function ZIP contains its necessary runtime dependencies; those are part of the compiled function, not a development install.

## Backend and concurrency

The browser calls `/.netlify/functions/game` directly for every operation. There is no `/api/game` alias, redirect, or `config.path`.

Production runs request-scoped functions, not a continuously running server. A named, site-wide Netlify Blobs store uses **strong consistency**. Each room is stored as a single versioned document. Room creation uses `onlyIfNew`; every update uses the ETag from `getWithMetadata` and `onlyIfMatch`. Contended operations re-read and retry the whole action. Bounded request receipts prevent duplicate retries from adding a story twice or advancing multiple rounds. Missing ETags fail safely rather than falling back to an unconditional write. Scores are derived from confirmed entries rather than incrementing separate counters.

Deadlines are checked on requests, including the existing state polling. Timer expiry needs no background worker, scheduled job, WebSocket server, or interval process. Authentication and room limits remain enforced by the function; seat tokens never appear in public room state.

## Verification performed

- TypeScript check and production frontend build.
- Standard function packaging with `@netlify/zip-it-and-ship-it`, including its runtime bootstrap and dependencies.
- Complete three-round, two-player games through the source handler and through the actual extracted packaged function.
- Packaged-function totals: **40 → 80 → 120**; final phase: **finished**; shared winner title: **Keepers of the Lore**, with both players included.
- Duplicate and unique scoring, suggestions, host confirmation, story capture, hidden drafts, permissions, idempotent retries, and cold function instances.
- Eight simultaneous joins and draft saves, the eight-player limit, ETag conflicts, and deadline expiry.
- Real `@netlify/blobs` SDK calls in the packaged-function test, including conditional-create and stale-ETag rejection.

The package test uses Netlify’s local BlobsServer. Its published version omits GET ETags, so a small **test-only** adapter supplies the emulator’s existing ETag calculation. Production does not use that adapter or a substitute store. A live Netlify production deployment has not been performed from this workspace.

## Hamster logo update
The approved Twilight hamster logo is integrated into the header and home screen. Twilight Ink is the default for new visitors and when display settings are reset. Existing saved reading and colour preferences remain respected. All five palette logos are included under public/assets and switch with the player’s selected palette. The branding is preserved alongside the saved-room and joke-rating update.

## The Joke Drawer, memories and joke reactions

Rooms you create or join appear in **The Joke Drawer** in the same browser. **Keep this chapter** pins a room; **Back to the drawer** returns to the dashboard without deleting the room or leaving your player seat. **Jump back in** resumes that seat, and **Open the drawer** opens revealed answers and saved conversations. Refreshing the page preserves the list. Browser storage contains your private seat access: clearing it or using another device does not restore that access automatically. There are no accounts or cross-device history sync in this version. Download the archive for an additional personal copy.

**Play again** preserves the completed match in the room’s game history before starting a new one. The memories game selector opens previous matches; their jokes remain open for conversation and ratings. Removing a room from this browser only removes the dashboard entry, not the online room.

Rate other players’ jokes with five animated emoji buttons: 🙂 Made me smile, 😄 A chuckle, 😂 Good laugh, 🤣 Group legend, or 🤩 Instant classic. Each player has one changeable rating per joke and cannot rate their own joke. The room displays an average and rating count. Reactions do not change gameplay points. Animations stop after a brief response and are disabled by the device’s reduced-motion preference. Text labels, keyboard controls and selected-state indicators accompany the emojis.

Verification also covers return with the same seat, post-game conversations, rating validation and replacement, permissions, preserved match history, and unchanged scores after ratings. A browser playthrough verified saving, leaving, refreshing The Joke Drawer, reopening memories and retaining a 5/5 rating.

To update your existing Netlify game, extract this ZIP, open that project’s **Deploys** page and upload the project folder using its manual deployment area. Keep using the same Netlify project so its Blobs rooms remain associated with the same site.
