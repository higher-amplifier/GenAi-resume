# GenAI Resume Parser 🎯

Ever spent hours tailoring your resume for a job, only to guess whether it'll actually clear the ATS or catch a recruiter's eye? Yeah, me too. That's why I built this.

**GenAI Resume Parser** is a full-stack app that takes your resume and a job description, and tells you — honestly — how well you match up. Then it goes a step further: it builds you an interview prep plan and can spit out a tailored, recruiter-ready resume PDF, all powered by Google's Gemini LLM.

No black-box scoring. No vague "72% match!" with zero explanation. Just a genuinely useful breakdown of what's working, what's missing, and how to close the gap.

---

## What it actually does

- **Match scoring** — paste a job description and your resume (or a quick self-description), and get a real score with reasoning behind it
- **Interview strategy generation** — a custom prep plan built from the actual overlap (and gaps) between your profile and the role
- **Tailored resume PDFs** — rendered server-side via headless Chromium, so what you download actually looks like a resume, not a text dump
- **Structured, reliable AI output** — the LLM doesn't just "chat" back at you; every response is validated against a schema before it ever reaches the frontend

### What it looks like

![Create Your Custom Interview Plan UI](./interview-plan-ui.png)

This is the entry point — drop in the job description, then either upload your resume or just describe your background in plain English. The app takes it from there.

### The output — where it gets useful

Once the AI does its thing, you land on a full breakdown: a match score, the specific skill gaps holding you back, and a prep plan built around them.

![Technical Questions with Match Score and Skill Gaps](./technical-questions-ui.png)

Every question is generated against your actual profile — not generic "tell me about yourself" filler. Notice the **Match Score (68%)** and **Skill Gaps** panel on the right, calling out exactly where the JD and your background diverge.

![Behavioral Questions with Intention and Model Answer](./behavioral-questions-ui.png)

This is the part I'm most proud of. Each behavioral question comes with an **Intention** (why the interviewer is likely asking this) and a **Model Answer** grounded in your real experience — not a canned STAR template, but one that references your actual projects and internships.

![5-Day Preparation Road Map](./roadmap-ui.png)

And finally, a day-by-day prep roadmap that sequences what to study based on the gaps identified — so you're not just told what you're missing, you're told what to do about it, in what order.

---

## Under the hood

**Stack:**
- **Frontend:** React
- **Backend:** Express / Node.js
- **Database:** MongoDB
- **AI:** Google Gemini
- **PDF rendering:** Puppeteer (headless Chromium)
- **Auth:** JWT with a token blacklist for logout/revocation
- **Validation:** Zod + `zod-to-json-schema`

**The interesting engineering bit:**
LLMs are probabilistic by nature — ask the same question twice and you might get two differently-shaped answers. That's a problem when your frontend expects a consistent JSON structure to render.

The fix here: every prompt to Gemini is paired with a **Zod schema**, converted via `zod-to-json-schema`, and passed in as a structured output constraint. Gemini's JSON mode then returns data that *has* to match that shape. In practice, this turns a probabilistic chat model into something that behaves like a predictable, typed API endpoint.

Worth being clear on: **this project doesn't train or fine-tune any models.** It's an application built *on top of* a foundation model, using prompt engineering and structured output constraints to make it production-usable. That distinction — "using GenAI" vs. "building GenAI" — matters a lot when explaining the project to others.

---

## Known rough edges

Nobody's codebase is perfect, and I'd rather list these than pretend they don't exist:

- **IDOR on the PDF generation endpoint** — currently missing an ownership check, so it needs a fix before this touches real user data in production
- **No retry/fallback logic** if the AI response fails schema validation — right now a malformed response just fails instead of retrying or degrading gracefully

Both are on the radar for the next pass.

---

## Why these design choices

- **MongoDB over a relational DB** — resume/job-description data is naturally document-shaped and doesn't need rigid joins, so schema flexibility won out
- **JWT + blacklist over sessions** — stateless auth that still supports proper logout/revocation
- **Puppeteer over PDF libraries** (like PDFKit) — trades some overhead for pixel-perfect, real-CSS resume rendering instead of fighting a limited PDF DSL
- **Gemini model tier** — picked with an eye toward the cost/latency/quality tradeoff for structured extraction tasks specifically, rather than defaulting to the biggest model available

---

## Roadmap

- [ ] Fix the IDOR vulnerability on PDF generation
- [ ] Add retry/validation fallback for AI response failures
- [ ] Expand test coverage around schema validation edge cases
- [ ] Consider rate-limiting on the Gemini calls to control cost at scale

---

Built as a hands-on way to actually understand what it takes to ship a GenAI feature end-to-end — not just call an API, but design around its quirks.
