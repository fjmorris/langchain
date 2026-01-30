# Graph API Concepts

This page explains the conceptual model behind the LangGraph Graph API. It focuses on how graphs execute, how state flows through the system, and how control flow is expressed.

---

## Core mental model

A LangGraph graph is a stateful program:

- **Nodes** perform work and emit updates
- **Edges** control execution order
- **State** accumulates results over time

At runtime, the graph repeatedly:
1. Activates nodes whose inputs are ready
2. Executes those nodes (possibly in parallel)
3. Applies state updates via reducers
4. Routes execution along edges

Execution stops when no nodes remain active.

---

## Execution model

LangGraph executes graphs using a message-passing model inspired by Google’s Pregel system.

### Super-steps

Execution proceeds in discrete **super-steps**:

- Nodes that run in parallel belong to the same super-step
- Nodes that depend on prior output run in later super-steps

Each super-step consists of:
- Receiving state updates via incoming edges
- Executing node logic
- Emitting new updates

The graph halts when no messages remain in transit and all nodes are inactive.

### Why this matters

Understanding the execution model helps explain:

- Why multiple nodes can run concurrently
- How loops and retries are expressed
- Why recursion limits exist

Most users do not need to reason about super-steps directly, but they affect performance and control flow.

---

## State

Graph state represents the shared memory of the application.

State consists of:
- A **schema** defining valid keys and types
- **Reducers** defining how updates are merged

All nodes read from and write to the same logical state.

### Input, output, and internal state

LangGraph supports three logical views over state:

- **Input schema** – keys accepted when invoking the graph
- **Output schema** – keys returned when execution completes
- **Internal schema** – the full set of keys used during execution

Input and output schemas act as filters over the internal state; they are not separate states.

### Private state channels

Nodes may communicate via internal or private state channels that are not exposed as graph inputs or outputs. As long as a schema exists, nodes may write to those channels.

---

## Reducers

Reducers define how concurrent or sequential updates to a state key are applied.

Each state key has exactly one reducer.

### Default reducer

If no reducer is specified, updates overwrite the previous value.

### Custom reducers

Reducers may be specified using `typing.Annotated` to merge values instead of overwriting them. This is commonly used for:

- Appending to lists
- Accumulating messages
- Combining partial results

Reducers are applied independently per key.

### Overwrite

The `Overwrite` type allows bypassing a reducer and directly replacing a value. This should be used sparingly as an escape hatch.

---

## Control flow

Control flow is defined by edges.

- **Static edges** always route execution to the same node
- **Conditional edges** route based on state

If multiple edges fire, all destination nodes execute in parallel in the next super-step.

---

## Dynamic routing

### Command

`Command` allows a node to:

- Emit state updates
- Dynamically route execution

This is useful when routing decisions depend on newly produced data. `Command` adds dynamic edges but does not suppress static edges.

### Send

`Send` enables dynamic fan-out by spawning multiple downstream executions with isolated state. This pattern is commonly used for map-reduce workflows.

---

## Runtime context

Runtime context allows passing configuration or dependencies to nodes without storing them in graph state. Examples include:

- Model providers
- Database connections
- Feature flags

Runtime context is accessible via the `runtime` argument in node functions.

---

## Recursion and limits

Graphs execute in super-steps, which may loop indefinitely if not bounded.

LangGraph enforces a recursion limit to prevent runaway execution. This limit can be:

- Monitored proactively using managed values
- Handled reactively by catching execution errors

Proactive handling allows graceful degradation and controlled exits.

---

## Operational concerns

### Visualization

Graph visualization tools help inspect structure, routing, and execution paths.

### Migrations

LangGraph supports evolving graph definitions over time, including changes to:

- Nodes
- Edges
- State schemas

Completed threads allow full topology changes. Interrupted threads have limited constraints to ensure correctness.

