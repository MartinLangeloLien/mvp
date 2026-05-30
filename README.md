# Volt — Phase 1 Registration (Clickable Prototype)

A self-contained, clickable demo of Volt's **Phase 1 — Campaign Audit & Brand
Validation** flow. Built to validate the state-machine logic from the v3.1 spec, not
to be production code.

**Live:** https://martinlangelolien.github.io/mvp/

It's a single `index.html` (no build step, no backend). All logic runs in the browser.

---

## The flow at a glance

A **Registration** moves through 8 states. The campaign and the brand are two parts of
the *same* registration — there's no separate "brands" vs "campaigns" object.

| Step | State | Who acts | What happens |
|------|-------|----------|--------------|
| 1 | Campaign details (S1) | Customer | Pick channel type, fill campaign fields, submit |
| 2 | Campaign audit (S2) | System | Automated (and sometimes human) review — always advisory, never a hard pass/fail |
| 3 | Review audit results (S3) | Customer | Address findings, waive them, or proceed; pick CNP/DCA |
| 4 | Brand information (S4) | Customer | Company details + live EIN verification |
| 5 | Brand verification (S5) | System | Binary result: **Verified** or **Changes Required** |
| 6 | Review brand results (S6) | Customer | Fix & resubmit, or proceed as-is |
| 7 | Registry submission (S7) | System | Auto-submits to TCR (10DLC) / carrier (TFN) / CSCA (Short Code) |
| ✓ | Registered (P2) | — | Phase 1 complete; Phase 2 runs carrier-side |
| ✕ | Closed (S8) | Customer/System | Terminal; retained read-only |

The customer can close a registration from any step. Every transition fires the
webhook named in the spec — watch the **Events** panel on the right while you click.

---

## Reviewing it without setup

Two affordances make review fast and repeatable (both are clearly demo-only, styled in
purple):

- **⚡ Fill test data** — top-right of the campaign and brand forms. Autofills every
  field with valid sample values (and auto-verifies the EIN) so you can move quickly.
- **Demo Controls** — collapsible panel, bottom-left, inside a registration. Forces the
  outcome of each system step so you can walk a specific path on demand:
  - **Campaign audit:** Auto-approve · Human review · Random
  - **Brand verification:** Pass · Changes required · Random
  - **Registry submission:** Success · Retry → ok · Random

Suggested paths to try:
1. **Happy path:** New Registration → Fill test data → leave Demo Controls at
   Auto / Pass / Success → reach Registered.
2. **Full review path:** set Human review / Changes required / Retry → you'll see the
   human-review wait, the brand-feedback step (S6), and the registry retry.
3. **Resume:** from the dashboard, click any **Action Required** row (e.g. *TechStart —
   Review audit feedback*) — it opens at that exact step with realistic data.

The dashboard shows 13 sample registrations spanning all three channel types and every
status. **Reset demo data** (in the banner) restores the clean sample set at any time.

---

## What's simulated

This is a prototype, so a few things are faked client-side:

- **EIN verification** — any EIN validates *except* ones starting with `00` (use that to
  see the error path). No real IRS call.
- **Campaign audit & brand verification** — findings are generated from your input;
  outcomes are driven by Demo Controls (or randomized).
- **Registry submission** — no real TCR/CSCA call; success/retry is simulated.
- **Persistence** — drafts and completed registrations are saved to your browser's
  `localStorage` only (so they survive refresh). Nothing leaves your machine.

---

## Known simplifications vs the v3.1 spec

Faithfully implemented: all 8 states and transitions, the v3 "no pass/fail audit gate"
behavior, binary brand validation, per-item feedback disposition tracking, channel→
registry routing, the close-from-any-state path, and every webhook name/payload.

Intentionally simplified for the demo:

- The CNP/DCA suggestion is keyed on channel type (the spec also factors in use case,
  industry, and volume); the rationale text references those factors.
- Screenshot auto-capture on opt-in URL is not implemented.
- Phase 2 / Phase 3 and their back-transitions into S1 are out of scope (Phase 1 only).
