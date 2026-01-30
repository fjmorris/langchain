+## Execution model
+
+LangGraph executes graphs using a message-passing model inspired by Google Pregel.
+
+- Nodes execute in parallel within super-steps
+- State updates are merged via reducers
+- Execution halts when no nodes remain active
+
+This model explains parallelism, loops, and recursion limits.

+## State
+
+State consists of:
+- A schema (keys and types)
+- Reducers (merge behavior)
+
+Input and output schemas act as filters over the internal state.

+## Control flow
+
+Edges define execution order.
+Dynamic routing is achieved via conditional edges or `Command`.
