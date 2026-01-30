# Proposed edits for Graph API Overview (v3.0)

## Goals

This revision targets developers who are already familiar with LangGraph concepts and want:
- Faster navigation
- Clearer separation of conceptual vs operational concerns
- Less narrative repetition
- More precise language around control flow, state updates, and execution semantics

The current page is comprehensive but dense. The proposed changes improve scanability, reduce cognitive load, and clarify advanced behavior without removing detail.

---

## High-level structural changes

### 1. Add an explicit “What this page covers” section at the top

**Why**
Developers landing on this page need immediate orientation. Currently, the page dives straight into conceptual explanation without framing.

**Change**
Add a short section after the title that answers:
- Who this page is for
- What is in scope
- What is intentionally not covered

---

### 2. Move execution model (super-steps, Pregel-style execution) into its own section

**Why**
The execution model is important but currently interrupts the flow of the core mental model (State → Nodes → Edges). It should be explicitly labeled as *execution semantics*.

**Change**
- Create a new section: `## Execution model`
- Move message passing + super-step explanation there
- Reference it briefly from the Graphs section

---

### 3. Reorder major sections to match how developers build graphs

**Current order**
Graphs → State → Nodes → Edges → Advanced control flow → Runtime

**Proposed order**
1. Graphs (mental model)
2. State (schema + reducers)
3. Nodes (what runs)
4. Edges & control flow (how execution moves)
5. Advanced routing (`Send`, `Command`)
6. Runtime & execution controls
7. Visualization & migrations

This mirrors the actual graph authoring workflow.

---

### 4. Tighten and normalize terminology

**Issues**
- “Node” vs “node”
- “Edges define how logic is routed” vs “control flow”
- Redundant explanations of reducers and state updates

**Change**
- Use consistent capitalization when referring to API concepts
- Prefer concrete verbs (“routes”, “updates”, “emits”) over abstractions
- Eliminate repeated explanations once a concept is introduced

---

### 5. Reduce narrative repetition in reducers and messages sections

**Why**
Reducers are explained multiple times in slightly different ways. Messages get reintroduced repeatedly.

**Change**
- Explain reducers once, clearly
- Reference back instead of re-explaining
- Emphasize *when* you need a reducer vs *which* reducer to use

---

## Section-by-section copy edits (highlights)

### Title + intro

**Before**
> Graph API overview

**After**
> Graph API overview  
> *Core concepts and execution semantics for building LangGraph workflows*

---

### Graphs section

**Edits**
- Shorten definition
- Replace rhetorical phrasing (“The real power, though…”) with concrete claims
- Add one-sentence summary callout

**Add**
> **Mental model:** Nodes update state. Edges decide what runs next. The graph coordinates execution.

---

### Execution model (new section)

**Move**
- Message passing explanation
- Pregel reference
- Super-step definition

**Edit**
- Clarify that this is an implementation detail most users don’t need daily
- Emphasize implications: parallelism, determinism, recursion limits

---

### State section

**Edits**
- Clarify “State = schema + reducers” up front
- Reduce verbosity in TypedDict vs Pydantic discussion
- Make private/internal state rules more explicit

**Add**
- One short “Rules of thumb” list:
  - Nodes can write any declared channel
  - Reducers control merge semantics
  - Input/output schemas are filters, not separate states

---

### Reducers

**Edits**
- Remove duplicated “Nodes don’t need to return full state” explanation
- Explicitly contrast default reducer vs annotated reducer
- Move Overwrite into a short “Escape hatch” subsection

---

### Nodes section

**Edits**
- Emphasize function signature flexibility
- Move RunnableLambda explanation into a short “Behind the scenes” note
- Clarify when `runtime` vs `config` should be used

---

### Edges section

**Edits**
- Rename section to “Edges and control flow”
- Explicitly distinguish:
  - Static routing
  - Conditional routing
  - Dynamic routing (`Command`, `Send`)

---

### `Send` and `Command`

**Edits**
- Add a comparison table:
  - Conditional edges vs Send vs Command
- Clarify that `Command` does not suppress static edges
- Add “Common mistakes” callouts

---

### Runtime context & recursion

**Edits**
- Group recursion limit, step counter, and RemainingSteps into one coherent section
- Reduce repeated examples
- Clearly recommend proactive handling as default

---

### Visualization & migrations

**Edits**
- Move migrations after visualization
- Treat both as “operational concerns”

---

## Summary of impact

These edits:
- Preserve all existing technical depth
- Improve discoverability for experienced users
- Reduce reading time without reducing information
- Make advanced behavior easier to reason about

This version is better aligned with how LangGraph is actually used in production systems.
