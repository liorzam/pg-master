── Page 1 ──

# Writing Effective Tools for Agents

**Authors:** Ken Aizawa, Barry Zhang, Zachary Witten, Daniel Jiang, Sami Al-Sheikh, Matt Bell, Maggie Vo, Theodora Chu, John Welsh, David Soria Parra, Adam Jones, Santiago Seira, Molly Vorwerck, Drew Roper, Christian Ryan, and Alexander Bricken

**Source:** Anthropic Engineering Blog
**Published:** September 11, 2025

---

## Chapter 1: The Fundamental Challenge

Agents are only as effective as the tools we give them. This is the core insight that drives this guide: when we build tools for AI agents, we face a fundamentally different design challenge than when building APIs for human developers or traditional software systems.

In a deterministic system, behavior is predictable. Given an input, the system produces a known output every time. But agents operate in a non-deterministic world. When a user asks "Should I bring an umbrella today?", an agent might call the weather tool, answer from general knowledge, or even ask a clarifying question about location first. The path is not predetermined.

This non-determinism means that tool design for agents requires a different philosophy. The good news is that effective agent-friendly tools turn out to be surprisingly intuitive to grasp as humans once we understand the principles behind them. The patterns that make tools work well for agents often mirror what makes tools work well for people: clarity, simplicity, and appropriate abstraction.

---

## Chapter 2: The Three-Phase Development Process

Building effective tools for agents follows a systematic three-phase methodology: prototyping, evaluation, and optimization. Each phase builds on the previous one, creating a feedback loop that progressively improves tool quality.

### Phase 1: Prototyping

The first phase is about getting something working quickly. Build rapid implementations using tools like Claude Code. Leverage LLM-friendly documentation such as llms.txt files to help bootstrap the process. Wrap your initial tools in local MCP (Model Context Protocol) servers or Desktop extensions for immediate testing.

The goal at this stage is not perfection but exploration. You want to understand the shape of the problem, discover edge cases, and build intuition about how agents will interact with your tools.

### Phase 2: Evaluation

Evaluation is where rigor enters the process. Create comprehensive evaluation tasks grounded in real-world scenarios. Generate dozens of prompt-response pairs that cover the full range of expected agent behavior. Run evaluations programmatically with simple agentic loops.

The quality of your evaluation tasks matters enormously. There is a critical distinction between strong and weak evaluation tasks.

**Strong evaluation tasks** require multiple tool calls and reflect realistic complexity:

> "Schedule a meeting with Jane next week to discuss our latest Acme Corp project. Attach the notes from our last project planning meeting and reserve a conference room."

This task requires the agent to search for contacts, check calendars, find documents, and coordinate resources -- all through tool calls.

**Weak evaluation tasks** are overly simplistic and do not exercise real-world behavior:

> "Schedule a meeting with jane@acme.corp next week."

This task hands the agent everything it needs on a silver platter and does not test the agent's ability to resolve ambiguity or orchestrate multiple operations.

### Phase 3: Optimization

In the optimization phase, you analyze agent behavior transcripts to understand where tools succeed and fail. Collaborate with Claude Code to refactor implementations based on observed patterns. Validate improvements against held-out test sets to ensure that optimizations generalize.

This phase is iterative. Each round of analysis reveals new insights that feed back into the design.

---

## Chapter 3: Choosing the Right Tools

Not all functionality needs to become a tool. One of the most important design decisions is determining what should be a tool and what should not. More tools do not always lead to better outcomes.

Agents face context constraints that traditional software does not. Every tool definition, every parameter description, and every response consumes tokens in the agent's context window. This means that tool design must be thoughtful about what to include and what to leave out.

### Search Over Listing

In many cases, search-based tools are preferable to comprehensive listing tools. Consider the difference:

- **list_contacts**: Returns all contacts, potentially hundreds or thousands of entries. Consumes enormous context and forces the agent to filter results.
- **search_contacts**: Takes a query parameter and returns only relevant results. Efficient, focused, and context-friendly.

The search-based approach respects the agent's constraints while still providing the functionality needed.

### Consolidation

Tools should consolidate related functionality. Multiple API calls can be wrapped into single, higher-level operations. Rather than forcing an agent to orchestrate a chain of low-level calls, provide a single tool that handles the orchestration internally.

Examples of effective consolidation:

| Instead of... | Use... |
|---|---|
| `list_users` + `list_events` + `create_event` | `schedule_event` |
| `read_logs` with manual filtering | `search_logs` with built-in filters |
| Separate customer, transaction, and notes lookups | `get_customer_context` combining all three |

Each consolidation reduces the number of tool calls required, decreases context consumption, and lowers the chance of errors in the orchestration chain.

---

## Chapter 4: Namespacing and Organization

As the number of tools grows, organization becomes critical. A well-designed namespacing strategy helps agents select the right tools at the right time and reduces cognitive overhead.

### Prefix-Based Namespacing

Organize tools with clear prefixes that indicate their domain:

- `asana_search` -- search within Asana
- `jira_search` -- search within Jira
- `slack_send_message` -- send a message via Slack

### Resource-Type Namespacing

For tools within a single domain, further organize by resource type:

- `asana_projects_search`
- `asana_projects_create`
- `asana_users_search`
- `asana_tasks_update`

This hierarchical approach creates a natural taxonomy that agents can navigate efficiently. When an agent needs to find something in Asana, the prefix immediately narrows the search space. When it needs to operate on a specific resource type, the second level provides further disambiguation.

---

## Chapter 5: Designing Tool Responses

What a tool returns is just as important as what it does. Responses should contain only high-signal information. The goal is to give the agent exactly what it needs to continue its work, nothing more and nothing less.

### Semantic Naming

Prioritize semantic meaning over technical metadata in response fields:

| Prefer | Over |
|---|---|
| `name` | `uuid` |
| `image_url` | `256px_image_url` |
| `file_type` | `mime_type` |

The agent needs to understand what things are and what they mean. Technical identifiers like UUIDs and MIME types are useful for machines but add noise for agents trying to reason about their next action.

### Resolving Identifiers

Wherever possible, resolve arbitrary identifiers into semantically meaningful and interpretable language. If a status field contains a code like `3`, resolve it to `"in_progress"`. If a user is referenced by ID, include their name alongside the ID. This reduces the number of follow-up lookups the agent needs to make.

### Flexible Response Formats

Implement optional `response_format` enum parameters that allow agents to request different levels of detail:

- `"concise"`: Returns minimal fields needed for decision-making
- `"detailed"`: Returns comprehensive information for deep analysis

This gives agents the ability to trade off between context efficiency and information completeness based on their current needs.

---

## Chapter 6: Token Efficiency and Error Handling

Context windows are finite, and every token matters. Designing for token efficiency is not just an optimization -- it is a core requirement for tools that work well in practice.

### Managing Response Size

Several strategies help keep responses within reasonable bounds:

- **Pagination**: Return results in pages rather than all at once
- **Range selection**: Allow agents to request specific ranges of data
- **Filtering**: Provide server-side filtering so agents receive only relevant data
- **Truncation**: When responses would be too large, truncate intelligently with clear indicators

### Actionable Error Messages

When something goes wrong, the error message should guide the agent toward a solution. Helpful truncation and error messages guide agents toward more token-efficient tool-use behaviors.

**Good error response:**
> "Results truncated to 50 items. Use the `query` parameter to narrow your search, or use `offset` to paginate through results."

**Poor error response:**
> "Error code: 413"

Opaque error codes and tracebacks are unhelpful for agents. Instead, provide clear, actionable guidance that helps the agent adjust its approach and try again successfully.

---

## Chapter 7: Description Engineering

Prompt-engineering your tool descriptions and specifications ranks among the most impactful optimization methods available. The text you write in tool descriptions, parameter descriptions, and usage examples directly influences how well agents use your tools.

### Parameter Naming

Choose clear, unambiguous parameter names. Use `user_id` instead of a generic `user`. Use `start_date` instead of `date`. Use `search_query` instead of `q`. Every parameter name is a signal to the agent about what belongs there.

### Providing Context

Include explicit context about specialized query formats, expected input patterns, and edge cases. If a search tool supports a special query syntax, document it in the tool description. If a parameter has constraints or valid ranges, state them clearly.

### Descriptions as Prompts

Think of tool descriptions as prompts. They are instructions to the agent about when and how to use the tool. A well-written description answers:

- **When** should the agent use this tool?
- **What** does it need to provide?
- **What** will it get back?
- **What** should it do with the results?

---

## Chapter 8: Evaluation Methodology

Rigorous evaluation is the backbone of effective tool development. Without measurement, optimization is guesswork.

### Building Evaluation Loops

Use simple while-loop agentic patterns for evaluation. The evaluation harness does not need to be complex. A straightforward loop that sends prompts to the agent, captures tool calls and responses, and checks results against expected outcomes is sufficient.

### Metrics Beyond Accuracy

Track multiple dimensions of performance:

- **Accuracy**: Does the agent produce correct results?
- **Runtime**: How long does the end-to-end interaction take?
- **Call counts**: How many tool calls does the agent need?
- **Token consumption**: How much context does the interaction consume?
- **Error rates**: How often do tool calls fail?

A tool might produce correct results but consume excessive tokens or require too many calls. Multi-dimensional metrics reveal these tradeoffs.

### Reasoning and Chain-of-Thought

Include reasoning and feedback blocks in agent outputs before tool calls. This triggers chain-of-thought behavior that improves decision-making. When agents explain their reasoning before acting, they make better tool selections and provide better parameters.

### Response Format Considerations

The format of tool responses -- whether XML, JSON, or Markdown -- can measurably impact agent performance. Different formats work better for different types of data and different agent architectures. Test multiple formats during evaluation to find the best fit.

---

## Chapter 9: Empirical Results and Iteration

The methodology presented in this guide has been validated through real-world application. Internal testing of tools such as Slack MCP servers and Asana integrations showed measurable accuracy improvements through iterative optimization.

A key finding is that the systematic process of evaluation and optimization achieved additional performance improvements even beyond what was achieved with expert tool implementations -- whether those tools were manually written by researchers or generated by Claude itself. This suggests that the methodology matters more than the initial implementation quality.

The implication is powerful: even if your first tool implementation is imperfect, applying rigorous evaluation and systematic optimization will drive it toward high performance.

---

## Chapter 10: Looking Forward

Tools will evolve alongside model capabilities. As language models become more capable, the bar for effective tools will shift. New modalities, larger context windows, and better reasoning will all change what constitutes a good tool.

However, the systematic, evaluation-driven approach to improving tools for agents remains relevant across these changes. The principles of clarity, consolidation, token efficiency, and rigorous evaluation transcend any particular model architecture or protocol version.

What matters is not any single tool design but the discipline of measuring, analyzing, and improving. The tools you build today will need to adapt tomorrow, and the methodology described here provides the framework for that ongoing evolution.

---

## Summary of Key Principles

1. **Design for non-determinism**: Agents choose their own paths. Tools must work well regardless of the order or combination in which they are called.

2. **Consolidate functionality**: Fewer, more powerful tools outperform many granular ones. Reduce the number of calls agents need to make.

3. **Prefer search over listing**: Let agents query for what they need rather than dumping everything into context.

4. **Use clear namespacing**: Organize tools so agents can find the right one quickly.

5. **Return high-signal responses**: Include semantic meaning, resolve identifiers, and support flexible detail levels.

6. **Engineer your descriptions**: Tool descriptions are prompts. Write them with the same care you would give to any other prompt.

7. **Manage tokens deliberately**: Use pagination, filtering, and truncation. Every token in the context window has a cost.

8. **Provide actionable errors**: When things go wrong, tell the agent how to fix it.

9. **Evaluate rigorously**: Build evaluation loops, track multiple metrics, and test against realistic scenarios.

10. **Iterate systematically**: The process of improvement matters more than the starting point. Measure, analyze, optimize, repeat.
