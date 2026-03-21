── Page 1 ──

# Building Effective Agents

**By Anthropic** | Erik Schluntz and Barry Zhang
Published December 19, 2024

---

*A comprehensive guide to building successful LLM agent implementations using simple, composable patterns rather than complex frameworks.*

── Page 2 ──

# Chapter 1: Introduction

Over the past year, Anthropic has worked with dozens of teams building large language model (LLM) agents across industries. Consistently, the most successful implementations were not using complex frameworks or specialized libraries. Instead, they were building with simple, composable patterns.

This book shares what Anthropic has learned from working with customers and building agents internally, and provides practical advice for developers on building effective agents.

The central thesis is straightforward: success in the LLM space is not about building the most sophisticated system. It is about building the *right* system for your needs. Start with simple prompts, optimize them with comprehensive evaluation, and add multi-step agentic systems only when simpler solutions fall short.

## 1.1 Who This Book Is For

This guide is intended for developers, architects, and technical leaders who are building or planning to build systems that incorporate LLMs. Whether you are creating a customer support chatbot, a coding assistant, or a complex multi-step workflow, the patterns and principles described here will help you make better design decisions.

## 1.2 The Simplicity Principle

Before diving into patterns and architectures, it is important to internalize the guiding principle that runs throughout this guide: always find the simplest solution possible, and only increase complexity when needed. Many teams fall into the trap of over-engineering their agentic systems before validating that simpler approaches are insufficient.

── Page 3 ──

# Chapter 2: What Are Agents?

"Agent" can be defined in several ways. Some define agents as fully autonomous systems that operate independently over extended periods, using various tools to accomplish complex tasks. Others use the term to describe more prescriptive implementations that follow predefined workflows.

At Anthropic, all these variations are categorized as **agentic systems**, but an important architectural distinction is drawn between **workflows** and **agents**.

## 2.1 Workflows

Workflows are systems where LLMs and tools are orchestrated through predefined code paths. The developer designs the sequence of steps, the decision points, and the flow of data. The LLM operates within a structured framework that the developer controls.

Key characteristics of workflows:
- Predefined execution paths
- Deterministic flow control
- Developer-directed orchestration
- Predictable and consistent behavior

## 2.2 Agents

Agents, on the other hand, are systems where LLMs dynamically direct their own processes and tool usage, maintaining control over how they accomplish tasks. The LLM decides what steps to take, which tools to use, and when to stop.

Key characteristics of agents:
- Dynamic decision-making
- Self-directed tool usage
- Flexible execution paths
- Model-driven control flow

## 2.3 The Spectrum of Autonomy

In practice, most agentic systems fall somewhere on a spectrum between fully prescribed workflows and fully autonomous agents. Understanding where your system needs to be on this spectrum is one of the most important design decisions you will make.

── Page 4 ──

# Chapter 3: When (and When Not) to Use Agents

When building applications with LLMs, the recommendation is to find the simplest solution possible, and only increase complexity when needed. This might mean not building agentic systems at all.

## 3.1 The Cost-Benefit Tradeoff

Agentic systems often trade latency and cost for better task performance. You should carefully consider when this tradeoff makes sense. Key factors include:

- **Latency**: Multi-step agentic systems inherently take longer than single LLM calls
- **Cost**: Each additional LLM call adds to your API costs
- **Complexity**: More moving parts means more potential failure points
- **Performance**: The quality improvement must justify the additional overhead

## 3.2 When Workflows Are the Right Choice

When more complexity is warranted, workflows offer predictability and consistency for well-defined tasks. They are the better choice when:

- The task has a known, fixed structure
- Consistency and repeatability are paramount
- You need deterministic behavior
- The subtasks are well-understood in advance

## 3.3 When Agents Are the Right Choice

Agents are the better option when flexibility and model-driven decision-making are needed at scale. They are appropriate when:

- The task is open-ended with unpredictable steps
- The number and nature of subtasks cannot be known in advance
- The system needs to adapt dynamically to new information
- You have sufficient trust in the model's decision-making

## 3.4 When Neither Is Needed

For many applications, optimizing single LLM calls with retrieval and in-context examples is usually enough. Before building an agentic system, always ask: can a well-crafted prompt with good context achieve the desired result?

── Page 5 ──

# Chapter 4: When and How to Use Frameworks

There are many frameworks that make agentic systems easier to implement, including:

- **Claude Agent SDK** - Anthropic's own agent development kit
- **Strands Agents SDK by AWS** - Amazon's agent framework
- **Rivet** - A drag-and-drop GUI LLM workflow builder
- **Vellum** - A GUI tool for building and testing complex workflows

## 4.1 Benefits of Frameworks

These frameworks make it easy to get started by simplifying standard low-level tasks like:

- Calling LLMs
- Defining and parsing tools
- Chaining calls together
- Managing conversation state

## 4.2 Risks of Frameworks

However, frameworks often create extra layers of abstraction that can obscure the underlying prompts and responses, making them harder to debug. They can also make it tempting to add complexity when a simpler setup would suffice.

Incorrect assumptions about what is under the hood are a common source of customer error. When something goes wrong, you need to understand exactly what prompts are being sent and what responses are coming back.

## 4.3 Recommendations

The recommendation is that developers start by using LLM APIs directly. Many patterns can be implemented in a few lines of code. If you do use a framework, ensure you understand the underlying code.

As you move to production, do not hesitate to reduce abstraction layers and build with basic components. The overhead of a framework may not be worth the convenience it provides once you understand the patterns well.

── Page 6 ──

# Chapter 5: The Augmented LLM - The Foundation

The basic building block of agentic systems is an LLM enhanced with augmentations such as retrieval, tools, and memory. Current models can actively use these capabilities, generating their own search queries, selecting appropriate tools, and determining what information to retain.

## 5.1 Retrieval Augmentation

Retrieval augmentation allows the LLM to access external knowledge bases, documents, and data sources. The model generates its own search queries and incorporates the retrieved information into its responses.

Best practices for retrieval:
- Index your data effectively for the types of queries the model will generate
- Provide clear instructions about when and how to use retrieval
- Test with representative queries to ensure relevant results
- Consider the tradeoff between retrieval breadth and precision

## 5.2 Tool Augmentation

Tools give the LLM the ability to take actions in the world: calling APIs, querying databases, executing code, and interacting with external services. The model selects which tools to use and formulates the appropriate parameters.

Best practices for tools:
- Design clear, well-documented tool interfaces
- Limit the number of tools to what is necessary
- Provide usage examples in tool descriptions
- Handle errors gracefully and informatively

## 5.3 Memory Augmentation

Memory allows the LLM to retain and recall information across interactions. This can include conversation history, user preferences, learned facts, and accumulated context.

Best practices for memory:
- Be selective about what information to persist
- Provide clear mechanisms for the model to store and retrieve memories
- Consider privacy and data retention policies
- Test that memory retrieval is accurate and relevant

## 5.4 Model Context Protocol (MCP)

One approach to implementing these augmentations is through the Model Context Protocol, which allows developers to integrate with a growing ecosystem of third-party tools with a simple client implementation. MCP provides a standardized way to connect LLMs with external capabilities.

── Page 7 ──

# Chapter 6: Workflow Pattern - Prompt Chaining

Prompt chaining decomposes a task into a sequence of steps, where each LLM call processes the output of the previous one. You can add programmatic checks ("gates") on any intermediate steps to ensure that the process is still on track.

## 6.1 How It Works

1. The first LLM call receives the initial input and produces an intermediate output
2. A programmatic gate (optional) checks whether the output meets quality criteria
3. The next LLM call receives the intermediate output and produces the next step
4. This continues until the final output is produced

The key insight is that each LLM call handles a smaller, more focused task, which tends to produce higher quality results than asking a single LLM call to handle the entire task at once.

## 6.2 When to Use Prompt Chaining

This workflow is ideal for situations where the task can be easily and cleanly decomposed into fixed subtasks. The main goal is to trade off latency for higher accuracy, by making each LLM call an easier task.

## 6.3 Examples

**Marketing Copy Translation:**
- Step 1: Generate marketing copy in the source language
- Step 2: Translate the copy into the target language

**Document Writing with Quality Control:**
- Step 1: Write an outline of a document
- Step 2: Check that the outline meets certain criteria (gate)
- Step 3: Write the document based on the outline

## 6.4 Design Considerations

- Keep each step focused on a single, well-defined subtask
- Use gates to catch quality issues early in the pipeline
- Consider what information needs to pass between steps
- Monitor intermediate outputs to identify bottlenecks

── Page 8 ──

# Chapter 7: Workflow Pattern - Routing

Routing classifies an input and directs it to a specialized follow-up task. This workflow allows for separation of concerns and building more specialized prompts.

## 7.1 How It Works

1. An input arrives at the system
2. A classification step determines the category or type of the input
3. Based on the classification, the input is routed to a specialized handler
4. The specialized handler processes the input with prompts and tools optimized for that category

## 7.2 Why Routing Matters

Without routing, optimizing for one kind of input can hurt performance on other inputs. By separating different types of inputs into specialized handlers, each handler can be independently optimized for its specific category.

## 7.3 When to Use Routing

Routing works well for complex tasks where there are distinct categories that are better handled separately, and where classification can be handled accurately, either by an LLM or a more traditional classification model or algorithm.

## 7.4 Examples

**Customer Service Query Routing:**
- General questions are routed to an informational handler
- Refund requests are routed to a transactional handler with refund tools
- Technical support queries are routed to a diagnostic handler with troubleshooting tools

**Model Selection Routing:**
- Easy or common questions are routed to smaller, cost-efficient models like Claude Haiku 4.5
- Hard or unusual questions are routed to more capable models like Claude Sonnet 4.5
- This optimizes for both cost efficiency and performance quality

## 7.5 Design Considerations

- Invest in accurate classification: the quality of routing depends entirely on the quality of the classifier
- Define clear, mutually exclusive categories when possible
- Include a fallback category for inputs that do not fit neatly into any defined category
- Monitor classification accuracy over time and adjust as needed

── Page 9 ──

# Chapter 8: Workflow Pattern - Parallelization

LLMs can sometimes work simultaneously on a task and have their outputs aggregated programmatically. This workflow manifests in two key variations.

## 8.1 Sectioning

Sectioning breaks a task into independent subtasks that are run in parallel. Each subtask handles a different aspect of the overall task, and the results are combined at the end.

**Examples of sectioning:**
- **Guardrails implementation**: One model instance processes user queries while another screens them for inappropriate content or requests. This tends to perform better than having the same LLM call handle both guardrails and the core response.
- **Automated evaluations**: Each LLM call evaluates a different aspect of the model's performance on a given prompt, running simultaneously to reduce total evaluation time.

## 8.2 Voting

Voting runs the same task multiple times to get diverse outputs. Multiple perspectives or attempts produce higher confidence results.

**Examples of voting:**
- **Code vulnerability review**: Several different prompts review and flag code if they find a problem. Multiple independent reviewers increase the chance of catching issues.
- **Content moderation**: Multiple prompts evaluate different aspects of whether content is inappropriate, with different vote thresholds to balance false positives and negatives.

## 8.3 When to Use Parallelization

Parallelization is effective when:
- The divided subtasks can be parallelized for speed
- Multiple perspectives or attempts are needed for higher confidence results
- Complex tasks with multiple considerations benefit from focused attention on each specific aspect

For complex tasks with multiple considerations, LLMs generally perform better when each consideration is handled by a separate LLM call, allowing focused attention on each specific aspect.

## 8.4 Design Considerations

- Ensure subtasks are truly independent (for sectioning)
- Define clear aggregation logic for combining results
- Consider how to handle disagreements (for voting)
- Balance the number of parallel calls against cost and complexity

── Page 10 ──

# Chapter 9: Workflow Pattern - Orchestrator-Workers

In the orchestrator-workers workflow, a central LLM dynamically breaks down tasks, delegates them to worker LLMs, and synthesizes their results.

## 9.1 How It Works

1. The orchestrator LLM receives the overall task
2. It analyzes the task and determines what subtasks are needed
3. It delegates each subtask to a worker LLM
4. Worker LLMs complete their subtasks and return results
5. The orchestrator synthesizes the worker results into a final output

## 9.2 Difference from Parallelization

While topographically similar to parallelization, the key difference is flexibility. In parallelization, subtasks are pre-defined. In the orchestrator-workers pattern, subtasks are determined dynamically by the orchestrator based on the specific input.

## 9.3 When to Use Orchestrator-Workers

This workflow is well-suited for complex tasks where you cannot predict the subtasks needed. For example, in coding tasks, the number of files that need to be changed and the nature of the change in each file likely depend on the specific task at hand.

## 9.4 Examples

**Complex Code Changes:**
- The orchestrator analyzes a feature request
- It identifies which files need modification
- It delegates each file change to a worker
- It synthesizes the changes into a coherent implementation

**Multi-Source Research:**
- The orchestrator determines what information is needed
- It assigns workers to search different sources
- It combines and synthesizes the gathered information
- It produces a comprehensive research report

## 9.5 Design Considerations

- Give the orchestrator clear instructions on how to decompose tasks
- Define the interface between orchestrator and workers clearly
- Consider how the orchestrator should handle worker failures
- Monitor the quality of task decomposition over time

── Page 11 ──

# Chapter 10: Workflow Pattern - Evaluator-Optimizer

In the evaluator-optimizer workflow, one LLM call generates a response while another provides evaluation and feedback in a loop.

## 10.1 How It Works

1. The generator LLM produces an initial response
2. The evaluator LLM reviews the response and provides feedback
3. If the response does not meet quality criteria, the generator incorporates the feedback and produces a revised response
4. This loop continues until the evaluator is satisfied or a maximum number of iterations is reached

## 10.2 When to Use Evaluator-Optimizer

This workflow is particularly effective when:
- There are clear evaluation criteria
- Iterative refinement provides measurable value
- LLM responses can be demonstrably improved when a human articulates their feedback
- The LLM can provide such feedback effectively

This is analogous to the iterative writing process a human writer might go through when producing a polished document.

## 10.3 Examples

**Literary Translation:**
- A translator LLM produces an initial translation
- An evaluator LLM identifies nuances that were not captured
- The translator revises the translation based on the critique
- The process continues until the translation is deemed high quality

**Comprehensive Research:**
- A search agent gathers initial information
- An evaluator determines whether the information is comprehensive enough
- If further searches are warranted, the evaluator directs additional rounds
- The process terminates when sufficient information has been gathered

## 10.4 Design Considerations

- Define clear, specific evaluation criteria
- Set a maximum number of iterations to prevent infinite loops
- Monitor whether each iteration actually improves quality
- Consider the cost of multiple LLM calls for evaluation and regeneration

── Page 12 ──

# Chapter 11: Autonomous Agents

Agents are emerging in production as LLMs mature in key capabilities: understanding complex inputs, engaging in reasoning and planning, using tools reliably, and recovering from errors.

## 11.1 How Agents Work

Agents begin their work with either a command from, or interactive discussion with, the human user. Once the task is clear, agents plan and operate independently, potentially returning to the human for further information or judgment.

The agent loop follows this pattern:
1. **Receive input** from user or environment
2. **Think** and plan the next steps
3. **Act** by calling tools or generating outputs
4. **Observe** the results of the action
5. **Repeat** until the task is complete or a stopping condition is met

During execution, it is crucial for agents to gain "ground truth" from the environment at each step (such as tool call results or code execution) to assess progress. Agents can then pause for human feedback at checkpoints or when encountering blockers.

## 11.2 Key Properties of Agents

- **Autonomy**: Agents direct their own processes and tool usage
- **Persistence**: They can operate over extended periods and multiple steps
- **Adaptability**: They adjust their approach based on intermediate results
- **Tool usage**: They interact with external systems and APIs
- **Error recovery**: They can recognize and recover from mistakes

## 11.3 Implementation Simplicity

Despite handling sophisticated tasks, agent implementation is often straightforward. Agents are typically just LLMs using tools based on environmental feedback in a loop. The sophistication comes not from complex code, but from well-designed toolsets and clear documentation.

## 11.4 When to Use Agents

Agents can be used for open-ended problems where:
- It is difficult or impossible to predict the required number of steps
- You cannot hardcode a fixed path
- The LLM will potentially operate for many turns
- You have sufficient trust in the model's decision-making

Agents' autonomy makes them ideal for scaling tasks in trusted environments.

## 11.5 Risks and Mitigations

The autonomous nature of agents means:
- **Higher costs**: Multiple LLM calls and tool invocations add up
- **Compounding errors**: Mistakes early in the process can cascade
- **Unpredictable behavior**: The model may take unexpected paths

Mitigations include:
- Extensive testing in sandboxed environments
- Appropriate guardrails and stopping conditions
- Human oversight at critical checkpoints
- Maximum iteration limits to maintain control

## 11.6 Examples from Anthropic

**SWE-bench Coding Agent:**
A coding agent that resolves real GitHub issues from the SWE-bench benchmark, which involves edits to many files based on a task description.

**Computer Use Agent:**
A reference implementation where Claude uses a computer to accomplish tasks, demonstrating the full range of agent capabilities in interacting with real software environments.

── Page 13 ──

# Chapter 12: Combining and Customizing Patterns

The building blocks described in this book are not prescriptive. They are common patterns that developers can shape and combine to fit different use cases.

## 12.1 Mixing Patterns

In practice, most production systems combine multiple patterns. For example:
- A **routing** step might direct inputs to different **prompt chains**
- An **orchestrator-workers** system might use **evaluator-optimizer** loops within each worker
- A **parallelization** step might feed into a **prompt chain** for synthesis
- An **agent** might use **routing** internally to select between different tool strategies

## 12.2 The Key to Success

The key to success, as with any LLM features, is measuring performance and iterating on implementations. You should consider adding complexity *only* when it demonstrably improves outcomes.

This means:
- Start with the simplest viable approach
- Measure its performance against clear criteria
- Add complexity incrementally, measuring improvement at each step
- Be willing to revert complexity that does not produce measurable gains

── Page 14 ──

# Chapter 13: Agents in Practice - Customer Support

Customer support combines familiar chatbot interfaces with enhanced capabilities through tool integration. This is a natural fit for more open-ended agents.

## 13.1 Why Customer Support Is a Natural Fit

Support interactions naturally follow a conversation flow while requiring access to external information and actions. Several properties make this domain particularly suitable:

- **Conversational nature**: Support interactions follow a natural dialogue flow
- **Tool integration**: Tools can pull customer data, order history, and knowledge base articles
- **Actionable outcomes**: Actions such as issuing refunds or updating tickets can be handled programmatically
- **Measurable success**: Success can be clearly measured through user-defined resolutions

## 13.2 Proven Business Model

Several companies have demonstrated the viability of this approach through usage-based pricing models that charge only for successful resolutions, showing confidence in their agents' effectiveness. This pricing model aligns incentives: the provider only earns revenue when the agent actually solves the customer's problem.

## 13.3 Design Recommendations

When building a customer support agent:
- Provide access to comprehensive customer context (account history, previous interactions)
- Define clear escalation paths for issues the agent cannot resolve
- Implement guardrails to prevent unauthorized actions
- Maintain conversation logs for quality assurance and improvement
- Set clear expectations with users about what the agent can and cannot do

── Page 15 ──

# Chapter 14: Agents in Practice - Coding Agents

The software development space has shown remarkable potential for LLM features, with capabilities evolving from code completion to autonomous problem-solving.

## 14.1 Why Coding Is a Natural Fit

Coding agents are particularly effective because:

- **Verifiable outputs**: Code solutions can be verified through automated tests
- **Feedback loops**: Agents can iterate on solutions using test results as feedback
- **Well-defined problem space**: The domain is structured and has clear rules
- **Objective quality measures**: Output quality can be measured through test passage, linting, and other automated checks

## 14.2 The Power of Automated Verification

The ability to automatically verify code through tests provides a powerful feedback mechanism that is often absent in other domains. When an agent writes code that fails a test, the test failure provides specific, actionable information about what needs to change.

## 14.3 Real-World Performance

In Anthropic's own implementation, agents can now solve real GitHub issues in the SWE-bench Verified benchmark based on the pull request description alone. This demonstrates that agents can handle real-world software engineering tasks, not just synthetic benchmarks.

## 14.4 The Role of Human Review

However, while automated testing helps verify functionality, human review remains crucial for ensuring solutions align with broader system requirements. Automated tests can verify that code works correctly, but they cannot always verify that it represents the best approach, maintains code quality standards, or integrates well with the broader system architecture.

── Page 16 ──

# Chapter 15: Prompt Engineering Your Tools

No matter which agentic system you are building, tools will likely be an important part of your agent. Tool engineering deserves just as much attention as prompt engineering.

## 15.1 The Importance of Tool Format

There are often several ways to specify the same action. For instance, you can specify a file edit by writing a diff, or by rewriting the entire file. For structured output, you can return code inside markdown or inside JSON.

In software engineering, differences like these are cosmetic and can be converted losslessly from one to the other. However, some formats are much more difficult for an LLM to write than others:

- **Writing a diff** requires knowing how many lines are changing in the chunk header before the new code is written
- **Writing code inside JSON** (compared to markdown) requires extra escaping of newlines and quotes

## 15.2 Principles for Tool Format Design

The following principles guide tool format selection:

1. **Give the model enough tokens to think** before it writes itself into a corner. Do not require the model to commit to structural decisions before it has had a chance to reason about the content.

2. **Keep the format close to what the model has seen naturally** occurring in text on the internet. Formats that appear frequently in training data will be easier for the model to produce correctly.

3. **Minimize formatting overhead** such as having to keep an accurate count of thousands of lines of code, or string-escaping any code it writes. The less the model has to worry about formatting, the more it can focus on the actual task.

## 15.3 Building Agent-Computer Interfaces (ACI)

One rule of thumb is to think about how much effort goes into human-computer interfaces (HCI), and plan to invest just as much effort in creating good agent-computer interfaces (ACI).

**Guidelines for ACI design:**

- **Put yourself in the model's shoes.** Is it obvious how to use this tool, based on the description and parameters, or would you need to think carefully about it? If so, then it is probably also true for the model. A good tool definition often includes example usage, edge cases, input format requirements, and clear boundaries from other tools.

- **Optimize parameter names and descriptions.** Think of this as writing a great docstring for a junior developer on your team. This is especially important when using many similar tools.

- **Test extensively.** Run many example inputs to see what mistakes the model makes, and iterate on the tool definition based on those observations.

- **Apply poka-yoke principles.** Change the arguments so that it is harder to make mistakes. Poka-yoke is a Japanese term meaning "mistake-proofing" -- designing tools and interfaces so that errors are prevented by design rather than caught after the fact.

## 15.4 Real-World Example: Absolute vs Relative Paths

While building the agent for SWE-bench, Anthropic spent more time optimizing tools than the overall prompt. For example, the model would make mistakes with tools using relative filepaths after the agent had moved out of the root directory. The fix was to change the tool to always require absolute filepaths, and the model used this method flawlessly.

This example illustrates the principle well: rather than adding instructions to "remember to use absolute paths," the tool was redesigned so that relative paths were simply not an option. The interface itself prevented the error.

── Page 17 ──

# Chapter 16: Core Principles and Summary

Success in building effective agents comes down to three core principles.

## 16.1 Principle 1: Maintain Simplicity

Maintain simplicity in your agent's design. Resist the temptation to add complexity before it is needed. Start with the simplest approach that could work, and add layers of sophistication only when measurement shows they provide tangible improvement.

## 16.2 Principle 2: Prioritize Transparency

Prioritize transparency by explicitly showing the agent's planning steps. When an agent's reasoning is visible, it becomes:
- Easier to debug when things go wrong
- More trustworthy for end users
- Simpler to identify and fix systematic issues
- More straightforward to evaluate and improve

## 16.3 Principle 3: Invest in Tool Design

Carefully craft your agent-computer interface through thorough tool documentation and testing. The quality of your tools directly determines the quality of your agent's behavior. Invest the same level of care in your ACI as you would in any user-facing interface.

## 16.4 Final Thoughts

Frameworks can help you get started quickly, but do not hesitate to reduce abstraction layers and build with basic components as you move to production. By following these principles, you can create agents that are not only powerful but also reliable, maintainable, and trusted by their users.

The field of LLM agents is evolving rapidly, but the fundamentals remain constant: understand your problem, choose the right level of complexity, design your tools carefully, and always measure the impact of your decisions.

---

*This work draws upon Anthropic's experiences building agents and the valuable insights shared by their customers.*

*Source: https://www.anthropic.com/engineering/building-effective-agents*