Build Module $ARGUMENTS for this learning project.

## Instructions

Before doing anything, read these files completely — they define every convention you must follow:

1. `docs/module_guidelines.md` — the authoritative structure, style, and code conventions for all modules
2. `docs/course_overview.md` — the full course structure, module summaries, and technical spec
3. `CLAUDE.md` — critical project rules (especially: never implement 🔧 CORE function bodies)

Then read the current state of the codebase:
4. The source file(s) this module creates or extends (check the Module-to-Codebase Mapping table in the guidelines)
5. `tests/conftest.py` — shared fixtures available for tests
6. The project's central config file (e.g., `configs/*.yaml`) for data paths and parameters

If any previous module documents exist in `docs/modules/`, read the most recent one to maintain consistency in depth, tone, and quality.

## What to produce

### 1. Module document
Write `docs/modules/module_NN_<slug>.md` following the exact section structure from the guidelines:
- Header block (module number, title, time estimate, prerequisites, "you will build", "you will learn")
- Motivation (3–5 sentences, concrete forward reference)
- Theory + Implementation (interleaved, Feynman method, every concept motivated, every equation with plain-English preamble and practical note, 🔧/⚙️/▶️ tags on all code)
- Integration checkpoint (exact script to run, what correct output looks like, common failure modes)
- Deliverables (files with exact paths, artifacts, understanding statements)
- Key resources (3–5, with section-level specificity)

Use web search to find and verify the best current resources, documentation links, and technical details for the topics covered. Don't rely on memory for URLs or API details.

### 2. Test file
Write `tests/test_module_NN.py` with all test cases specified in the module document. Tests come BEFORE the functions they verify (test-first). Use fixtures from `conftest.py` where applicable. Tests must be runnable with `pytest` even before 🔧 CORE functions are implemented (they will raise NotImplementedError — that's expected).

### 3. Source file updates
Update the appropriate source file(s):
- Add 🔧 CORE function stubs: signature, type hints, full docstring, pseudocode comments, `raise NotImplementedError` — but NOT the implementation body
- Add ⚙️ INFRASTRUCTURE functions: implement these fully
- Add necessary imports

### 4. Runner script
Write `scripts/run_module_NN.py` — the end-to-end entry point for the module's integration checkpoint. It imports from the project library, calls the relevant functions, and saves outputs to `outputs/module_NN/`.

## Quality checks before finishing

Run through the checklist from the module guidelines:
- Every concept motivated before introduced
- Every equation has plain-English preamble and practical note
- Every code block tagged 🔧 CORE, ⚙️ INFRASTRUCTURE, or ▶️ RUN
- Core functions have signature, type hints, docstring, pseudocode, test specification
- Infrastructure tasks describe purpose and interface
- Tests specified before implementations
- Verification checkpoints after each implementation section
- Integration checkpoint runs full pipeline
- Deliverables list files with exact paths
- Key resources have section-level specificity
- Module document specifies which files it creates/extends
- No notebooks — visualization via saved plots, web viewers, or rendered videos
- No 🔧 CORE function bodies implemented — only stubs
