# The IDesign Method, A Working Primer

**Last Updated:** 2026-06-05
**Source material:** Juval Löwy, *Righting Software* (Addison-Wesley, 2019); idesign.net; secondary summaries (cited at the end)
**Scope:** Core methodology plus Löwy's "Architect in the Age of AI" thesis. Not a Webapper-specific mapping.

**The whole methodology in one sentence: design the system around what changes, not around what it does, then design the project as a network so you can hand management real options for time, cost, and risk instead of one guess.**

---

## 1. What it actually is

The IDesign Method ("The Method") is a structured design methodology from Juval Löwy and IDesign, distilled into his 2019 book *Righting Software*. It is not a framework, a manifesto, or a development process. It is a way to produce two artifacts fast, usually in about a week each for a trained architect:

1. **System design**, the high-level software structure (the architecture).
2. **Project design**, the plan to build that system, expressed as a set of time/cost/risk options.

The formula they repeat everywhere:

> **The Method = System Design + Project Design**

That pairing is the whole point. Most design techniques give you a structure but no notion of time or cost. The Method treats "how you build it" as a design problem with the same rigor as "how you structure it."

It is also a **design process, not a development process.** It sits on top of Scrum, Kanban, or anything else. You design with The Method, then you execute with whatever delivery process you already run.

---

## 2. The core problem it solves

Most systems are designed against their **requirements**. You get requirements A, B, and C, so you build a service for A, a service for B, and a service for C. Löwy calls this **functional decomposition**, and his central claim is that it is a trap.

The reasoning is simple and hard to argue with: requirements always change. When your structure mirrors the requirements, every requirement change spreads across the structure. Functional decomposition reliably produces:

- Clients doing orchestration (hard to test, hard to reuse across web and mobile).
- The same behavior duplicated across services.
- Service bloat and "explosion" as features multiply.
- Services stitched together because features were built as services instead of as integrations.
- Tight coupling to the current use cases and their order of execution.

The one line to remember: **never design against the requirements.** Domain decomposition (carving the system by business domain) gets criticized for the same reasons. It still tends to bury volatility and harm reuse.

---

## 3. System Design: volatility-based decomposition

The alternative is to **decompose by volatility.** Identify the areas of the system most likely to change, and encapsulate each one inside a component. The required behaviors of the system then become the **integration** of those components, not the components themselves.

The metaphor IDesign uses: your architecture is a room of fireproof vaults. A requirement change is a bomb. You throw it into the right vault, where it detonates without touching anything else in the room. Good decomposition means most changes land inside a single vault.

When you get this right, the architecture stays valid for years even as requirements churn, because the changes are contained. That is the actual definition of agility here, and it is what functional decomposition can never deliver.

### The process (three moves)

1. **Extract the core use cases.** Not functional requirements, the behaviors that deliver business value. Group everything down to **3 to 5 core use cases.** Löwy's claim is that every system has only 3 to 5, never more. Everything else is a variation. The gut check: if you wrote a brochure for the system, what are the 3 to 5 bullets?
2. **Find the areas of volatility.** Look along two axes: how different users interact with the system at the same moment in time (power user vs. consumer), and how one user's interaction changes over time. Common volatilities: how a user interacts, notification medium, payment method, data scale, storage target.
3. **Define the services** that encapsulate those volatilities, using a fixed, small vocabulary of component types.

### The building blocks

The Method allows a deliberately tiny vocabulary. Clean responsibilities, easy to teach, easy to enforce.

| Layer | Role | The question it answers |
|---|---|---|
| **Client** | Presentation / entry point. No business logic. | **Who** is making the request? |
| **Manager** | Orchestrates a use case, owns the workflow / sequence. | **What** needs to be done? |
| **Engine** | Executes business logic and rules for an activity. | **How** is it done? |
| **ResourceAccess** | Encapsulates access to a resource (the verbs around it). | **Where** does the data live? |
| **Resource** | The actual store (database, queue, external API). | The thing being accessed. |
| **Utility** (bar) | Cross-cutting infrastructure: security, logging, diagnostics, message bus. | Plumbing, reusable anywhere. |

The test for a Utility: it should be generic enough to **work in a coffee machine.** If it knows anything about your business domain, it is not a Utility.

### The call rules (semi-closed architecture)

This is where The Method gets opinionated. The common ruleset is "semi-closed":

1. **Calls flow top to bottom only.** Client to Manager to Engine to ResourceAccess to Resource.
2. **A component can call anything below it,** and can skip layers downward (a Manager can call ResourceAccess directly if no Engine is needed).
3. **Managers talk to other Managers only asynchronously,** by queuing or publishing an event through the message bus. Never a synchronous Manager-to-Manager call.
4. **Nothing calls sideways or upward.** Engines do not call Engines, ResourceAccess does not call ResourceAccess, and nothing below calls back up.
5. **Every component can use any Utility.**
6. **Clients hold no business logic and should never call more than one Manager.**

Those constraints are what kill coupling. If changing one thing breaks something unrelated, you violated a rule somewhere.

### Two gradients fall out of this

- **Volatility decreases as you go down.** Clients change most often, Resources change least. The stable stuff sits at the bottom.
- **Reuse increases as you go down.** Clients are platform-specific and barely reusable. Managers, Engines, and especially ResourceAccess and Resources get reused across use cases and even across systems.

A practical tell of good design: **you end up with few Managers** (one Manager serves a whole family of use cases) and proportionally more Engines and ResourceAccess components below.

### The principle that ties it together

**Features are aspects of integration, never units of implementation.** A feature is what happens when components collaborate. It is not a thing you build in isolation. The architect's actual mission, stated plainly: find the **smallest set of services** that satisfies every core use case and encapsulates every volatility. Smallest. Not the most complete, not the most clever.

### Prove it with a vertical slice

IDesign validates an architecture by building a **vertical slice**: a stubbed, end-to-end implementation of one or two services in each layer. It surfaces the plumbing problems (the real Achilles heel of most systems) early, lets you stress-test against performance and scale goals, and confirms the team actually understands the design before they pour months into it.

### Architecture is not detailed design

The Method explicitly stops at the architecture. UI, reports, data contracts, and the internal code design of each component are **detailed design**, done during development, just in time, against the architecture. This is what separates The Method from waterfall. The up-front design is **broad but terse,** good enough to drive educated decisions, not a months-long big-design-up-front exercise. Think architecture of a house, not the paint colors and furniture.

---

## 4. Project Design: build the plan as a network

Part two is the half most architects have never seen done rigorously. The Method designs the project itself using the **Critical Path Method** (the same network technique that came out of the 1940s Manhattan Project), with the architecture as the key input.

### Build the graph

- **Activities:** coding activities (IDesign largely splits these by service) plus non-coding activities (UX, migrations, contract negotiation, acceptance testing).
- **Dependencies:** derived from the architecture, call chains, and team/organizational constraints.
- **Estimates:** for every activity (PERT, historical data, or Wideband Delphi).

Once it is a graph, you get graph theory for free: duration, cost, staffing, complexity, and risk become things you calculate instead of things you guess.

### The levers

- **Critical path:** the longest dependency chain. It sets the minimum possible duration for any staffing level.
- **Float (slack):** how long a non-critical activity can slip before it delays the project. "Sub-critical" roughly means understaffed (you cannot finish the off-path work in time, which manufactures a new critical path).
- **Compression:** shorten the critical path by adding faster resources, splitting activities, or designing to a contract and integrating later. Compression only helps when applied to the critical path. Projects compress about **25 to 30 percent** before it stops paying off.
- **Decompression:** deliberately add float back to lengthen the project and **lower risk.** A calculated safety buffer.

### The metrics worth memorizing

| Metric | What it measures | Healthy range |
|---|---|---|
| **Efficiency** | Share of staff time actively advancing the project vs. idle | 15 to 25 percent (over 30 percent is too rigorous, high risk) |
| **Complexity** | Cyclomatic complexity of the project network | 10 to 12 (15 is too complex to execute) |
| **Risk** | An index (not a probability) of failure likelihood | roughly 0.3 to 0.75, sweet spot near 0.5 |

Two ways to compute risk, both yielding an index between 0 and 1: **criticality risk** (weights activities by how close to critical they are) and **activity risk** (based on total float vs. maximum possible float). They usually agree. When they diverge, you have a lopsided float distribution worth investigating. Counterintuitive but important: **risk that is too low is also bad.** It usually means you overstaffed and are burning money for safety you do not need.

### The flow: turn one project into a spectrum of options

1. **Establish the normal solution:** least direct cost, does not impede the critical path, respects real constraints (staffing, specialists), risk under about 0.7.
2. **Compress** from there, plotting cost against duration at each step, until compression hits an asymptote or blows the budget.
3. **Decompress** the promising solutions, plotting risk and cost against duration, until risk drops into the target band.
4. **Pick about three** options that give management the best trade-offs.

A real example from the book, the kind of table that goes to decision-makers:

| Design Option | Duration (months) | Total Cost (man-months) | Risk |
|---|---|---|---|
| Activity Driven | 8 | 61 | 0.74 |
| Architecture Driven | 9 | 68 | 0.38 |
| Understaffed | 12 | 80 | 0.47 |

### The death zone

Fit a curve to your viable solutions on the cost-vs-duration plot. Anything above the line is achievable. Anything below it (cheaper *and* faster than the curve allows) is the **death zone**: physically impossible, and any project promised there should be killed immediately rather than marched into failure. This single idea is why architects who learn project design stop losing arguments with management. You are no longer debating opinions, you are pointing at a line.

---

## 5. Why people rate it

- **It is fast.** Designing a moderate system or a decent-size project each takes about a week for a trained architect. The cost is front-loaded and small.
- **It gives management real choices.** Three defensible options beat one optimistic estimate.
- **It quantifies risk,** which almost nothing else does.
- **It is process-agnostic.** Layer it on top of your existing Agile delivery.

The honest limitation, raised by practitioners: the project-design half assumes longer, standalone initiatives. At a startup or on a two-week feature where the business model itself is still moving, the full critical-path apparatus can be heavier than the work justifies. The system-design half (volatility-based decomposition) scales down much more cleanly than the project-design half.

---

## 6. The Architect in the Age of AI (Löwy's current thesis)

This is the throughline from the methodology to where Löwy is pointing now, and it is the part most relevant to anyone leading an AI transformation. IDesign is running a 2026 lecture titled "Architect in the Age of AI." Their own description of the argument, quoted directly:

> "By default, AI generates very complex code that is not reusable, and it lacks design integrity. The cost of ownership of this code could exceed by multiples the cost of human-generated code. Instead of gains, developers experience strains trying to understand what the AI has generated. Companies now transition from AI euphoria to AI spaghetti. Throttling back token spending does not address the underlying complexity."

The claims, broken out:

- AI defaults to **complex, non-reusable** code.
- That code **lacks design integrity**, it has no coherent structure holding it together.
- **Cost of ownership can exceed human-written code by multiples,** because someone still has to understand and maintain it.
- **"AI euphoria to AI spaghetti."** The productivity high is followed by a maintenance hangover.
- **Cutting token spend does not fix it.** The cost is structural complexity, not inference dollars.

His resolution, in his framing: the **architect** is the role that unleashes AI's productivity gains by applying real software-engineering discipline. AI is a phenomenal implementer and a poor designer. Point it at a sound architecture and it accelerates you. Let it design, and it compounds complexity faster than any human team could.

**Why The Method is his answer (my synthesis, flagged as such):** everything The Method optimizes for is exactly what AI-generated code lacks by default. Volatility encapsulation gives changes a place to land. The smallest-possible service set fights the bloat AI loves to produce. The reuse gradient and the strict call rules impose the design integrity AI does not bring on its own. In that reading, AI does not make the architect obsolete, it makes the architect the bottleneck that determines whether AI pays off or quietly bankrupts you in maintenance. Future-proof the role by owning the design that the AI then fills in.

---

## 7. How to go deeper

- **Book:** *Righting Software*, 1st ed., Juval Löwy (Addison-Wesley, 2019). The canonical source. Part 1 is system design, Part 2 is project design.
- **Free PDF:** "The IDesign Method Management Overview" on idesign.net. Eight pages, executive-level, no code. Good to hand to a non-technical exec.
- **The AI lecture:** "Architect in the Age of AI" (IDesign / software-leader.com), if a recording becomes available.
- **Training:** the Architect's Master Class and Project Design Master Class are the official deep dives.
- **Fast secondary reads:** Tal Joffe's "Software architecture with the IDesign method" (the cleanest short intro to system design), and Spencer Farley's "Righting Software, Project Design Summary" (the best free walkthrough of the critical-path math).

---

## Bottom line

**System design protects you from changing requirements. Project design protects you from management's guesses. In an AI-native shop, both are the discipline that turns AI from a complexity generator into a force multiplier, and the architect is the one who supplies it.**

---

## Sources

- [IDesign homepage and "Architect in the Age of AI" lecture description](https://www.idesign.net/)
- [The IDesign Method, Management Overview (PDF)](https://www.idesign.net/Download/IDesign-Method-Management-Overview.pdf)
- [IDesign, System Design service page](https://www.idesign.net/Services/System-Design)
- [Tal Joffe, "Software architecture with the IDesign method" (Medium)](https://medium.com/nmc-techblog/software-architecture-with-the-idesign-method-63716a8329ec)
- [Spencer Farley, "Righting Software, Project Design Summary"](https://spencerfarley.com/2020/07/16/idesign-project-summary/)
- [InfoQ, Q&A on the book Righting Software](https://www.infoq.com/articles/book-review-righting-software/)
- [Righting Software, Juval Löwy (Amazon / InformIT)](https://www.informit.com/store/righting-software-9780136524038)

*Confidence note: the architecture rules, the 5 component types, and the project-design metric bands (efficiency 15 to 25 percent, complexity 10 to 12, risk roughly 0.3 to 0.75) are consistent across IDesign's own materials and multiple independent summaries. The exact risk thresholds are Löwy's and worth confirming against the book before you cite them in a formal doc. The AI-thesis quote is verbatim from IDesign's site; the closing argument tying The Method to AI is my synthesis, labeled above.*
