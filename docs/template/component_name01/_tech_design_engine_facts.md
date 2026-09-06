# {ComponentName} ({component_code_name}) Technical Design · Engine Facts Register ({EngineName} {EngineVersion})
Version: v0.1.0
Created by: {Author}
Created at: {YYYY-MM-DD}

<!--
TEMPLATE NOTES (delete this block when using)
- Purpose: a **volume** of `{component_code_name}_tech_design.md` that registers every behavioural fact of a
  third-party engine / library that the component's design relies on (physics engine, database engine,
  rendering library, message broker …). Each fact gets an `EF-nn` id; design, drafting, review and
  implementation task books cite the id and **never open the engine source again**.
- This is NOT a summary of the engine. Only facts that a design clause depends on are registered.
- Usage: copy to `docs/components/{component_code_name}/{component_code_name}_tech_design_engine_facts.md`,
  add a row to the main document's §1.0 file index, replace placeholders, delete this block.
- Origin: a real project measured that four consecutive review rounds each re-verified the same ten
  engine facts in the engine source; after the register existed, later rounds cited ids instead.
-->

> **Document position**: this file is a volume of [{component_code_name}_tech_design.md]({component_code_name}_tech_design.md) with the same authority level. Engine version is pinned to **{EngineName} {EngineVersion}**; when the engine is upgraded every row is re-verified. **Design and review cite `EF-nn` ids and do not read the engine source**; a new fact is added only through a one-off verification task dispatched by the Chief Architect (file + line range given), and a fact that is pinned by a unit test uses the test name as the authority.

---

## 1 API Surface Actually Used

Only the engine classes / methods / settings the component's adapter layer really calls, grouped by header. This is the whole surface anyone needs to know.

| Header / module | Class / method / setting | Used for | Design section |
|---|---|---|---|
| `{engine/path/Header1.h}` | `{Class::Method}` | {purpose} | §{x.y} |
| `{engine/path/Header2.h}` | `{Setting}` (**the only setting that deviates from the engine default**) | {purpose} | §{x.y} |

---

## 2 Facts Register

One row per fact. A fact statement must be a single sentence that can be true or false.

### 2.1 {Topic group 1, e.g. body types and activation}

| Id | Fact statement | {EngineName} {EngineVersion} source location | Our chosen setting / usage | Design clauses depending on it | First verified in | Pinned by test |
|---|---|---|---|---|---|---|
| **EF-01** | {one falsifiable sentence} | `{path/File.cpp}:{line}`; `{path/Other.h}:{line}` | {setting or usage} | §{x.y} ({volume name}) | {component_code_name}/vA.B.C review R1 §0 | `{test_name}` / "suggested: `{test_name}`" / "not testable" |
| **EF-02** | … | … | … | … | … | … |

### 2.2 {Topic group 2, e.g. force injection and reset}

| Id | Fact statement | Source location | Our chosen setting / usage | Design clauses | First verified in | Pinned by test |
|---|---|---|---|---|---|---|
| **EF-03** | … | … | … | … | … | … |

---

## 3 Pending Verification and Pending Test Pinning

### 3.1 Pending verification (cited by the design, no verified source location yet)

| Id | Statement | Why it is pending | Proposed one-off verification task |
|---|---|---|---|
| EF-{nn} | … | {reports disagree / no line given / only circumstantial evidence} | {file + line range to read; expected outcome} |

### 3.2 Facts recommended for pinning by unit tests

| Id | Test name | What the test asserts |
|---|---|---|
| EF-{nn} | `{test_name}` | … |

---

## 4 Maintenance Rules

| # | Rule |
|---|---|
| 1 | Rows are appended, never renumbered; a fact that turns out false is marked **REVOKED** with the date and the replacing row id. |
| 2 | Engine upgrade: every row is re-verified; line numbers may change, test names must not. Tests are the primary authority once they exist. |
| 3 | A new fact enters only via a one-off verification task whose report gives file + line and a falsifiable statement; the Chief Architect verifies the row before it is committed. |
| 4 | Task books cite `EF-nn`; a review that needs a fact not present here lists it under §3.1 and hands it to the Chief instead of opening the engine source. |

---

## Change Log

| Version | Date | Author | Change |
|---|---|---|---|
| v0.1.0 | {YYYY-MM-DD} | {Author} | Created: API surface, {n} facts in {m} groups, pending items, maintenance rules |
