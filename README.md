# Sovereign Provenance Architecture (SPA)

A consortium specification for inspectable, deterministic, provenance-bearing AI governance. Living document. Stewarded by Ethereal Connections Co.

**Canonical:** [spa.etherealconnectionsco.com](https://spa.etherealconnectionsco.com)
**License:** [CC BY-SA 4.0](LICENSE)
**Current version:** v0.3 (revised 13 May 2026)
**Status:** Proposed · Open to Signatory

---

## What this is

The SPA is a minimum specification that allows independently-operating firms — each with its own commercial brand and methodology — to converge on shared truths about how AI systems must be governed at formation, custody, and execution.

It is not a brand. It is not a product. It is not a platform. It does not certify, license, or charge. It defines what *conformant implementation* means and leaves the implementation sovereign.

For the full specification, open `index.html`.

---

## Repository structure

```
.
├── index.html       — the SPA specification (current version)
├── seal.jpg         — ECCO seal (256×256 JPG, referenced from index.html)
├── 404.html         — custom 404 matching SPA aesthetic
├── netlify.toml     — deploy config (headers, caching, redirects)
├── check-links.mjs  — link integrity check (supports SPA §9 claim)
├── LICENSE          — CC BY-SA 4.0
├── .gitignore       — standard exclusions
└── README.md        — this file
```

---

## Deploy

This site is a static deploy. No build step.

### Netlify (recommended path)

1. New site → Import from Git → connect this repo.
2. Build settings: leave empty. Publish directory: `.` (root).
3. `netlify.toml` provides security headers, caching, and the `/spa` → `/` redirect (harmless on subdomain deploy; active if the site is also reachable at apex `/spa`).
4. Configure domain: `spa.etherealconnectionsco.com` (subdomain) is the canonical form for v0.3. Apex path-form `etherealconnectionsco.com/spa` redirects to the subdomain at the apex site's config layer.

### Local preview

```bash
# Any static server works:
python3 -m http.server 8000
# or:
npx serve .
```

Then open `http://localhost:8000/`.

---

## Versioning convention

Per SPA §8 (Living Document Protocol):

- **Additive changes** (vocabulary additions, clarifications, examples, registry growth) increment the minor version: v0.1 → v0.2.
- **Breaking changes** (modifications to Tenets §2, Required Layers §3, or Conformance Criteria §4) increment the major version: v0.x → v1.0.
- **Major revisions** require a 30-day public review period and notice to all currently listed Conformant signatories.
- **All previous versions** remain accessible at versioned URLs. The canonical URL always points to the current version.

Suggested versioning workflow for this repo:

```
/index.html              → current version (canonical, evergreen)
/v0.1/index.html         → v0.1 archive snapshot
/v0.2/index.html         → v0.2 archive snapshot
/v0.3/index.html         → v0.3 archive snapshot
/v0.4/index.html         → next minor, when shipped
```

When shipping a new version: snapshot the outgoing `index.html` to its versioned folder before overwriting root. Git history is the secondary record; the path-versioned snapshots are the canonical accessible-version record per §8.

---

## Integrity check

`check-links.mjs` is a self-contained Node script that supports the §9 *"CI-gated link integrity at issue time"* claim.

```bash
node check-links.mjs
```

It extracts every `href=` and `src=` from `index.html`, resolves local paths against the filesystem, fetches remote URLs (HEAD with GET fallback), and exits non-zero on any failure. Mailto and anchor-only links are passed through.

Run it before every deploy. Run it in CI to make the §9 claim verifiable from outside the firm.

Environment:

- `LINK_CHECK_TIMEOUT_MS` (default 8000) — per-request timeout
- Requires Node ≥ 18 (uses built-in `fetch`)

---

## Editing this document

The SPA is a Living Document under stewardship discipline. Edits follow these rules:

1. **Every revision increments the version per §8.** No silent edits. (Procedure detail in *Version-bump discipline* below.)
2. **Every revision logs to the inline changelog** in §8 of `index.html`.
3. **Pre-deploy gate:** run `node check-links.mjs` and verify pass.
4. **Vocabulary changes** that would alter §6 require an attribution check against §9 Vocabulary Origin. The §7 declaration discipline applies to the steward's own additions.
5. **§9 changes** that affect listed signatories require steward verification of inspectability per §5 prior to listing.
6. **License changes** are logged in both this repository's `LICENSE` file and the §8 changelog; the in-document license metadata (header row and footer line) is updated in the same revision to remain consistent with the LICENSE file.

### Version-bump discipline

Three formatting variants of the version string appear in `index.html`, and any bump must catch all of them while leaving historical references intact.

| Variant | Where it appears | Grep pattern |
|---|---|---|
| `v0.X` | Title, meta tags, body prose, §4–§6 normative refs, §9 ref-status | `v0\.[0-9]` |
| `v 0.X` (with space) | Header `.v-num` element, footer version line | `v 0\.[0-9]` |
| `v0.X → v0.Y` | §8 changelog entries, §8.1 rule example | PROTECTED — see below |

**The trap.** A global find/replace of `v0.X` corrupts the §8 changelog's historical entries and the §8.1 rule example. Both contain version-pair strings that must remain as written:

- The §8.1 rule example reads `v0.1 → v0.2` as an illustration of the minor-bump pattern. It is canonical, not topical, and does not advance with each version.
- Each prior changelog entry references its own outgoing → incoming version pair as historical record. A v0.1 → v0.2 entry stays as `v0.1 → v0.2` forever.

A naive find/replace in an editor will not respect these. Two safe approaches:

- **Surgical:** edit each non-protected line individually, leaving lines containing `<strong>v0.A → v0.B</strong>` and the §8.1 rule line untouched.
- **Scripted:** run the bump as a Python pass that filters out protected lines by content match before applying the swap.

**Recommended procedure for shipping `v0.X → v0.{X+1}`:**

1. `grep -nE "v0\.[0-9]|v 0\.[0-9]"` against `index.html` to enumerate every version reference.
2. Identify protected lines (§8.1 rule example + all existing changelog `<p><strong>v0.A → v0.B</strong>` entries).
3. Apply the version swap to all non-protected lines, including both `v0.X` and `v 0.X` variants.
4. Update the intro paragraph (currently line 692) so its `the v0.A → v0.B changelog` self-reference points to the new version pair.
5. Author the new changelog entry, dated to the revision day, documenting what changed and how it maps to §8.1 (additive) vs §8.2 (breaking). Include the doctrinal reasoning for borderline calls.
6. If license, README, or LICENSE-file metadata also changed, ensure they cite the same version pair and are committed together.
7. Re-run the grep from step 1; confirm only the protected lines retain prior-version pairs.
8. Run `node check-links.mjs` per rule 3 and verify pass before commit.

A future enhancement to `check-links.mjs` could add a version-consistency assertion: parse the canonical version from the `<title>` element and confirm every other non-protected version reference in the document matches it. Open work item; not blocking.

The doctrine is: *the steward holds the steward to the same standard the steward holds the field to.*

---

## Stewardship

Steward: J. W. Hearne · Ethereal Connections Co. · Denver, Colorado.
Contact: `jeremiah@etherealconnectionsco.com`

Stewardship is open to transfer or rotation by community consensus, defined as a written majority of named Conformant signatories agreeing in writing to a successor (per §7).

---

## Doctrine in active service

- *Provenance over performance.*
- *Infrastructure over influence.*
- *Doctrine over excuse.*
- *Inspectability is the credential; the steward verifies before listing.*

---

*The architecture is the architecture. The brands are the brands. The work is the bridge.*
