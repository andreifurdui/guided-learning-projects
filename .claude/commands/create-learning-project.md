You are building a guided learning project — a self-directed, postgraduate-level course that teaches a technical topic by building a real project from scratch, module by module.

This skill runs in two phases: **interactive elicitation** (gather requirements with the user) followed by **autonomous generation** (build the entire project scaffolding). Do not skip or rush the interactive phase — the quality of the output depends entirely on understanding the user's goals.

---

# PHASE 1 — INTERACTIVE ELICITATION

Conduct this as a natural conversation. Ask one question at a time. Wait for the answer before proceeding. Do not present all questions at once.

## Step 1: Topic

Ask the user to describe what they want to learn. Encourage them to be specific about the *outcome* they want, not just the subject. Good: "I want to build a real-time speech-to-text pipeline that runs on-device." Less good: "I want to learn about speech recognition."

If the description is vague, ask one follow-up to sharpen it.

## Step 2: Familiarity

Ask the user to rate their familiarity with the domain on a scale from 1 to 10:
- 1–3: Beginner. Knows the field exists, maybe some adjacent skills, but hasn't worked in it.
- 4–6: Intermediate. Has done some work or study, understands basic concepts, but hasn't built anything substantial.
- 7–9: Advanced. Has working experience, understands the landscape, wants to go deeper or learn a specific sub-area.
- 10: Expert. Wants a structured project to consolidate mastery or explore a cutting edge.

This rating calibrates:
- **Theory depth:** Lower familiarity → more foundational explanation, more analogies, gentler math. Higher familiarity → concise theory, focus on non-obvious insights, deeper math where it matters.
- **Module count at each tier:** Lower familiarity → more foundation modules before the core work. Higher familiarity → skip or compress foundations, more time on advanced topics.
- **Refinement depth in Step 5:** Lower familiarity → you (the agent) make more decisions autonomously, ask simpler confirming questions. Higher familiarity → present trade-offs and let the user make architectural choices.

## Step 3: Duration

Ask the user to choose a course length:
- **Short** (~5 modules): Foundations + core skill + one integration pass. 2–3 weeks of work.
- **Medium** (~7–8 modules): Foundations + core + extensions + evaluation. 4–5 weeks.
- **Long** (~10–11 modules): Full treatment — foundations, core, extensions, evaluation, scaling, capstone. 5–6+ weeks.

Clarify: module depth stays the same regardless of duration. Duration controls how many modules the course is decomposed into, which determines how far you go from fundamentals toward a polished final product.

## Step 4: Resources

Ask whether the user has any existing resources to anchor the course in:
- **A work repository** they'd like the course to build alongside or integrate with (the agent will explore it to understand its structure, conventions, and domain)
- **Papers or articles** they want to work through
- **Documentation, datasets, or specifications** that should ground the project in real data
- **Nothing** — the course stands alone, and the agent picks best-practice tools and data

If resources are provided:
- For repositories: explore the directory tree, read key files (README, main entry points, config), and understand the domain, language, conventions, and architecture. The course should complement this codebase — referencing its patterns, potentially building extensions or related tooling.
- For papers/docs: read them (use web_fetch if URLs, or read from disk if paths). Extract the key concepts, methods, and tools that the course should teach.
- For datasets: understand the schema, format, and size. The course project should work with this data.

## Step 5: Collaborative Refinement

This is the critical step. Based on everything gathered, you now:

1. **Research the domain.** Use web search to find:
   - Current gold-standard tools, libraries, and frameworks for this topic
   - The most respected learning resources, tutorials, and papers
   - Common pitfalls and best practices for the project type
   - Any recent developments that affect tool or approach choices

2. **Design the course.** Propose:
   - The **final project goal** — a single, concrete artifact the student will have built by the end (e.g., "a real-time 3D reconstruction of an indoor building from RGB-D video, viewable in a web browser")
   - The **module sequence** — each module's title, what it teaches, what it produces, and how it builds toward the final goal
   - The **technical stack** — which libraries, tools, and algorithms the course uses, and why these over alternatives
   - The **codebase structure** — what the Python package looks like, which files map to which modules

3. **Present the plan and refine it with the user.** Show the proposed structure and ask for feedback. Adapt the depth and specificity of your questions to the user's familiarity level:
   - **Familiarity 1–4:** Present the plan as a recommendation. Ask: "Does this goal match what you had in mind? Anything you'd like to add or change?" Keep it simple — one or two confirming questions.
   - **Familiarity 5–7:** Present the plan with the key trade-offs you considered. Ask about specific choices: "I chose X over Y because Z — does that align with your experience?" Allow 2–3 rounds of refinement.
   - **Familiarity 8–10:** Present the plan as a discussion. Flag the decisions you're least sure about. Ask: "Here's where I'd like your input on the approach." Allow the user to reshape the module structure, swap tools, or adjust scope. This mirrors a peer design conversation.

4. **If multiple viable end goals exist,** present them as options with trade-offs and let the user choose. If the final goal is clear and obvious from the topic description, proceed directly.

5. **Converge.** Once the user approves (or after reasonable refinement), confirm the final plan in a brief summary and proceed to Phase 2.

---

# PHASE 2 — AUTONOMOUS GENERATION

Once the interactive phase is complete, build the entire project. Do not ask further questions — execute the plan.

## What to generate

### 1. Directory structure

**Generate everything in place, directly into the current working directory** — the folder the user cloned and launched Claude from. The `<project-slug>/` at the root of the diagram below denotes that current directory's *role* as the project root; do **not** create an extra nested folder. The `.claude/commands/` directory already exists in the clone and currently holds this bootstrap command — generation writes the two per-module commands into it (and the Finalize step later removes this bootstrap command from it).

```
<project-slug>/                          # = the current working directory (do not nest)
├── CLAUDE.md
├── README.md
├── .gitignore
├── pyproject.toml                       # or equivalent for the project's language
│
├── .claude/
│   └── commands/
│       ├── create-module.md             # Per-module generation skill
│       └── review-module.md             # Socratic review skill
│
├── configs/
│   └── <project-config>.yaml            # Central configuration (data paths, parameters)
│
├── docs/
│   ├── course_overview.md               # Full course plan and technical spec
│   ├── module_guidelines.md             # Authoring conventions (the template system)
│   └── modules/                         # Individual module docs go here (empty at creation)
│
├── src/<package_name>/                  # Library that grows module by module
│   ├── __init__.py
│   └── <one .py stub per module topic>
│
├── scripts/                             # Per-module entry points
│
├── tests/
│   └── conftest.py                      # Shared test fixtures
│
└── outputs/                             # Generated artifacts (gitignored)
    └── <one dir per module>/
```

Adapt the layout to the project's language and conventions. Python with pytest is the default. If the topic naturally uses a different language (e.g., Rust, TypeScript), adapt accordingly — but the structural principles (one source file per module topic, test files, runner scripts, configs) remain.

### 2. CLAUDE.md

This file must contain both the project-specific conventions AND the following general guidelines, included verbatim at the top:

```markdown
## General Guidelines

General guidelines for working with Claude, the AI assistant. These principles aim to maximize clarity, efficiency, and alignment with user intent while minimizing unnecessary changes and overcomplication.

### 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

### 2. Simplicity and Maintainability First

**Minimum code that solves the problem. Implementation that anyone can understand later.**

- No features beyond what was asked.
- Name things for what they *are*, not what they *do* in the moment.
- No abstractions for single-use code.
- Error handling where failure is real and likely.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated? Would someone unfamiliar with this code fail to understand it in 60 seconds?" If yes, simplify.

### 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

### 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.
```

After the general guidelines, include the project-specific section:

```markdown
## Project-Specific Conventions

### 🔧 CORE functions — DO NOT IMPLEMENT

Functions tagged `🔧 CORE` in module documents and marked with `raise NotImplementedError` in source files are hand-implemented by the student. Never write the implementation body. You may add the function signature, type hints, docstring, pseudocode comments, and the NotImplementedError stub. You may write tests and infrastructure that call these functions. You must NOT fill in the implementation.

### ⚙️ INFRASTRUCTURE functions — implement freely

Boilerplate: data loading, file I/O, format conversion, visualization, config parsing. Implement these fully.

### ▶️ RUN commands — document only

Shell commands for external tools. Document in module docs and scripts but do not execute.

### No notebooks

All visualization via saved plots, web-based viewers, or rendered videos.

### Module development workflow

1. Read the module document in docs/modules/
2. Write tests in tests/test_module_NN.py
3. Add 🔧 CORE function stubs to src/<package>/
4. Implement ⚙️ INFRASTRUCTURE functions fully
5. Write scripts/run_module_NN.py
6. Student fills in 🔧 CORE implementations until tests pass
```

Adapt the conventions to the project's language and tooling, but preserve the three code categories and the rule that 🔧 CORE functions are never implemented by agents.

### 3. README.md

Write a comprehensive README that serves as both project documentation and a guide for someone who has never used this workflow before. Include:

**What this is:** A self-directed learning project on [topic], structured as [N] progressive modules that build toward [final goal].

**How to use this project:**

1. **Start with Module 0.** In Claude Code, from the project directory, run:
   ```
   /create-module 0
   ```
   This generates the full module document (theory + implementation guide), test file, source stubs, and runner script.

2. **Read the module document** in `docs/modules/`. It interleaves theory with implementation — read a concept, then implement the corresponding function.

3. **Write the tests first** (or let Claude Code write them from the test specs in the module doc). Then implement the 🔧 CORE functions by hand until the tests pass. Use Claude Code for ⚙️ INFRASTRUCTURE boilerplate.

4. **Run the integration checkpoint** at the end of each module (`python scripts/run_module_NN.py`) to verify everything works together.

5. **Review your understanding** with a Socratic dialog:
   ```
   /review-module 0
   ```

6. **Move to the next module** and repeat. Each module builds on the previous one's code and concepts.

**Project structure:** Brief description of each directory.

**Course modules:** Table listing all modules with title, source file, and status checkbox.

### 4. course_overview.md

Write the full course plan following this structure:

1. **Purpose and structure** — what this document is, how the course works, what the final project produces
2. **Technical specification** — the tools, libraries, algorithms, key papers, and data being used, with rationale for each choice. If the user provided resources (repo, papers, data), reference them here.
3. **Module summaries** — for each module: title, estimated time, theory topics, practical output, and how it connects to the next module. Each summary must be detailed enough that the create-module command can generate a full module document from it alone — specify the concepts taught, the key functions/code produced, the practical output, and the connection to adjacent modules.
4. **Module dependency graph** — ASCII art showing which modules depend on which
5. **Time budget** — table with days per module and cumulative total

### 5. module_guidelines.md

Generate the full authoring conventions document. This is the "system prompt" for the create-module skill. It must contain ALL of the following sections:

**1. Module document structure** — the exact section order every module follows:
- Header block (module number, title, time estimate, prerequisites, "you will build", "you will learn")
- Motivation (3–5 sentences connecting to final project)
- Theory + Implementation (interleaved, Feynman method)
- Integration checkpoint (script to run, expected output, failure modes)
- Deliverables (files, artifacts, understanding statements)
- Key resources (3–5, section-level specificity)

**2. Writing style:**
- Conversational, address the reader as "you"
- Feynman method: every concept motivated, explained in plain language first, then formalized
- Every equation gets a plain-English preamble and a practical note
- "Formulas at a glance" callout after each section for quick reference
- Deep dives deferred to specific external resources with section-level precision

**3. Code guidance conventions:**
- 🔧 CORE: signature, type hints, docstring, pseudocode *that guides the approach without giving the solution*, NotImplementedError stub. No implementation body. CORE = the idea the student must derive and implement.
- ⚙️ INFRASTRUCTURE: describe purpose and interface. Agent implements freely. INFRASTRUCTURE = setup/display/glue around the idea (I/O, plotting, config).
- ▶️ RUN: exact commands with parameter annotations.
- Every code block must be tagged with one of the three categories.

**4. Test-first approach:**
- Tests specified before implementations in the module document
- Tests in `tests/test_module_NN.py`, runnable with pytest
- Test categories: numerical assertions, shape/type checks, invariant checks, visual verification (saved plots), system tests
- Each 🔧 CORE function has ≥1 numerical or invariant test with known expected values (not just a shape check)
- Tests import cleanly and, before implementation, fail only with `NotImplementedError` — never `ImportError` or a collection error
- Test cases provided inline in the module doc in copy-paste-ready format

**5. Project layout** — the exact directory structure with annotations

**6. External tool integration conventions** — how tool configs, invocations, and outputs are handled

**7. Module-to-codebase mapping** — table showing which files each module creates or extends

**8. Quality checklist** — the verification list module authors check before finalizing. Must include:
- Every concept motivated before introduced
- Every equation has plain-English preamble and practical note
- Every code block tagged 🔧/⚙️/▶️
- Core functions have signature, types, docstring, guiding (non-solving) pseudocode, and a numerical/invariant test
- Infrastructure describes purpose and interface
- Tests before implementations and import cleanly (fail only with `NotImplementedError`)
- Verification checkpoints after each section
- Integration checkpoint runs full pipeline
- Self-check run (or explicitly skipped with a note when the environment isn't set up)
- Deliverables with exact paths
- Resources with section-level specificity
- No notebooks

### 6. .claude/commands/create-module.md

Read `templates/create-module.md` and write its content to `.claude/commands/create-module.md`. **This template is the single source of truth** — do not paraphrase, reorder, or drop sections.

**Slot rule.** The template contains adaptation slots marked like this:

```
<!-- SLOT:slot_id — fill: <instruction for what to inject> -->
<default placeholder content>
<!-- /SLOT:slot_id -->
```

For each slot: replace **only** the content between the two markers, following its `fill:` instruction and drawing from the course plan you just designed; then **delete the two marker lines** so the generated file contains no `<!-- SLOT ... -->` comments. Copy everything outside slot blocks **verbatim**. If a slot genuinely can't be filled, keep its default text (still strip the markers).

Fill targets for this command: `config_ref` (the project's central config path + description), `source_map_ref` (the concrete `src/<package>/…` path for module source), `domain_test_focus` (2–4 domain-specific test categories / failure modes).

### 7. .claude/commands/review-module.md

Read `templates/review-module.md` and write its content to `.claude/commands/review-module.md` using the **identical slot rule from section 6**. Preserve all four dialog phases, every conduct rule, and the complete "What NOT to do" section verbatim.

Fill targets for this command: `source_ref` (the `src/<package>/` path where module source lives), `phase1_examples` (2 opening questions for the course domain), `phase3_examples` (2 domain-specific debugging scenarios), `common_misconceptions` (3–5 misconceptions students hold about this domain). Draw all of these from the course's topic and module plan.

### 8. Source file stubs

For each module topic, create a stub `.py` file in `src/<package>/` with:
- Module docstring listing which course modules populate it and what functions it will contain
- Necessary base imports
- No function implementations yet (those come from create-module)

### 9. Test conftest.py

Create `tests/conftest.py` with shared fixtures appropriate for the project domain. These should provide common test data that multiple module test files will use.

### 10. Config file

Create the central configuration file (e.g., `configs/dataset.yaml` or equivalent) with the project's key parameters. If the user provided data or a repository, reference those paths. Use placeholder values where specific data isn't available yet.

### 11. .gitignore

Appropriate for the project's language and tooling. Always include `outputs/`.

### 12. pyproject.toml (or equivalent)

Package configuration with dependencies, dev dependencies, and test configuration. List all libraries identified in the technical spec.

### 13. Finalize: clean up and initialize git

This is the **last** step. Run it only after every file above has been written. Perform these in order so the initial commit contains the real course and nothing else:

1. **Confirm the generated `CLAUDE.md` and `README.md` are in place.** They overwrite the starter versions that shipped with the clone — make sure this happened before staging.
2. **Remove the starter-only files** now that the course is generated:
   - the `templates/` directory — but **only after sections 6 & 7 have already written** `.claude/commands/create-module.md` and `.claude/commands/review-module.md` from it (the templates are the source for those files; they are not needed in the generated course once written)
   - this bootstrap command itself: `.claude/commands/create-learning-project.md` (it is a one-time scaffolding command; removing it leaves `.claude/commands/` holding exactly `create-module.md` and `review-module.md`). Deleting it now is safe — the command's instructions are already loaded in this session and are not re-read from disk.
3. **Initialize a fresh local git repository** for the course and make the first commit:
   ```bash
   git init
   git add -A
   git commit -m "Initial course scaffold"
   ```
   Use that exact, fixed commit message — do not interpolate the course title (titles may contain quotes/colons/parentheses that break shell quoting). Do **not** run `rm -rf .git` or any other destructive `rm -rf` — the user's clone command already stripped the workshop history before launching Claude.

---

# QUALITY STANDARDS

Before presenting the generated project to the user, verify:

- [ ] The course overview tells a coherent story from Module 0 to the final deliverable
- [ ] Every module builds on the previous one — no module could be done out of order
- [ ] The technical stack choices are current and well-supported (verified via web search)
- [ ] The codebase structure is clean — one file per topic, clear imports between modules
- [ ] The CLAUDE.md contains both the General Guidelines (verbatim) and project-specific conventions
- [ ] `.claude/commands/create-module.md` matches `templates/create-module.md` — slots filled from the course plan, all markers stripped, everything else verbatim
- [ ] `.claude/commands/review-module.md` matches `templates/review-module.md` — slots filled (incl. domain examples), markers stripped, all four phases / conduct rules / "What NOT to do" verbatim
- [ ] The README explains the full workflow clearly enough for a first-time user
- [ ] The module guidelines contain all 8 sections specified above
- [ ] No notebooks anywhere — all visualization via plots, web viewers, or rendered media
- [ ] If user resources were provided, they are integrated into the course plan
- [ ] Duration scaling is reflected in module count (short ~5, medium ~7-8, long ~10-11)
- [ ] The project is generated in place in the current directory — no extra nested folder
- [ ] Starter-only files removed (`templates/` and the `create-learning-project.md` bootstrap command)
- [ ] A fresh local git repository was initialized with a single "Initial course scaffold" commit

---

# IMPORTANT NOTES

- **Use web search liberally** during the research phase. Do not rely on memory for tool versions, library APIs, current best practices, or documentation URLs. Verify everything.
- **The module guidelines are the most important file.** They are the "system prompt" for create-module, which does the heavy lifting of producing each module. If the guidelines are weak, every module will be weak. Spend the most care here.
- **Adapt to the domain.** The structural principles are universal, but the specifics must fit. A systems programming course might use C and Make instead of Python and pytest. A data engineering course might use SQL and dbt. Adapt the tooling conventions while preserving the pedagogical framework.
- **The course overview determines the quality ceiling.** The module summaries must be detailed enough that create-module can produce a full module document from them. Each summary should specify: the concepts taught, the practical output, the key functions/code produced, and the connection to adjacent modules.
- **The create-module and review-module commands must be complete and self-contained.** They are generated into the project's `.claude/commands/` directory and must work without referencing any files outside the project. Every instruction, every phase, every rule must be present in the generated file — not abbreviated or summarized.
