# Migration handoff — Mintlify → GitHub Pages + Scalar

**Status:** scoped, not started. Nothing in this repo has been changed yet.
**Written:** 2026-08-05
**Maintainer after handover:** _[TO BE FILLED IN — see "Named owner" below]_

---

## Why this exists

Jordan is leaving Sony. The `crsdk.app` domain and site ownership transfer to a
coworker, but **nobody is committed to actively maintaining the docs content**.

That single fact drives every recommendation here, and it inverts some of the
usual advice:

- The fragile dependency is **not the hosting** — it is the **Mintlify account**.
  Billing, domain config, deploy settings, and someone to log in when it breaks.
  Open-sourcing this repo does not fix that.
- **Automatic deploys are a feature here, not a risk.** A human review gate only
  helps if a human is there to review. Gate only the pages where wrong
  instructions can damage hardware; let everything else merge and publish.
- **An unmaintained site that still looks authoritative is worse than no site.**
  This is not hypothetical. The published spec advertised `contents-transfer` as
  a connection mode for months while the server only ever accepted `contents`.
  Every client generated from that spec failed on contents-mode connect.

The goal is a docs site that stays correct and reachable **with no one minding
it**.

---

## Decisions made

### 1. Host on GitHub Pages, at the `github.io` URL

No SaaS account, no billing, nothing to administer. Deploys happen on merge. If
the coworker keeps `crsdk.app`, a `CNAME` points it at Pages — but the docs stay
reachable either way, which is the point.

### 2. Scalar for the API reference and playground

Replaces Mintlify's auto-generated API Reference tab, and adds a working
request console it never had.

### 3. Merge the docs into `alpha-sdk-api`

The spec is currently **duplicated** — `alpha-sdk-api/api/openapi.yaml` and this
repo's `openapi.yaml`. They match today (46 operations each) only because Jordan
has been syncing them by hand. In one repo, an endpoint change updates the
server, the spec and the docs page in the same commit. They *structurally cannot*
drift.

---

## Why a playground works on static hosting

This is the part that sounds impossible and isn't.

```
GitHub Pages  ──serves──►  HTML + JS + openapi.yaml   (public web)
                                    │
                           runs in the reader's browser
                                    │
                           "Send" ──►  http://localhost:8080   (their own machine)
```

The docs host never talks to a camera — **the reader's browser does**. Three
things make it work, and all are already true:

- The server sends `Access-Control-Allow-Origin: *`,
  `Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS` and
  `Access-Control-Allow-Headers: *`
  (`alpha-sdk-api/api/server/src/CameraWebServer.cpp:941-943`), and handles
  `OPTIONS` preflight (same file, `:366`).
- Browsers exempt `http://localhost` from mixed-content blocking, so an HTTPS
  page can call it.
- `docs.json` already sets `"api": {"playground": {"proxy": false}}` — the
  current playground already calls localhost directly. Scalar uses the same
  model.

No proxy, no backend, nothing to host but files. Nobody's camera is exposed.

---

## Current state — inventory

Taken 2026-08-05 at `f28e773`.

| | |
|---|---|
| MDX files | **41** (34 in nav, **7 orphans** not referenced in `docs.json`) |
| Content volume | ~6,900 lines, 182 code blocks |
| `openapi.yaml` | 2,927 lines, OpenAPI 3.1.0, 40 paths / **46 operations** |
| API reference pages on disk | **zero** — Mintlify generates all 46 at build time |
| Mintlify JSX components | 14 types, **~214 opening-tag sites** |
| Assets | 690 KB across 17 files — **only 4 are referenced** |
| Secrets / analytics / search keys | **none** |
| CI | **none** — no `.github/` directory at all |

### What makes this easier than it looks

- **No hand-written API reference pages to port.** `docs.json` declares
  `{ "tab": "API Reference", "openapi": "openapi.yaml" }` and Mintlify
  synthesises all 46 endpoint pages at build. Pointing Scalar at the same file
  reproduces it — and adds the playground.
- **No `<ParamField>`, `<ResponseField>`, `<RequestExample>` or
  `<ResponseExample>` anywhere.** All parameter documentation lives in the spec,
  which Scalar consumes natively. This is the single biggest de-risker.
- **Frontmatter is 4 keys**, two of them universal (`title`, `description`).
  Only `icon` is Mintlify-specific, on 4 pages, 3 of which are orphans.
- **No secrets to re-provision.** No analytics, no search keys, no project
  tokens, no `.env`.

---

## The real work

~214 JSX component sites, so effort is not underestimated:

| Component | Files | Sites | Note |
|---|---|---|---|
| `<CodeGroup>` | 9 | 33 | Hardest — fence info-strings carry tab labels (`` ```bash curl ``) |
| `<Card>` | 10 | 32 | CSS only |
| `<Note>` | 15 | 29 | Callout |
| `<Warning>` | 11 | 28 | Callout |
| `<Step>` / `<Steps>` | 3 | 30 | Ordered-list styling |
| `<Tab>` / `<Tabs>` | 3 | 20 | Needs JS |
| `<Tip>` / `<Info>` / `<Check>` | — | 24 | Callouts |
| `<Accordion>` / `<AccordionGroup>` | 3 | 11 | Maps to `<details>` |
| `<CardGroup>` | 6 | 7 | Grid wrapper |

Concentrated in five files:

- `web-api/actions.mdx` — 12 `<CodeGroup>`
- `MCP-Server/setup.mdx` — 4 `<Tabs>` / 9 `<Tab>`
- `web-api/advanced-topics.mdx` — 14 `<Step>`
- `MCP-Server/privacy.mdx` — 5 `<Accordion>` *(orphan page — may not need porting)*
- `sdk/overview.mdx` — 10 `<Card>`

---

## The first decision the implementer must make

**GitHub Pages' built-in Jekyll cannot render MDX with JSX components.** Those
~214 sites would render as literal text. Something has to handle them. Two
routes:

### Route A — convert MDX to plain Markdown + HTML *(recommended)*

A one-time conversion. Callouts, cards and accordions become HTML/CSS;
`<Tabs>` and `<CodeGroup>` become sequential sections or `<details>`.

Afterwards there is **no node build, no npm dependency churn, nothing to
upgrade**. For a repo nobody is maintaining, that durability is worth more than
the feature it costs.

**Cost:** loses tabbed code groups — the 33 `<CodeGroup>` sites become stacked
code blocks with headings.

### Route B — keep MDX, add a build

An MDX-capable SSG. Astro Starlight has native equivalents for nearly every
component above (`Tabs`/`TabItem`, `Card`/`CardGrid`, `Aside`, `Steps`) plus
built-in Pagefind search, so most of the 214 sites become **renames rather than
rewrites**.

**Cost:** a node toolchain that needs dependency upkeep nobody has committed to
providing. In two years an unmaintained build is likely to be the thing that
breaks.

**Recommendation:** Route A, unless someone commits to owning the upkeep. If
someone does, Route B is meaningfully nicer to read and less work to port.

---

## Gotchas found during inventory

The things that will bite silently if nobody flags them:

- **Search must be rebuilt from nothing.** Mintlify's was free, built-in and
  unconfigured — there is no config to port. Pagefind is the usual static-site
  answer.
- **`global.css` logo rules are written against Mintlify's DOM**
  (`nav img`, `[class*="logo"] img`, `nav a[href="/"] img`, all with
  `!important`). They will silently do nothing after migration. The table rules
  in the same file are portable.
- **`x-fern-ignore` on the two SSE operations** — `/api/events` and
  `/api/cameras/{cameraId}/events` (`openapi.yaml` lines 1205, 1207, 1277, 1279).
  Fern was told to hide them; **Scalar will not**. A "Send" button on a
  never-ending stream hangs. Decide whether to hide them in Scalar or document
  them in prose only.
- **Route preservation.** `llms.txt` hardcodes 11 absolute `https://crsdk.app/...`
  links, including `/api-reference` — the Mintlify-generated route. Put Scalar
  there, or add a redirect.
- **Stale Mintlify references to fix.** The project slug
  `https://sony-88f06855.mintlify.app/` appears in `system-prompt.md` (1×) and
  `.claude/agents/sony-camera-sdk-expert.md` (3×, lines 113, 123, 166). And
  `MCP-Server/overview.mdx:26` states in published prose that the docs are
  "hosted on Mintlify".
- **No `.gitignore` exists** — hence 7 untracked paths in the working tree
  (`.cpa-workflow-artifacts/`, `prompting-context.md`, 6 stray logo files). Add
  one.
- **13 of 17 assets are unreferenced** — leftovers from logo-sizing iteration
  (commits `d1bf2db`, `4f6624d`). Only `images/hero.jpg`, `favicon.png`,
  `logo/light.png` and `logo/dark.png` are wired up. Drop the rest rather than
  porting them.
- **7 orphan pages** need a keep-or-drop call: `MCP-Server/compatibility.mdx`,
  `MCP-Server/privacy.mdx`, `MCP-Server/help-guides.mdx`,
  `MCP-Server/sdk-docs.mdx`, `sdk/camera-remote-sdk.mdx`,
  `sdk/camera-remote-command-sdk.mdx`, and `snippets/snippet-intro.mdx` (the
  last is Mintlify boilerplate, never imported — safe to delete).

---

## The higher-value automation

**This matters more than the hosting move.**

Syncing docs to the spec guarantees the docs match the **spec**. It does *not*
guarantee the spec matches the **server**. The `contents-transfer` bug would have
propagated faithfully to a perfectly synced site.

Two checks were done **by hand** during the session that produced this document,
and each was a few lines of Python. Both belong in CI, where they keep working
with no maintainer:

1. **`ButtonRequest.button` enum** in `openapi.yaml` vs the `kCameraButtons`
   table in `alpha-sdk-api/api/server/src/device/CameraDeviceRest.cpp`
2. **`PropertyName` enum** in `openapi.yaml` vs `PROPERTY_MAP` in
   `alpha-sdk-api/api/server/src/CameraWebController.cpp`

Both are trivially automatable once the docs live in `alpha-sdk-api`, because
spec and code are then in the same checkout.

---

## Suggested sequencing

Picked up in slices rather than as one large change. Step 3 is worth shipping on
its own even if nothing else follows.

1. **Housekeeping.** Add `.gitignore`; delete the 13 dead assets and
   `snippets/`; decide the 7 orphan pages.
2. **Decide the MDX question** (Route A or B above).
3. **Stand up Pages with Scalar** at `/api-reference`, pointed at
   `openapi.yaml`. *This is the highest-value half and works standalone* — the
   API reference and playground are what most readers come for.
4. **Port the prose pages.**
5. **Move the tree into `alpha-sdk-api`** and delete the duplicate
   `openapi.yaml`.
6. **Add the spec-vs-code CI checks** described above.
7. **Fix the stale Mintlify references** and the `MCP-Server/overview.mdx` copy.

### Vendor the Scalar assets, don't use a CDN

Most quickstarts hand you `<script src="https://cdn.jsdelivr.net/npm/@scalar/api-reference">`.
That makes a third-party CDN a runtime dependency of a Sony-branded docs site,
with nobody watching if it breaks. Scalar publishes prebuilt `dist` bundles —
vendor them so Pages serves every byte.

This mirrors what `alpha-sdk-api` already does deliberately: jsoncpp is vendored
and OpenSSL was removed so end users need zero runtime dependencies. Same
reasoning applies here, and more strongly.

---

## Named owner

> **Maintainer:** _[NAME]_
> **Deploy path:** _[how a merge reaches the published site; who can roll it back]_
> **Domain / DNS:** _[where `crsdk.app` is registered and who administers it]_

**This is the most important section of this document and it is currently
blank.**

An open repo with a named, active maintainer is better than a private one. An
open repo with unanswered PRs sitting for months is *worse* than private — it
signals abandonment on pages developers are trusting for correctness.

Fill this in before the handover completes. Everything else here is reversible;
a handover with no named owner is not.
