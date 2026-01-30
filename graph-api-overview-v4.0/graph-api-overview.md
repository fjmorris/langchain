@@
-# Graph API overview
+# Graph API overview

+This section documents the core concepts and APIs used to build LangGraph workflows.
+
+If you are new to LangGraph, start with **Graph API Concepts**.
+If you are looking for specific APIs, see **Graph API Reference**.
+
+- 📘 [Graph API Concepts](./graph-api-concepts.md)
+- 📚 [Graph API Reference](./graph-api-reference.md)
+
+---
+
+## What is a LangGraph graph?
+
 LangGraph models agent workflows as graphs composed of:
 
-- State
-- Nodes
-- Edges
+- **State** – shared, typed application data
+- **Nodes** – functions that perform work
+- **Edges** – control-flow rules
 
-By composing Nodes and Edges, you can create complex, looping workflows.
+Nodes update state. Edges decide what runs next.
+
+For a deeper explanation of execution semantics, state reducers, and runtime behavior, see **Graph API Concepts**.
