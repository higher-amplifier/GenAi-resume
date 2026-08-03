# GenAI Resume Parser

I built this because I was tired of blindly tailoring my resume for every job posting with no real idea if it actually matched what the role wanted. So I made a tool that tells me straight up — here's your match score, here's what you're missing, and here's a plan to fix it before the interview.

It's a full-stack app (React + Express + MongoDB) that uses Google's Gemini LLM to parse a resume against a job description, score the match, generate an interview prep plan, and render a tailored resume as a PDF.

---

## What it does

- Takes a job description + your resume (or just a quick self-description) and gives you a match score with reasoning
- Generates a full interview prep plan — technical questions, behavioral questions, and a day-by-day roadmap — based on the gaps between your profile and the role
- Renders a tailored resume as a PDF using headless Chromium
- Validates every AI response against a schema before it hits the frontend

### Input screen

![Create Your Custom Interview Plan UI](./interview-plan-ui.png)

You paste the JD on the left, and either upload a resume or describe yourself on the right.

### What you get back

![Technical Questions with Match Score and Skill Gaps](./technical-questions-ui.png)

Technical questions generated off my profile and the JD, plus a match score and skill gap breakdown on the right.

![Behavioral Questions with Intention and Model Answer](./behavioral-questions-ui.png)

For behavioral questions, each one comes with an "Intention" (why they're likely asking it) and a model answer built around my actual projects.

![5-Day Preparation Road Map](./roadmap-ui.png)

And a 5-day roadmap that sequences what to study based on the gaps it found.

---

## Stack

- **Frontend:** React
- **Backend:** Express / Node.js
- **DB:** MongoDB
- **AI:** Google Gemini
- **PDF rendering:** Puppeteer (headless Chromium)
- **Auth:** JWT + token blacklist for logout/revocation
- **Validation:** Zod + zod-to-json-schema

## How it works

LLMs are probabilistic — ask the same thing twice and you can get differently shaped responses. That's an issue when my frontend needs a consistent JSON structure to render into UI.

So every prompt to Gemini is paired with a Zod schema, converted through zod-to-json-schema, and passed in as a structured output constraint. Gemini's JSON mode is forced to return data matching that shape. Basically I turned a chat model into something that behaves like a predictable, typed API.

Worth being clear about: I'm not training or fine-tuning any models here. This is an application built on top of a foundation model — the engineering is in the prompting, the schema constraints, and making an inherently unpredictable API production-usable.

---

## Why these choices

- **MongoDB over a relational DB** — resume and JD data is naturally document-shaped, didn't need rigid joins
- **JWT + blacklist instead of sessions** — stateless auth but still supports real logout/revocation
- **Puppeteer over a PDF library like PDFKit** — more overhead, but pixel-perfect, real-CSS resume rendering
- **Gemini model tier** — picked with cost/latency/quality tradeoffs in mind for structured extraction

---

Built this mainly to understand what it takes to ship a GenAI feature end-to-end — not just call an API, but design around how it actually behaves.
