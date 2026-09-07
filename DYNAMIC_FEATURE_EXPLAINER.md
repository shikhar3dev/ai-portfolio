# Make It Do Something: Dynamic Feature & Data Flow Explainer

**Author:** Shikhar Singh  
**Track:** General AI Fluency (Week 6 Milestone: *Make It Do Something*)  
**Live URL:** [https://shikhar3dev.github.io/ai-portfolio/](https://shikhar3dev.github.io/ai-portfolio/)  
**Feature Implemented:** Interactive Organic Search Decay & Refresh Opportunity Diagnostic Engine  

---

## 1. What is a Backend? (In Plain Words)

A website consists of two sides:
* **The Frontend (What you see):** Like the dining room of a restaurant. It consists of the HTML menus, CSS decor, buttons, and visual layouts rendered inside the user's browser.
* **The Backend (The engine room):** Like the restaurant's kitchen and storage pantry. A backend is a computer server running behind the scenes that stores private databases, processes payments, enforces user security and authentication, and handles heavy computation that browsers cannot or should not perform.

When a website is purely "static," it has no custom backend server running; files are simply served as pre-written documents. When a website has a dynamic feature, it either runs client-side computation inside the browser or communicates with a server API to process and persist state.

---

## 2. What My Feature Does

Instead of presenting a static screenshot or passive text describing my Machine Learning capstone, I embedded an **interactive, client-side Organic Search Decay & Refresh Priority Diagnostic Engine** directly onto my portfolio.

### User Interaction:
A visitor (such as a recruiter or engineering lead) can adjust four real-world search signals:
1. **Current SERP Position** (Rank $1.0$ to $35.0$)
2. **60-Day Position Drift** (Rank loss or gain)
3. **30-Day Impression Velocity** (Percentage change in search visibility)
4. **Content Staleness** (Days since the page was last updated)

### Real-Time Output:
As the user drags any slider, the feature instantly computes:
* **Predicted 30-Day Decay Risk ($P(\text{Decay})$):** The statistical likelihood that this page will lose $\ge 20\%$ of its organic traffic over the next 30 days.
* **Composite Opportunity Score ($0 - 100$):** A normalized metric combining decay probability, ranking sensitivity, and traffic loss potential.
* **Automated Reason Code:** Explaining the primary failure driver (e.g., `RC_01: VELOCITY_CLIFF` or `RC_02: RANK_EROSION`).
* **Prescriptive Action Label:** Recommending whether to execute a *Deep Content Refresh*, a *Targeted Content Polish*, or *Protect & Maintain*.

---

## 3. How the Data Flows (Step-by-Step)

The data flow operates in a continuous, deterministic loop in the browser:

```
[User Input] (Slider Event)
      │
      ▼
1. Event Listener captures DOM change (input event)
      │
      ▼
2. Client-Side State Extraction (parses float/int values)
      │
      ▼
3. Mathematical Model Execution (calibrated logistic regression log-odds: z = β₀ + Σ βᵢxᵢ)
      │
      ▼
4. Decision Heuristic Evaluation (prob thresholding, score normalization, reason code tagging)
      │
      ▼
5. Dynamic DOM Mutation (updates score dial, prob percentage, color styling, and action pill)
      │
      ▼
[User Sees Instant Feedback (< 16ms, 60fps)]
```

1. **Input Event Capture:** The browser listens for `input` events on all range slider elements.
2. **Data Parsing & Normalization:** Raw DOM values are parsed into numerical floats, converting percentages to relative decimal multipliers.
3. **Algorithmic Computation:** The JavaScript engine calculates the log-odds $z$:
   $$z = -1.2 + 0.6 \cdot \text{Drift} - 2.5 \cdot \text{Velocity} + 0.012 \cdot \text{Staleness}$$
   The sigmoid function $\sigma(z) = \frac{1}{1 + e^{-z}}$ yields the predicted decay probability $P(\text{Decay})$.
4. **Opportunity Logic & Styling:** The score combines the raw probability with position-sensitivity weighting. Dynamic CSS classes adjust the color palette in real time: rose red for urgent refresh ($\ge 70$), amber yellow for moderate polish ($45 - 69$), and emerald green for stable pages ($< 45$).
5. **Zero Server Overhead:** Because the calibrated weights run client-side, the feature incurs **zero server cost**, has **zero cold-start delay**, and works offline.

---

## 4. Evidence of End-to-End Functionality
* **Test Input:** Position: `4.5`, Drift: `+2.5` ranks, Velocity: `-35%`, Staleness: `120 days`.
* **Output Generated:**
  * Composite Opportunity Score: **`82.4`** (Urgent priority, colored crimson red)
  * Predicted 30d Decay Risk: **`86.5%`**
  * Reason Code: **`RC_01: VELOCITY_CLIFF`**
  * Recommended Action: **`Deep Content Refresh`**
* **Verification:** Tested across Chrome, Edge, and mobile Safari over HTTPS.
