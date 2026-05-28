Build Module $ARGUMENTS for this learning project.

## Instructions

Before doing anything, read these files completely — they define every convention you must follow:

1. `docs/module_guidelines.md` — the authoritative structure, style, and code conventions for all modules
2. `docs/course_overview.md` — the full course structure, module summaries, and technical spec. Find the summary for **this** module number and treat it as the brief.
3. `CLAUDE.md` — critical project rules (especially: never implement 🔧 CORE function bodies)

Then read the current state of the codebase:
4. The source file(s) this module creates or extends (check the Module-to-Codebase Mapping table in the guidelines)
5. `tests/conftest.py` — shared fixtures available for tests
<!-- SLOT:config_ref — fill: the project's central config file path + a one-line description of what it holds -->
6. The project's central config file (e.g., `configs/*.yaml`) for data paths and parameters
<!-- /SLOT:config_ref -->

If any previous module documents exist in `docs/modules/`, read the most recent one to maintain consistency in depth, tone, and quality.

## Scope

A module teaches a **bounded** set of concepts and produces roughly one source file's worth of 🔧 CORE functions. If the module brief implies more than that, teach the core idea well and defer the extras to a later module — note the deferral explicitly rather than expanding the module. A focused module the student finishes beats a sprawling one they abandon.

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

**Quality target — theory.** Each concept follows this shape:

> **Why do we even need this?** One or two sentences of motivation — the problem this concept solves.
> Plain-language explanation first. *Then* the equation:
> `L = (1/N) Σ ‖x_i − x̂_i‖²` — "the average squared distance between each point and its reconstruction."
> **In practice:** what this means for the code you're about to write (e.g., "small L means the reconstruction is faithful; it can never be negative").

**Quality target — a good 🔧 CORE stub.** Signature, type hints, a docstring stating the contract, and pseudocode that *guides the approach without giving the answer*:

```python
def normalize_points(points: np.ndarray) -> tuple[np.ndarray, np.ndarray, float]:
    """Center points at the origin and scale so the mean distance to the origin is 1.

    Args:
        points: (N, 3) array of 3D points.
    Returns:
        (normalized_points, centroid, scale) — apply the inverse to recover originals.

    🔧 CORE — you implement this.
    """
    # 1. Compute the centroid (mean over the N axis).
    # 2. Subtract it so the cloud is centered.
    # 3. Compute the mean distance of the centered points to the origin.
    # 4. Divide by that distance so the mean distance becomes 1.
    # Return the transformed points plus the centroid and scale you used.
    raise NotImplementedError
```

Note what the pseudocode does and doesn't do: it names the *steps* and the *return contract*, but never writes the array operations. That is the line you hold for every 🔧 CORE function.

### 2. Test file
Write `tests/test_module_NN.py` with all test cases specified in the module document. Tests come BEFORE the functions they verify (test-first). Use fixtures from `conftest.py` where applicable.

Test bar:
- **Every 🔧 CORE function gets at least one numerical or invariant test with known expected values** — not just a shape check. Pick inputs whose correct output you can compute by hand or cite, so a wrong implementation actually fails.
- Tests must **import cleanly** and, before the student implements anything, fail only with `NotImplementedError` — never `ImportError`, `SyntaxError`, or a collection error.
<!-- SLOT:domain_test_focus — fill: 2–4 test categories or failure modes specific to this course's domain (e.g., "rotation matrices stay orthonormal", "probabilities sum to 1") -->
- Cover the categories that matter for this domain: numerical assertions, shape/type checks, invariant checks, and visual verification (saved plots) where relevant.
<!-- /SLOT:domain_test_focus -->

### 3. Source file updates
Update the appropriate source file(s):
<!-- SLOT:source_map_ref — fill: the concrete package path (e.g., `src/recon/geometry.py`) for this module, from the Module-to-Codebase Mapping table -->
- The file(s) for this module per the Module-to-Codebase Mapping table in `docs/module_guidelines.md`.
<!-- /SLOT:source_map_ref -->
- Add 🔧 CORE function stubs: signature, type hints, full docstring, pseudocode comments, `raise NotImplementedError` — but NOT the implementation body.
- Add ⚙️ INFRASTRUCTURE functions: implement these fully.
- Add necessary imports.

**The CORE vs INFRASTRUCTURE line:** 🔧 CORE is *the idea the student must derive and implement* — the math, the algorithm, the thing the module exists to teach. ⚙️ INFRASTRUCTURE is everything that sets it up or shows the result: I/O, data loading, format conversion, plotting, config parsing, glue. When unsure, ask "is understanding *this* the point of the module?" If yes → CORE (stub only). If it's plumbing around the point → INFRASTRUCTURE (implement it).

### 4. Runner script
Write `scripts/run_module_NN.py` — the end-to-end entry point for the module's integration checkpoint. It imports from the project library, calls the relevant functions, and saves outputs to `outputs/module_NN/`.

### 5. Self-check (guarded — run code only if the environment is set up)
After writing the files, verify the scaffolding holds together. **Only run code if the project's environment/dependencies are already installed** (e.g., a virtualenv exists and imports resolve); otherwise skip this and print a one-line note telling the student to run it after `pip install`.

If the environment is set up:
- Run `pytest tests/test_module_NN.py --collect-only` — confirm every test is collected with no import or collection errors.
- Import-smoke-check the new module and runner (e.g., `python -c "import <module>; import scripts.run_module_NN"`).
- Optionally run `pytest tests/test_module_NN.py` and confirm the 🔧 CORE tests fail with `NotImplementedError` — that is the *correct* pre-implementation state.

Report a short summary: which checks passed, which were skipped and why. **Never implement a 🔧 CORE function body to make a test pass** — failing CORE tests are the expected starting point for the student.

## Quality checks before finishing

Run through the checklist from the module guidelines (keep this list in sync with `module_guidelines.md` §8):
- Every concept motivated before introduced
- Every equation has plain-English preamble and practical note
- Every code block tagged 🔧 CORE, ⚙️ INFRASTRUCTURE, or ▶️ RUN
- Core functions have signature, type hints, docstring, guiding (non-solving) pseudocode, and a numerical/invariant test
- Infrastructure tasks describe purpose and interface
- Tests specified before implementations and import cleanly (fail only with `NotImplementedError`)
- Verification checkpoints after each implementation section
- Integration checkpoint runs full pipeline
- Deliverables list files with exact paths
- Key resources have section-level specificity
- Module document specifies which files it creates/extends
- Self-check run (or explicitly skipped with a note)
- No notebooks — visualization via saved plots, web viewers, or rendered videos
- No 🔧 CORE function bodies implemented — only stubs
