# RTG QA Service Agreement Builder

Single-page form that builds the QA service agreement Raising the Game (Youth Options, Adelaide) signs with game studios. Fill the form on the left, the agreement renders live on the right, download as Word or print to PDF.

Owner: Iain Dalrymple, Coordinator – QA for Gaming. He reviews everything before it goes to a studio.

## Files

- `index.html` — the whole app. Markup, styles and script in one file. No build step, no dependencies.
- `netlify.toml` — publishes the repo root as-is.

## Deploy

Push to `main`. Netlify auto-deploys from this repo.
Status: Iain is linking his existing Netlify site to this repo (Site configuration → Build & deploy → Continuous deployment → Link repository). Confirm with him it's done before assuming pushes go live.

## How the agreement works

Two service tiers, one set of terms. The tier switch changes the document, never a separate template.

- **Active** — committed testing days per month, weekly cycle, cycle brief due COB Tuesday, initial term then rolling monthly, 14 days notice. Default $300/month.
- **Coverage** — testing as floor capacity allows, no committed days, cycle brief optional, findings shared as they arise, rolling monthly from day one, 7 days notice. Default $150/month.

Tier-dependent parts: clause 2 (services), clause 3 intro and cycle brief, clause 3 capacity vs 3A coverage, clause 5 term line, clause 7 pause (initial term line is Active only), clause 10 notice, clause 11 duration, Schedule A rows.

Switching tier swaps the fee and reporting fields to that tier's default, but only if the field is empty or still holds the other tier's default. Don't overwrite what Iain typed.

Billing timing (arrears/advance) changes clause 5, clause 10 and the Schedule A fee row. Default is arrears.

## Locked values (not form fields, by Iain's decision)

In the `FIXED` object:
- QA Lead contact: Iain Dalrymple, Coordinator – QA for Gaming, iain.d@youthoptions.org.au, (08) 8166 3852
- Testing floor days: Wednesday, Thursday, Friday
- Cycle brief due: Close of business Tuesday
- Platform is always PC (appended to the game title in Schedule A)
- Capacity review: ongoing, QA Lead updates the studio weekly on team availability. Changes to committed days still need written agreement with 14 days notice.
- Youth Options signature: Iain Dalrymple, Program Coordinator & QA Lead

## Settled decisions — don't reintroduce

- Commitment is **testing days, not tester hours**. A testing day = a scheduled floor day with at least one assigned tester on the studio's game. Hours caused the original Kyoti dispute.
- No build version or platform fields.
- No "Continuation beyond the initial period" bullet in clause 5 — it contradicted the rolling term in clause 11.
- No disclaimer about not retesting old bugs — regression checking is part of RTG's process.
- No "Studio may describe the QA service at its own discretion" line in clause 8.
- No onboarding-hours cap paragraph in clause 4.
- Build readiness has two requirements only: installs and launches without help; comes with notes on what changed and/or what to test.
- Fees are not apportioned on exit.
- Escalation: concerns raised with other Youth Options staff reach the QA Lead within 2 working days. Keep it.

## Gotchas

- Never put a literal `</style>` or `<style>` inside a JS string in the page. The HTML parser ends the script early and dumps code onto the page. The Word export splits them (`'<' + 'style>'`). Same rule for `</script>`.
- `lcFirst()` lowercases only the first letter when a value sits mid-sentence. Don't use `.toLowerCase()` on whole values — it turned "Tuesday" into "tuesday".
- Download is `.doc` (Word-readable HTML), not true `.docx`. Iain opens it in Word and tidies spacing by hand, sometimes adding blank lines so the signature block doesn't split across pages.
- Print styles hide the header and form so only the agreement prints.
- "Clear form" resets studio-specific fields, keeps locked values, resets to Coverage, and asks for confirmation first.

## Testing

No test suite. Check by rendering headlessly with jsdom:

```bash
npm i jsdom
node -e "
const {JSDOM}=require('jsdom');const fs=require('fs');
const dom=new JSDOM(fs.readFileSync('index.html','utf8'),{runScripts:'dangerously'});
const w=dom.window,d=w.document;
w.setTier('Active'); console.log(d.getElementById('preview').textContent.slice(0,400));
"
```

Also run `node --check` on the extracted script block after any edit. Check both tiers every time — most past bugs were one tier contradicting itself.

## Writing style

Iain wants plain, direct language: short sentences, no padding. Same for any contract wording you change. Ask before changing what a clause commits Youth Options to; small wording and bug fixes don't need asking.
