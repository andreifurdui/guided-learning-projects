# Guided Learning Projects

A system for generating self-directed, project-based technical courses using Claude Code. You describe what you want to learn, and the system builds a complete course that teaches the fundamentals by building a real project from scratch, module by module.

---

## What this produces

A project directory containing:

- **Course overview** — the full learning plan, technical spec, and module sequence
- **Module guidelines** — authoring conventions that ensure consistent quality across all modules
- **Project scaffolding** — source stubs, test fixtures, configs, and output directories
- **Two slash commands** for Claude Code:
  - `/create-module N` — generates a full module (theory doc, tests, code stubs, runner script)
  - `/review-module N` — conducts a Socratic dialog to solidify understanding
- **CLAUDE.md** — project instructions that keep Claude Code aligned with the course conventions

You build the course progressively: generate Module 0, work through it, review it, then move to Module 1. Each module teaches concepts through implementation — read the theory, write the code, pass the tests.

---

## Quick start

One command. Clone the starter, open Claude inside it, and go:

```bash
git clone --depth=1 https://github.com/<your-org>/guided-learning-projects.git my-course && cd my-course && rm -rf .git && claude
```

There's nothing to install — the `/create-learning-project` command ships inside the repo at `.claude/commands/`, so it's available the moment Claude opens in the folder.

> **On first launch, Claude Code asks "Do you trust the files in this folder?" — accept it.** Slash commands and tools only work once the folder is trusted.

---

## Usage

### 1. Create a learning project

Inside the cloned folder, with Claude running, invoke:

```
/create-learning-project
```

This **turns the current folder into your course**: it generates everything in place and initializes a fresh local git repository for it (the `rm -rf .git` in the clone command stripped the starter's history, so your course starts clean). There's no separate project directory to create.

The system will walk you through a conversation:
1. **Topic** — What do you want to learn? Be specific about the outcome.
2. **Familiarity** — Rate yourself 1–10. This calibrates theory depth and pacing.
3. **Duration** — Short (~5 modules, 2–3 weeks), Medium (~7–8, 4–5 weeks), or Long (~10–11, 5–6 weeks).
4. **Resources** — Optionally provide a work repo, papers, datasets, or documentation to anchor the course in.
5. **Refinement** — The system proposes a course plan. You discuss and refine it together. Then it builds everything.

### 2. Work through modules

Once the project is generated, **start a fresh `claude` session** in the folder — the per-module commands (`/create-module`, `/review-module`) were written during scaffolding and may not appear in autocomplete until Claude re-scans on launch. Then build the course module by module:

```
/create-module 0
```

This generates four artifacts:
- `docs/modules/module_00_<topic>.md` — the full module document with interleaved theory and implementation
- `tests/test_module_00.py` — test cases to implement first
- `src/<package>/<file>.py` — updated with function stubs
- `scripts/run_module_00.py` — end-to-end runner

**The workflow for each module:**

1. **Read the module document.** It flows from concept to implementation — read a theory section, then implement the corresponding code.
2. **Write the tests first** (or let Claude Code write them from the specs in the doc).
3. **Implement 🔧 CORE functions by hand.** These are marked with `raise NotImplementedError` — the learning happens here. Don't let Claude Code write these for you.
4. **Use Claude Code for ⚙️ INFRASTRUCTURE.** Boilerplate, data loading, format converters, visualization — delegate freely.
5. **Run the integration checkpoint** at the end: `python scripts/run_module_NN.py`
6. **Move to the next module.**

### 3. Review your understanding

After completing a module, solidify your knowledge:

```
/review-module 0
```

This starts a Socratic dialog — not a quiz, but a conversation. It asks probing questions about the concepts you just implemented: "what would happen if...", "why this approach over that one?", "here's a failure scenario — diagnose it." One question at a time, adapting to your level.

---

## The three code categories

Every piece of code in the course is tagged as one of:

| Tag | Meaning | Who writes it |
|-----|---------|---------------|
| 🔧 **CORE** | Domain logic where the learning happens | You, by hand |
| ⚙️ **INFRASTRUCTURE** | Boilerplate, I/O, helpers, visualization | Claude Code or you |
| ▶️ **RUN** | External tool commands | You execute these |

The critical rule: **🔧 CORE functions are never implemented by Claude Code.** They have full signatures, docstrings, and pseudocode comments — but the implementation body is yours to write. This is where understanding is built.

---

## Design philosophy

**Learn by building.** Every module produces a working piece of the final project. Theory exists to support implementation, not the other way around.

**Feynman method.** Concepts are introduced as answers to questions you should naturally be asking. No cold definitions. Every equation gets a plain-English explanation first, then formalization, then practical implications.

**Test-first.** Tests are written before implementations. You know you understand a concept when your code passes the tests that exercise it.

**Progressive construction.** Module 0's code is imported by Module 1. The library grows organically into a coherent codebase, not a collection of disconnected scripts.

**Socratic review.** Understanding is verified through dialog, not recall. The review command never asks "what is X?" — it asks "why does X matter here?" and "what would break if X were different?"

---

## What makes a good topic

This system works best for technical topics with a **concrete build target** — something you can point to at the end and say "I built that." The stronger the final artifact, the better the course.

**Works well:**
- "Build a real-time speech-to-text pipeline that runs on-device"
- "Build a recommendation engine from collaborative filtering to deployment"
- "Build a compiler for a small programming language"
- "Build a distributed key-value store with consensus"
- "Build a 3D reconstruction pipeline from RGB-D camera data"

**Works less well:**
- Purely theoretical topics with no build artifact ("learn category theory")
- Topics too broad to have a single project ("learn machine learning")
- Non-technical topics (the framework assumes code as the primary output)

When in doubt, try it — the interactive phase will help narrow the scope.

---

## Repository contents

```
guided-learning-projects/
├── README.md                          # This file
├── CLAUDE.md                          # Greets Claude in the unstarted clone
├── .gitignore
├── .claude/
│   └── commands/
│       └── create-learning-project.md # The skill — runs automatically on clone
└── templates/                         # Authoritative source for the per-module commands
    ├── create-module.md               # Source for /create-module (slot-filled at generation)
    └── review-module.md               # Source for /review-module (slot-filled at generation)
```

The `templates/` directory is the **authoritative source** for the two per-module commands. When you run `/create-learning-project`, the skill reads each template, fills its course-specific adaptation slots (marked with `<!-- SLOT:… -->`), and writes the result to `.claude/commands/`. **Editing a template changes what gets generated** — this is where you'd tune module generation or Socratic review across all your courses. Everything outside the slots is preserved verbatim, so the structure stays consistent. Both `templates/` and the bootstrap command are removed automatically once your course is scaffolded, leaving a clean course repo with its own `.claude/commands/`.
