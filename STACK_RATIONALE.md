# Three Roads: Choosing My Stack with AI — Architecture Rationale

**Author:** Shikhar Singh  
**Track:** General AI Fluency (Week 4 Build Phase)  
**Deliverable:** Written Stack Rationale & Pressure-Test  

---

## 1. Context & Constraints

To make an informed architectural decision for my AI & Machine Learning portfolio, I evaluated my options against four non-negotiable constraints:

1. **Free Only:** Zero upfront or recurring cloud hosting costs.
2. **Honest Skill Level:** Strong background in Python, Machine Learning, and data analysis; comfortable with Git, HTML5, CSS3, and client-side JavaScript. Limited interest in spending hours debugging backend server state, CI build pipelines, or container orchestrations for a personal portfolio.
3. **What the Portfolio Must Do:** Present my technical identity, display deep ML case studies (such as the FlyRank Search Ranking & Discoverability Capstone), showcase interactive data visualizations (Chart.js / metrics plots), link to GitHub repos, and provide contact channels.
4. **Does it Need a Backend?** **Not yet.** All model predictions, metrics, and simulators can run client-side using precomputed JSON payloads and lightweight JavaScript calculators. Introducing a live server right now would introduce unnecessary latency, maintenance burden, and cold-start delays without adding any user value.

---

## 2. The Three Roads Evaluated (Simplest to Most Powerful)

### Road 1: Static HTML5 + Vanilla CSS3 + Modern JavaScript (The Chosen Front-Runner)
* **How I'd Build:** Handcrafted semantic HTML5, modular CSS with CSS variables (supporting dark/light theme switching and responsive grids), and vanilla JavaScript for interactive UI elements (Chart.js integrations, dynamic filtering, and score simulators).
* **Where I'd Host:** **GitHub Pages** (Free, zero-config, native git integration).
* **Needs a Backend?** No. Purely static files distributed over Fastly CDN.
* **The Real Trade-Off:** Less abstraction. If the site grows to 50+ blog articles, the lack of a templating engine (like Markdown-to-HTML rendering) will require manual copy-pasting or a small build script.

---

### Road 2: Static Site Generator — Astro / Vite / Next.js Static Export (The Middle Ground)
* **How I'd Build:** Component-driven architecture using Astro or Vite, writing case studies in `.mdx` or Markdown files with automated routing.
* **Where I'd Host:** **Vercel** or **Cloudflare Pages** (Free tier).
* **Needs a Backend?** No. Pre-renders pages into static HTML/CSS at build time.
* **The Real Trade-Off:** Introduces a Node.js dependency graph (`node_modules`, lockfiles, build scripts). While writing in Markdown is convenient, debugging dependency breaking changes or framework upgrades creates operational overhead that distracts from the core content.

---

### Road 3: Full-Stack Web App — Next.js App Router + FastAPI Backend (The Most Powerful)
* **How I'd Build:** React Server Components frontend coupled with a Python FastAPI microservice deployed on Render/Railway to run live Python ML inference.
* **Where I'd Host:** **Vercel** (Frontend) + **Render / Hugging Face Spaces** (Free tier backend).
* **Needs a Backend?** Yes. Requires an active web server process and REST API endpoints.
* **The Real Trade-Off:** High maintenance complexity. Free-tier backend instances sleep after 15 minutes of inactivity (causing 30-50 second cold-start delays for recruiters). Managing CORS, environment secrets, and backend uptime is overkill when the model outputs can be exported to static JSON once during training.

---

## 3. Pressure-Testing the Front-Runner (Road 1)

* **What breaks if I pick Road 1 (Simplest)?**  
  Nothing breaks for the requirements of this portfolio. Semantic HTML and vanilla JS handle interactive charts, responsive dark mode, and dynamic filtering with zero latency and 100/100 Google Lighthouse performance scores.
* **What do I maintain if I pick Road 3 (Most Powerful)?**  
  I would have to maintain API health checks, manage cold starts, update server packages, monitor free tier memory caps, and troubleshoot CORS headers.
* **Can I finish in two weeks?**  
  **Yes, comfortably.** Road 1 eliminates build-step failures and framework bloat. 100% of my time in Week 5 can be spent on high-impact case study copy, interactive charts, and polished visual styling.
* **Does it show my work well?**  
  **Exceptionally well.** It allows direct control over every CSS animation, glassmorphic card, and Chart.js canvas without fighting framework opinionated styling or hydration mismatches.

---

## 4. Final Decision & Written Rationale

> **Chosen Stack:** **Road 1 — Semantic HTML5, Vanilla Modern CSS, and Client-Side JavaScript hosted on GitHub Pages.**

### Why I chose Road 1 and rejected Roads 2 & 3:
I rejected **Road 3 (Full-Stack)** because my portfolio does not require a live database or user authentication yet; forcing a backend would introduce free-tier cold starts that hurt the recruiter experience. I rejected **Road 2 (Static Site Generators)** because, while component libraries and Markdown imports are nice, managing Node packages and build pipelines adds unnecessary friction when my goal is to ship quickly.

### Can I maintain this?
**Yes, effortlessly.** Road 1 has zero dependencies and zero build steps. If I open this repository 3 years from now, it will still build and render perfectly with no deprecated `npm` packages, security alerts, or breaking API changes.

### Does it show my work well?
Yes. It delivers instant load speeds, responsive mobile presentation, and seamless integration with Chart.js to highlight real data, model benchmarks, and interactive decision tools.
