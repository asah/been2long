# Been2long

A personal catch-up page with a short overview, dated stories, linked photos, and a direct path back to email. The included Alex/Jamie collection is fictional demo content.

## Portable HTML

Open [`been2long.html`](been2long.html) directly in a browser, or send that single file to someone. It needs no backend, installation, or build. All CSS, JavaScript, and the favicon are inline. Photos, Google Fonts, and optional embedded videos stay at their original hosts, so those need an internet connection; system fonts provide an offline fallback.

Edit the `catchup-data` JSON block near the top of the HTML to change the author and photo, default reader/date/interests, category names, overview groups, threads, stories, and media credits. Each story includes an external image URL, paragraph array, thread IDs, and a short `summary` used in the opening overview. Keep valid JSON with double quotes and no trailing commas. If text contains a literal `<`, write it as `\u003c` inside the JSON block. Save and reopen/refresh the HTML; no rebuild is needed. The JSON uses two-space indentation, and the rest of the file stays unchanged when you edit content, making diffs straightforward.

The portable version includes the timeline modal, story details, category/topic/date filters, photo gallery, sorting, and reading preferences. It has no Studio, accounts, invitations, email notifications, or API requests. All stories in its JSON are included in the file. Shared reading links are useful when the HTML is hosted; to share a local copy, send the HTML file itself.

To refresh its inline UI from the shared frontend sources, run `npm run build:standalone` from the workspace root. Rebuilds are deterministic and preserve the existing JSON block, including your formatting and edits. `npm run build:standalone -- --reset-data` explicitly replaces the data with the fictional demo. This export starts with the checked-in demo content, not the server's private published collection.

## Run

Requires Node.js 20 or later; there are no application dependencies.

```sh
npm run dev
npm run check
npm test
npm run build
```

Open <http://localhost:3000>. The local server listens on loopback and saves shared application state in `web/.data/catchup.json`. That file survives server restarts and is shared by browser sessions; browser local storage now holds only reading preferences and any older prototype stories awaiting import.

## Author setup and readers

1. Open **Studio**. On localhost, enter the author's name/email and a password of at least twelve characters. Hosted publisher access uses the signed-in Sites identity, plus a one-time `AUTHOR_SETUP_KEY` when claiming the author.
2. Add the first reader with an address or mailbox such as `John Smith <john@example.com>`, their last catch-up date, and optionally how you met. **Readers & invites** opens the same form later; saving the same email updates its existing reader record and preserves its link.
3. Copy the personalized link, open a prefilled email draft, or select **Email an invitation now** when automatic delivery is configured. Reader email addresses are not placed in URLs. Links contain an opaque access token and reveal only that reader's settings, not the reader roster.
4. Publish a story. Shared storage is the default; **Email readers about material changes** is checked by default. Unchanged saves and photo-description/tag-only corrections do not generate another update. New stories and meaningful text/media changes notify eligible readers whose catch-up period includes the story.
5. Replies to delivered invitations and updates go to the author. The page has no separate reply action. **Personalize** contains the notification email field and update opt-out; every email links to this dropdown. Changing an invited reader’s address sends a confirmation to the new address before delivery switches. Without sending credentials, the change remains pending and the original address stays active.

The initial collection contains the fictional demo stories. Authors can edit/delete these or restore their own story backup. **Import earlier browser stories**, when available in Studio, migrates the previous browser-only collection into shared storage without sending update emails. Restoring a backup similarly does not send notifications.

## Email delivery

The sending adapter uses the [Resend email API](https://resend.com/docs/api-reference/emails/send-email). Set these server-side environment variables (names are also listed in `web/.env.example`):

- `RESEND_API_KEY`: delivery credential.
- `MAIL_FROM`: sender on a verified sending domain, for example `Been2long <hello@your-domain.example>`.
- `PUBLIC_URL`: the reader-visible base URL used in links.
- `AUTHOR_SETUP_KEY`: required when first configuring an author on hosted Sites.
- `BEEN2LONG_DATA_FILE`: optional local JSON data path.

The Node server reads process environment variables; it does not automatically load `.env`. Configure hosted values through Sites environment settings. Keep credentials out of source and browser code.

Without email credentials, publishing still works and notifications remain queued. Invitations can be shared manually or composed in the author's mail app. Studio shows waiting/failed delivery and offers a retry. Provider acceptance is recorded with a message ID; it is not claimed to guarantee inbox delivery. Notifications have durable deduplication IDs and provider idempotency keys; failed/abandoned sends can be explicitly retried within the provider's idempotency window. Older uncertain sends are not blindly resent. Delivery is triggered by publishing, sending an invitation, or retrying from Studio; there is no background scheduler.

## Reading experience

- One opening overview: “Jamie — here's the short version,” with a high-contrast **Explore the timeline** promo at the upper right. The header holds the author/photo, an inline date-range dropdown, **Personalize**, Share, and Studio. Personalize includes compact topic checkboxes, font and size choices, and notification email settings. Narrow layouts use the b2 mark; there is no left sidebar.
- Topic checkboxes combine with OR semantics. **All topics** selects or clears all choices and shows a partial-selection state. All topics are selected initially; clearing every choice shows no matching stories. Category filters and sort remain independent.
- **Photos only** switches to a gallery of all photos, including multiple photos in one story. **Back to stories** restores the story layout. The old control merely excluded text-only stories, which made no visible change in the all-photo demo.
- Cards vary in width, image proportions, and text placement. Photo zoom follows scroll; text lifts once on entry. Reduced-motion settings disable these effects. Text-only and large-type layouts have explicit fallbacks.
- Three font styles and four text sizes are inline within Personalize and persist on the device. **Share → Copy link** is the first sharing option; native sharing is secondary. Copied links preserve the explicit start/end dates, selected topics, relevance preferences, sort, category, photo mode, font, text size, and optional story. General author share links use an opaque read-only token; reader-specific links preserve their reader token. Email addresses and confirmation tokens are excluded from copied view links.

## Life threads

Stories still have one date. A story can belong to several threads, such as “Parenthood” and “Film photography.” Categories organize threads. Photo stories lead the main page; a compact **Explore the timeline** invitation inside the opening overview opens the full timeline in a modal. The modal supports Escape, a close button, and clicking the backdrop. Its entrance animation respects reduced-motion preferences. Choosing a thread closes it and filters the main story grid; choosing a moment opens that story. The desktop timeline shows parallel rows with dated story points, an inferred earliest/latest-story span, and an arrow to today for ongoing threads. The selected catch-up period is highlighted. Ongoing threads stay visible even when they have no new stories in that period. Dense points open a dated story chooser.

On mobile/tablet, each thread has an expandable vertical list of dated stories; there is no horizontal timeline scrolling. Selecting a thread filters the story cards below. **Whole thread** includes earlier/later context without changing the catch-up dates. Opening a timeline point follows the thread chronologically. Story details and cards link to their other threads. Shared links preserve thread selection and whole-thread context alongside the existing view settings.

In **Studio → Manage threads**, create, rename, categorize, and mark threads ongoing or finished. In the story editor, choose one or more threads or create a new one inline. No start/end dates are required on stories or threads. Empty threads appear in Studio and enter the reader timeline when their first story is added. A thread cannot be deleted while it is a story’s only membership; move that story first. Deleting a removable thread preserves its stories.

Older saved collections are migrated once, preserving all text, images, and dates. The fictional demo gets curated thread memberships; other unassigned stories get a category thread that the author can refine. Backups now include thread definitions and story memberships; old backups without threads remain importable. The legacy `/api/chapters` route and `chapters` storage/response key remain for compatibility. Thread-only edits and classification changes do not generate notification emails.

## Linked photos

A story can link directly to a named photo album without scraping or importing its photos. Album-only stories appear as album tiles in the gallery. Google Photos/Immich source links from imported photos are deduplicated into album links in cards and story details. Use **Studio → Import photos** or import within a story. Google Photos public share pages are parsed without executing scripts; metadata exposed by the page includes available URLs, dates, dimensions, and album title. This is best-effort scraping: Google can change markup or require sign-in, large albums may be incomplete, and image URLs may expire. A saved HTML-page fallback is available. Private EXIF is not recovered.

Immich public `/share/<key>` links and custom slugs use its shared-link/album APIs. Capture dates, descriptions, and locations depend on the share's metadata settings. Password-protected shares are unsupported. The local fetcher rejects private/LAN targets and nonstandard HTTPS ports.

Photos stay at their original host. Up to 200 linked photos per story can be previewed, selected, described, and reordered for cover choice. Original source links can give the reader access to the entire shared album, not just the selected images. Published stories accept linked photos, not uploaded image binaries; this keeps storage and backup records focused on text and metadata.

## Hosted runtime

The source manifest now targets a Cloudflare Worker with a `DB` D1 binding. `npm run build` embeds the existing editable `web/dist` files/assets and shared backend into `web/dist/server/index.js`. It does not replace the editable frontend files. The schema-only migration and migration journal live in `web/drizzle`; tables are never created from request handlers.

The D1 store uses revision-checked writes; the local store serializes writes and atomically replaces its private data file. The current single-collection implementation caps state at 1 MB and is intended for a small personal catch-up, not a large multi-author service.

The existing Site's hosting audience is preserved. A reader link authorizes application content but does **not** grant access through a private Sites hosting gate. Before emailing hosted invitations, the hosting audience must allow those readers. Changing the audience is a separate hosting action; published content still requires a reader token or the author identity.

The Worker build is prepared locally. It has not been deployed: this workspace cannot resolve the Sites source Git host. Email credentials are also not configured. Browser rendering/animation QA remains unverified because the workspace blocked both the preview server and Chromium.

## Verification and layout

The tests cover durable storage across fresh sessions, author authorization, hosted identity, recipient parsing, invitations, notification deduplication/opt-out, competing D1 writes, Google/Immich parsing, and reader-view gallery/topic interactions. Email delivery is mocked; the lightweight reader-view smoke tests do not render CSS or replace browser testing.

- `web/dist/index.html`, `styles.css`, `app.js`: editable frontend source.
- `web/dist/data.js`: fictional profiles, stories, media credits.
- `web/dist/photo-import.js`: shared metadata parsing and validation.
- `web/dist/view-state.js`: complete view-link serialization and album-link grouping.
- `web/dist/threads.js`: thread migration, spans, timeline filtering, and point clustering.
- `web/backend.js`: shared publishing/readers/email API.
- `web/file-store.mjs`: durable local storage.
- `web/photo-service.mjs`: Node metadata fetcher with address/redirect/body checks.
- `web/server.mjs`: local HTTP server and API adapter.
- `web/worker/`: hosted runtime, metadata fetcher, D1 adapter.
- `web/scripts/build-worker.mjs`: dependency-free Worker bundler.
- `web/tests/`: backend, Worker, import, and reader-interaction checks.

All seed characters and events are invented. Stock images illustrate the fictional stories; external videos belong to their credited creators. Individual photo sources are recorded in `web/dist/assets/credits.json`.
