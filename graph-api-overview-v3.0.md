# Graph API overview
*Core concepts and execution semantics for building LangGraph workflows*

## What this page covers

This page explains the core primitives of the LangGraph Graph API and how they work together at runtime.

**This page is for you if you:**
- Already understand LangGraph at a high level
- Are building non-trivial graphs with branching, loops, or multiple agents
- Want precise control over state updates and execution flow

**Out of scope:** step-by-step tutorials and end-to-end examples. See the how-to guides for those.

---

## Graphs

LangGraph models agent workflows as graphs composed of three primitives:

1. **State** – A shared, typed data structure that represents the current snapshot of the application.
2. **Nodes** – Functions that perform work and emit state updates.
3. **Edges** – Control-flow rules that determine which nodes execute next.

Nodes update state. Edges decide what runs next. The graph coordinates execution.

Nodes and edges are just Python functions. They may call LLMs, tools, or regular code.

---

## Execution model

LangGraph executes graphs using a message-passing model inspired by Google’s Pregel system.

At runtime:
- Nodes receive state updates via incoming edges
- Active nodes execute in parallel within a **super-step**
- Each node emits updates that are applied via reducers
- Execution continues until no nodes remain active

Most users don’t need to think about this model day-to-day, but it explains:
- Why multiple nodes can run in parallel
- How loops and recursion work
- Why recursion limits exist

---

## State

Graph state consists of:
- A **schema** defining allowed state channels
- **Reducer functions** that define how updates are merged

All nodes read from and write to the same logical state. Input and output schemas act as filters, not separate states.

### Schema

State schemas are typically defined using `TypedDict` or dataclasses. Pydantic models are supported when validation is required, but come with a performance cost.

You may define:
- An internal schema (all state channels)
- An input schema (accepted inputs)
- An output schema (returned outputs)

Nodes may write to any channel defined in the internal schema, regardless of their input type annotation.

#### Private and internal state

Nodes may also write to additional internal channels as long as a schema exists. This allows private communication between nodes without exposing keys in graph input/output.

---

### Reducers

Each state key has an independent reducer that determines how updates are applied.

- **Default reducer:** overwrite the existing value
- **Annotated reducer:** merge updates using a custom function

Reducers control how parallel updates are resolved and are critical for correctness.

#### Overwrite (escape hatch)

Use `Overwrite` to bypass a reducer and replace a value directly when needed.

---

## Working with messages

Storing message history in state is common for LLM-based graphs.

Use the `add_messages` reducer to:
- Append new messages
- Update existing messages by ID
- Automatically deserialize message payloads

For convenience, LangGraph provides `MessagesState`, which defines a `messages` channel with the correct reducer.

---

## Nodes

Nodes are Python functions (sync or async) with flexible signatures:

- `state` – current graph state
- `config` – runtime configuration and metadata
- `runtime` – access to runtime context, store, and streaming

Nodes return **partial state updates**, not the full state.

Behind the scenes, node functions are wrapped as `RunnableLambda` objects, enabling batching, async execution, and tracing.

---

## Edges and control flow

Edges determine how execution moves through the graph.

### Static edges

Always route from one node to another.

### Conditional edges

Route dynamically based on the current state. The routing function returns:
- A node name
- A list of node names
- A mapped value via a lookup table

All routed nodes execute in parallel.

---

## Dynamic routing

### `Send`

Use `Send` when the number of downstream invocations is dynamic or when each invocation requires its own state.

This is common in map-reduce patterns.

---

### `Command`

Use `Command` when a node must:
- Update state **and**
- Decide where execution goes next

`Command` adds dynamic routing but does **not** suppress static edges.

**Rule of thumb**
- Use conditional edges for routing only
- Use `Command` when routing and updating must happen together

---

## Runtime context and execution limits

### Runtime context

Runtime context lets you pass non-state configuration (e.g., model provider, DB handles) into nodes without polluting graph state.

### Recursion limits

Graphs execute in super-steps. The recursion limit caps how many steps may run in a single execution.

Use the `RemainingSteps` managed value for proactive handling and graceful degradation.

Reactive handling via `GraphRecursionError` is supported but less flexible.

---

## Visualization

LangGraph provides built-in graph visualization tools to inspect structure and control flow. These are especially useful for debugging complex graphs.

---

## Graph migrations

LangGraph supports safe evolution of graph definitions when using checkpoints.

Topology changes are fully supported for completed threads. Interrupted threads have limited constraints to ensure correctness.

---

<Callout icon="pen-to-square" iconType="regular">
  Edit this page on GitHub or file an issue.
</Callout>
