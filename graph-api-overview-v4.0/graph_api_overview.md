# Graph API overview

This section documents the core concepts and APIs used to build LangGraph workflows.

LangGraph models agent workflows as graphs composed of stateful nodes connected by explicit control flow. While the Graph API is powerful and flexible, most users interact with a small set of core primitives.

Use the pages below depending on your goal:

- 📘 **Graph API Concepts** – mental models, execution semantics, and design patterns
- 📚 **Graph API Reference** – API surface area, methods, and quick lookup tables

---

## What is a LangGraph graph?

A LangGraph graph consists of three primitives:

- **State** – a shared, typed data structure representing the current snapshot of the application
- **Nodes** – functions that perform work and emit state updates
- **Edges** – control-flow rules that determine which nodes execute next

Nodes update state. Edges decide what runs next. The graph coordinates execution.

Nodes and edges are plain Python functions. They may call LLMs, tools, databases, or standard application logic.

---

## How to use this documentation

If you are:

- **Learning how LangGraph works internally** → start with **Graph API Concepts**
- **Looking up a specific API or pattern** → jump to **Graph API Reference**

This overview intentionally stays lightweight and links out to deeper explanations.

---

## Related guides

- State schemas and reducers
- Control flow and routing patterns
- Runtime context and recursion handling
- Visualization and debugging

For detailed explanations and examples, continue to the Concepts and Reference pages.

