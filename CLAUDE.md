# {ProductName} — AI Team Working Rules (CLAUDE.md template)

<!--
TEMPLATE NOTES (delete this block when using)
- Purpose: the project-root `CLAUDE.md` that every Claude Code session loads. It defines the AI team
  (roles + models), concurrency and task-granularity discipline, the document authority chain, and the
  quota discipline that keeps subagent cost under control.
- Usage: copy to the project root as `CLAUDE.md`, replace every `{curly brace}` placeholder, delete
  this block. Keep it short — every line here is loaded into every session.
- Origin: distilled from a real multi-component project (2026-09) after measuring that ~75% of spend
  went to Opus subagents re-reading large documents and third-party engine sources.
-->

## Roles and Models

Role naming format: **FirstName-JobTitle-ModelName**.

| Role | Name | Model | Definition | Notes |
|---|---|---|---|---|
| **Chief Architect** | {Chief}-ChiefArchitect-{ChiefModel} | {ChiefModel} (the main session itself) | no agent file | Final design authority and ruling execution; **must not spawn other instances of its own model** |
| Architect | Oscar-Architect-Opus | Opus | `.claude/agents/Oscar-Architect-Opus.md` | Small architecture tasks, code review, **adversarial document review**, design drafting per rulings |
| Frontend Engineer | Elli-FEEngineer-Sonnet | Sonnet | `.claude/agents/Elli-FEEngineer-Sonnet.md` | Frontend / visualisation code |
| Backend Engineer | Ben-BEEngineer-Sonnet | Sonnet | `.claude/agents/Ben-BEEngineer-Sonnet.md` | Backend / core implementation |
| Test Development Engineer | Tina-TestDevEngineer-Sonnet | Sonnet | `.claude/agents/Tina-TestDevEngineer-Sonnet.md` | Test cases, scenarios, verification scripts |
| Test Execution Engineer | Ryan-TestExecEngineer-Sonnet | Sonnet | `.claude/agents/Ryan-TestExecEngineer-Sonnet.md` | Build / test execution and numeric cross-checks |

## Concurrency and Discipline

- **Concurrency limits are set by the engineer and may change at any time with the token quota; the latest instruction in `prompt_history.md` wins.** Current: **{N_Opus} Opus + {N_Sonnet} Sonnet**. Haiku is **not allowed**. A concurrency change never rewrites the dev plan — only the dispatch cadence.
- **Resumed instances count toward the cap**: before dispatching, the Chief counts every in-flight instance — including ones resumed via SendMessage — against the current limit; an idle instance must not be resumed if doing so would exceed the cap.
- **Acceptance-window discipline**: while an acceptance/evidence run (`<acceptance_run>`) is in progress, the Chief must not dispatch any task that modifies repository files, including documentation drafting — such edits would leave the run's recorded `git_commit` dirty. Drafting that must proceed in parallel is stashed or moved to a separate worktree. Formatting fixes (e.g. `clang-format`) change the binary hash, so L1 formatting checks must close **before** the frozen build used as evidence.
- The Chief Architect **verifies** every role's output (never accepts a report at face value) and spot-checks in person from time to time.
- Roles never modify finalized design documents under `docs/` and never widen their own scope; design problems are reported, not fixed unilaterally.
- **Task granularity**: each subagent task ≈ **10 minutes** of work; a task running past **20 minutes** is terminated, progress inventoried (`git status` / artefact list), and re-dispatched.
- The Chief Architect does not write production code; code goes to subagents. The Chief does architecture, task books, verification, spot checks and git.
- Every engineer prompt is archived **verbatim** in `docs/ai_dev/prompt_history.md`; every task ends with a commit.

## Quota Discipline (subagent cost control)

Opus cost is dominated by *large context × many tool calls* (cache re-reads), so:

1. **Reviews read only the diff and re-use the instance**: design/plan documents get a full R1 review from a **fresh Opus instance**; from R2 on, the review covers only the diff and its knock-on effects, and **re-uses the R1 instance** (resume it with its agent id). Independence comes from "never sees the drafting process", not from a new instance every round.
2. **Third-party engine facts are verified once and then cited by ID**: the only reference point for engine/library behaviour facts is the component volume `{component_code_name}_tech_design_engine_facts.md` (`EF-nn` ids). Design, drafting, review and implementation task books cite EF ids and **never read the engine source**. When a design touches an engine area not yet registered, the Chief dispatches a one-off verification task (file + line range given) that appends EF entries first; facts that can be pinned by a unit test use the test name as the authority.
3. **Convergence**: a review round with no Blocker and ≤ 3 Major issues is closed by the Chief after verifying the revision — no further round.
4. **Code review**: r1 is a full Opus review; when r1 finds no Blocker, r2 is a Sonnet closure check (verify each CR item fixed, build and tests pass). If r1 found a Blocker, r2 stays Opus and reviews only the fix diff.
5. **Task books and knowledge packs**: every dispatch follows the skeleton and templates in `docs/ai_dev/99_Prompts/_TaskBook.md` (volume-level read list, do-not-read list incl. engine sources and other volumes, **shared knowledge pack**, tool-call budget with implementation ≤ 35, tool-call count in every report, instance re-use from R2 on). The five role files in `.claude/agents/` carry a **resident knowledge pack** (build/test commands, toolchain, coding-standard digest, file map, known pitfalls, review conventions) maintained by the Chief per version, so subagents never cold-start by exploring.
6. **Document size**: a technical design that grows large is split into a main document (§1.0 file index + core concepts) and **volumes** (`{component_code_name}_tech_design_{volume}.md`, chapter numbers globally unique); large platform `tech_` documents are split the same way (see `project_directory_plan.md`). Task books point to the volume and section, never to "the whole design".
7. **Smoke runs use the production KPI functions**: a smoke test or first run calls the official KPI/orchestration functions in `<kpi_script>`, never an ad-hoc parallel script — ad-hoc scripts have produced false FAILs/PASSes (quantized positions, wrong field names, missing unit conversion, window-edge frames). KPI wiring code is self-checked against the documented data-source rules and receives an independent review; a "no Blocker in r1" result does not imply "no Blocker in r2" when r2 covers new scope.
8. **Parallel instances never share a build directory**: when several implementation subagents run concurrently, each builds only its own target, or the Chief runs a single build after all of the wave's commits have landed — a shared CMake/ninja build tree has produced `Permission denied` errors and false failures from concurrent writes to the same tree. Task books for a parallel wave state which target each instance is allowed to build.
9. **Smoke runs and self-checks never leave KPI records in the official evidence directory**: task books instruct that any records produced by a smoke run or self-check be moved out of `{kpi_results_dir}` into a temporary directory afterward, and that the tracked history file (`{kpi_history_file}`) be restored to its committed state; only the formal acceptance run performed by the test-execution role writes into the evidence directory.
10. **Check the upstream default branch and contribution agreement before an upstream pull request**: a patch written against a pinned release commit may no longer apply cleanly to the current default branch after upstream refactoring, and many projects require a CLA/FLA that only the engineer (a human) can sign — the Chief prepares the branch and PR text and reminds the engineer to sign it, never signs on the engineer's behalf.

## Document Authority Chain (everyone)

Platform level (`technical_overview.md` + `tech_*` topic documents + coding standard) > component `{component_code_name}_tech_design.md` (**including its volumes**, chapter numbers globally unique; read only the volume you need via the main document's §1.0 index) > code. Business semantics: the `business_overview*` documents are the sole authority. Directory layout: `project_directory_plan.md`. Version numbers and tags: `version_plan.md`; write versions **in full** — component versions as `{component_code_name}/vA.B.C`, platform versions as `platform/vA.B.C`, never a bare number. Product name always in full: **{ProductName}**. Design documents record **final state only** and never point at process documents (`docs/ai_dev/`).
