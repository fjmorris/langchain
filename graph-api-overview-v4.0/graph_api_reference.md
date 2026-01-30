# Graph API Reference

This page provides a quick-reference view of the LangGraph Graph API. It is intended for experienced users who want to look up specific APIs, patterns, or behaviors.

---

## Graph construction

| API | Purpose | Notes |
|---|---|---|
| `StateGraph(State, …)` | Create a new graph | `State` defines internal schema |
| `compile()` | Validate and finalize graph | Required before execution |
| `set_entry_point(node)` | Set initial node | Alternative to `START` edge |
| `set_finish_point(node)` | Set terminal node | Alternative to `END` edge |

---

## Nodes

| API / Pattern | Description | When to use |
|---|---|---|
| `add_node(name, fn)` | Register a node | Default pattern |
| `add_node(fn)` | Auto-named node | Small graphs |
| Node returns `dict` | Emit state update | Most nodes |
| Node returns `Command` | Update + route | Dynamic control flow |

Nodes may accept `state`, `config`, and/or `runtime` parameters.

---

## Edges and routing

| API | Description | Parallel execution |
|---|---|---|
| `add_edge(a, b)` | Static routing | No |
| `add_conditional_edges(a, fn)` | Dynamic routing | Yes |
| `START → node` | Entry point | No |
| `node → END` | Terminal edge | No |

If multiple edges fire, all destination nodes execute in parallel.

---

## Dynamic execution primitives

| Mechanism | Use case | Key behavior |
|---|---|---|
| `Command` | Update state and route | Adds dynamic edges |
| `Send` | Fan-out / map-reduce | Isolated state copies |
| Conditional edges | Branching | No state update |

---

## State and reducers

| Pattern | Reducer | Behavior |
|---|---|---|
| Default | overwrite | Last write wins |
| `Annotated[..., add]` | merge | Append / combine |
| `add_messages` | message-aware | ID-based updates |
| `Overwrite` | bypass | Ignore reducer |

Reducers apply independently per state key.

---

## Messages

| Tool | Purpose | Notes |
|---|---|---|
| `MessagesState` | Prebuilt message state | Uses `add_messages` |
| `add_messages` | Merge message lists | Handles IDs |

Messages are automatically deserialized into LangChain message objects.

---

## Runtime and execution

| Feature | Access | Purpose |
|---|---|---|
| Runtime context | `runtime.context` | Non-state config |
| Metadata | `config["metadata"]` | Debugging |
| Recursion limit | `invoke(config=…)` | Execution cap |
| Remaining steps | Managed value | Proactive safety |

---

## Caching

| API | Purpose | Notes |
|---|---|---|
| `cache_policy` | Enable node caching | Set per node |
| Cache key | Input hash | Customizable |
| TTL | Expiration | Optional |

---

## Visualization and tooling

| Feature | Description |
|---|---|
| Graph visualization | Inspect structure and flow |
| Checkpointers | Persist execution state |
| Tracing | Debug and observe |

---

## Common patterns

- Multi-agent handoffs via `Command`
- Map-reduce via `Send`
- Loop control via conditional edges
- Graceful exits using recursion monitoring

For conceptual explanations, see **Graph API Concepts**.

