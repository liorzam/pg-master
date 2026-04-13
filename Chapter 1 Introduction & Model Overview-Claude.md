# Chapter 1: Introduction & Model Overview

## Overview of the Claude Model Family

Claude is a family of state-of-the-art large language models developed by Anthropic. All current Claude models support text and image input, text output, multilingual capabilities, and vision. Models are available via the Claude API, AWS Bedrock, and Google Vertex AI.

The current generation consists of three models, each optimized for different use cases:

### Claude Opus 4.6

The most intelligent model for building agents and coding. Opus 4.6 excels at long-horizon reasoning, complex multi-step tasks, and agentic workflows. It features adaptive thinking, 128K max output tokens, and supports effort levels up to `max`.

- **API ID:** `claude-opus-4-6`
- **Context Window:** 200K tokens (1M tokens in beta)
- **Max Output:** 128K tokens
- **Pricing:** $5/input MTok, $25/output MTok
- **Latency:** Moderate
- **Knowledge Cutoff:** May 2025 (reliable), Aug 2025 (training data)

### Claude Sonnet 4.6

The best combination of speed and intelligence. Sonnet 4.6 is ideal for everyday coding, analysis, content tasks, and agentic workflows where fast turnaround matters.

- **API ID:** `claude-sonnet-4-6`
- **Context Window:** 200K tokens (1M tokens in beta)
- **Max Output:** 64K tokens
- **Pricing:** $3/input MTok, $15/output MTok
- **Latency:** Fast
- **Knowledge Cutoff:** Aug 2025 (reliable), Jan 2026 (training data)

### Claude Haiku 4.5

The fastest model with near-frontier intelligence. Best for high-volume, latency-sensitive workloads.

- **API ID:** `claude-haiku-4-5-20251001` (alias: `claude-haiku-4-5`)
- **Context Window:** 200K tokens
- **Max Output:** 64K tokens
- **Pricing:** $1/input MTok, $5/output MTok
- **Latency:** Fastest
- **Knowledge Cutoff:** Feb 2025 (reliable), Jul 2025 (training data)

## Model Comparison Table

| Feature | Opus 4.6 | Sonnet 4.6 | Haiku 4.5 |
|---------|----------|------------|-----------|
| **API ID** | claude-opus-4-6 | claude-sonnet-4-6 | claude-haiku-4-5-20251001 |
| **Pricing (input/output)** | $5/$25 per MTok | $3/$15 per MTok | $1/$5 per MTok |
| **Extended Thinking** | Yes | Yes | Yes |
| **Adaptive Thinking** | Yes | Yes | No |
| **Context Window** | 200K (1M beta) | 200K (1M beta) | 200K |
| **Max Output** | 128K tokens | 64K tokens | 64K tokens |
| **Latency** | Moderate | Fast | Fastest |
| **Effort: max** | Yes | No | No |

### Platform Availability

All models are available on:
- **Claude API** (direct)
- **AWS Bedrock** (IDs: `anthropic.claude-opus-4-6-v1`, `anthropic.claude-sonnet-4-6`, `anthropic.claude-haiku-4-5-20251001-v1:0`)
- **GCP Vertex AI** (IDs: `claude-opus-4-6`, `claude-sonnet-4-6`, `claude-haiku-4-5@20251001`)

Starting with Claude Sonnet 4.5 and later, AWS Bedrock and Google Vertex AI offer **global endpoints** (dynamic routing for maximum availability) and **regional endpoints** (guaranteed data routing through specific geographic regions).

## How to Choose the Right Model

### Choose Opus 4.6 when:
- You need the highest intelligence for complex reasoning tasks
- Building autonomous agents that run for extended periods
- Working on large-scale code migrations or deep research
- You need long-horizon reasoning across multiple context windows
- You need the `max` effort level for absolute maximum capability
- Cost is less important than quality

### Choose Sonnet 4.6 when:
- You need a balance of speed and intelligence
- Building agentic coding workflows with fast turnaround
- Working on everyday coding, analysis, and content tasks
- Cost efficiency matters alongside strong performance
- You need adaptive thinking with faster response times

### Choose Haiku 4.5 when:
- Speed is the top priority
- Running high-volume, latency-sensitive workloads
- Simple classification, routing, or subagent tasks
- Cost optimization is critical
- Tasks don't require deep reasoning

### Decision Framework

1. **Start with the task complexity:** If it requires deep multi-step reasoning, start with Opus 4.6
2. **Consider latency requirements:** If you need fast responses, use Sonnet 4.6 or Haiku 4.5
3. **Factor in volume:** High-volume tasks favor Haiku 4.5 for cost efficiency
4. **Evaluate with the effort parameter:** You can often get Opus-level quality from Sonnet at lower cost by adjusting effort levels
5. **Test and iterate:** The best model for your use case depends on your specific requirements - prototype with Opus, then see if Sonnet or Haiku can meet your quality bar

# Chapter 2: Universal Prompting Techniques

These core techniques apply to ALL Claude models and form the foundation of effective prompt engineering.

## Be Clear, Direct, and Detailed

When interacting with Claude, think of it as a brilliant but new employee who needs explicit instructions. The more precisely you explain what you want, the better Claude's response will be.

**The golden rule:** Show your prompt to a colleague with minimal context on the task. If they're confused, Claude will likely be too.

### How to be clear, contextual, and specific:

1. **Give Claude contextual information:**
   - What the task results will be used for
   - What audience the output is meant for
   - What workflow the task is part of
   - The end goal or what successful completion looks like

2. **Be specific about what you want Claude to do.** For example, if you want only code output, say so explicitly.

3. **Provide instructions as sequential steps.** Use numbered lists or bullet points to ensure Claude carries out the task exactly as intended.

### Example: Anonymizing Customer Feedback

**Less effective prompt:**
```
Please remove all personally identifiable information from these customer feedback messages: {{FEEDBACK_DATA}}
```

**More effective prompt:**
```
Your task is to anonymize customer feedback for our quarterly review.

Instructions:
1. Replace all customer names with "CUSTOMER_[ID]" (e.g., "Jane Doe" → "CUSTOMER_001").
2. Replace email addresses with "EMAIL_[ID]@example.com".
3. Redact phone numbers as "PHONE_[ID]".
4. If a message mentions a specific product (e.g., "AcmeCloud"), leave it intact.
5. If no PII is found, copy the message verbatim.
6. Output only the processed messages, separated by "---".

Data to process: {{FEEDBACK_DATA}}
```

### Example: Incident Response Analysis

**Less effective:**
```
Analyze this AcmeCloud outage report and summarize the key points.
{{REPORT}}
```

**More effective:**
```
Analyze this AcmeCloud outage report. Skip the preamble. Keep your response terse and write only the bare bones necessary information. List only:
1) Cause
2) Duration
3) Impacted services
4) Number of affected users
5) Estimated revenue loss.

Here's the report: {{REPORT}}
```

## Use Examples (Multishot Prompting)

Examples are your secret weapon for getting Claude to generate exactly what you need. By providing a few well-crafted examples, you can dramatically improve accuracy, consistency, and quality.

**Power up your prompts:** Include 3-5 diverse, relevant examples to show Claude exactly what you want.

### Why use examples?
- **Accuracy:** Reduces misinterpretation of instructions
- **Consistency:** Enforces uniform structure and style
- **Performance:** Boosts Claude's ability to handle complex tasks

### Crafting effective examples:
- **Relevant:** Mirror your actual use case
- **Diverse:** Cover edge cases and potential challenges; vary enough to avoid unintended patterns
- **Clear:** Wrap in `<example>` tags (if multiple, nest within `<examples>` tags)

### Example: Customer Feedback Analysis

```
Our CS team is overwhelmed with unstructured feedback. Your task is to analyze
feedback and categorize issues for our product and engineering teams. Use these
categories: UI/UX, Performance, Feature Request, Integration, Pricing, and Other.
Also rate the sentiment (Positive/Neutral/Negative) and priority (High/Medium/Low).

Here is an example:

<example>
Input: The new dashboard is a mess! It takes forever to load, and I can't find
the export button. Fix this ASAP!
Category: UI/UX, Performance
Sentiment: Negative
Priority: High
</example>

Now, analyze this feedback: {{FEEDBACK}}
```

**Pro tip:** Ask Claude to evaluate your examples for relevance, diversity, or clarity. Or have Claude generate more examples based on your initial set.

## Chain of Thought Prompting

Giving Claude space to think can dramatically improve performance on complex tasks. This technique encourages Claude to break down problems step-by-step.

### When to use CoT:
- Complex math, logic, or analysis tasks
- Multi-step reasoning problems
- Decisions with many factors
- Writing complex documents

### When NOT to use CoT:
- Simple lookups or classifications
- Tasks where increased latency is unacceptable

### Three levels of CoT (least to most complex):

#### 1. Basic Prompt
Add "Think step-by-step" to your prompt:
```
Draft personalized emails to donors asking for contributions to this year's
Care for Kids program.

Program information: {{PROGRAM_DETAILS}}
Donor information: {{DONOR_DETAILS}}

Think step-by-step before you write the email.
```

#### 2. Guided Prompt
Outline specific steps for Claude to follow:
```
Think before you write the email. First, think through what messaging might
appeal to this donor given their donation history and which campaigns they've
supported in the past. Then, think through what aspects of the Care for Kids
program would appeal to them. Finally, write the personalized donor email
using your analysis.
```

#### 3. Structured Prompt (Recommended)
Use XML tags to separate reasoning from the final answer:
```
Think before you write the email in <thinking> tags. First, think through
what messaging might appeal to this donor given their donation history.
Then, think through what aspects of the program would appeal to them.
Finally, write the personalized donor email in <email> tags.
```

**Important:** Always have Claude output its thinking. Without outputting its thought process, no thinking occurs!

## Use XML Tags for Structure

XML tags help Claude parse your prompts more accurately, leading to higher-quality outputs.

### Why use XML tags?
- **Clarity:** Clearly separate different parts of your prompt
- **Accuracy:** Reduce errors from misinterpretation
- **Flexibility:** Easily modify parts without rewriting everything
- **Parseability:** Makes it easy to extract specific parts of Claude's response

### Best practices:
1. **Be consistent:** Use the same tag names throughout and refer to them (e.g., "Using the contract in `<contract>` tags...")
2. **Nest tags:** Use `<outer><inner></inner></outer>` for hierarchical content
3. **Combine with other techniques:** Use with multishot (`<examples>`) or CoT (`<thinking>`, `<answer>`)

### Example: Legal Contract Analysis

```
Analyze this software licensing agreement for legal risks and liabilities.

We're a multinational enterprise considering this agreement for our core
data infrastructure.

<agreement>{{CONTRACT}}</agreement>

This is our standard contract for reference:
<standard_contract>{{STANDARD_CONTRACT}}</standard_contract>

<instructions>
1. Analyze these clauses:
   - Indemnification
   - Limitation of liability
   - IP ownership

2. Note unusual or concerning terms.
3. Compare to our standard contract.
4. Summarize findings in <findings> tags.
5. List actionable recommendations in <recommendations> tags.
</instructions>
```

## Give Claude a Role (System Prompts)

Using system prompts to assign Claude a role helps frame its responses for specific contexts. System prompts are particularly effective for:
- Setting the tone and expertise level
- Establishing behavioral guidelines
- Providing persistent context across a conversation

```python
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=1024,
    system="You are a senior financial analyst with 20 years of experience in risk assessment. Provide thorough, data-driven analysis.",
    messages=[{"role": "user", "content": "Analyze this portfolio..."}]
)
```

## Chain Complex Prompts for Multi-Step Tasks

For complex workflows, break the task into a pipeline of smaller prompts:

1. **Decompose the task** into subtasks with clear inputs and outputs
2. **Chain prompts** where the output of one becomes the input of the next
3. **Use different models** for different steps when appropriate (e.g., Haiku for classification, Opus for analysis)

This approach improves accuracy, allows targeted debugging, and lets you optimize each step independently.

## Long Context Tips

When working with long documents (Claude supports up to 200K tokens, or 1M in beta):

### Document Placement
- Place long documents **early** in the prompt, before your questions or instructions
- Claude processes information based on position; having documents first provides full context for the question

### XML Structure for Documents
```
Here are the documents to analyze:

<documents>
<document index="1">
<source>annual_report_2025.pdf</source>
<content>{{DOCUMENT_1}}</content>
</document>
<document index="2">
<source>competitor_analysis.pdf</source>
<content>{{DOCUMENT_2}}</content>
</document>
</documents>

Based on the above documents, answer the following questions:
```

### Grounding in Quotes
Ask Claude to quote directly from source material before analyzing. This reduces hallucinations and keeps responses grounded:

```
Find relevant quotes from the documents, then answer the question based on those quotes. Cite your sources.
```

### Tips for Very Long Contexts
- Structure documents with clear headings and sections
- Use numbered sections for easy reference
- Ask Claude to cite specific sections when answering
- Consider breaking very long tasks into focused sub-queries

# Chapter 3: Claude Opus 4.6 Best Practices

Claude Opus 4.6 is the most intelligent model in the Claude family, designed for building agents and coding.

## Adaptive Thinking (Recommended)

Adaptive thinking (`thinking: {type: "adaptive"}`) is the recommended thinking mode for Opus 4.6. Claude dynamically determines when and how much to think based on complexity.

```python
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=16000,
    thinking={"type": "adaptive"},
    messages=[{"role": "user", "content": "Solve this complex problem..."}],
)
```

Key behaviors:
- At `high` effort (default), Claude almost always thinks
- At lower effort levels, may skip thinking for simpler problems
- Automatically enables interleaved thinking (thinking between tool calls)
- No beta header required

**Important:** `thinking: {type: "enabled", budget_tokens: N}` is deprecated on Opus 4.6 and will be removed in a future release.

### Migration from Manual Thinking

```python

# BEFORE (deprecated)
response = client.beta.messages.create(
    model="claude-opus-4-5",
    max_tokens=16000,
    thinking={"type": "enabled", "budget_tokens": 32000},
    betas=["interleaved-thinking-2025-05-14"],
    messages=[...],
)

# AFTER (recommended)
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=16000,
    thinking={"type": "adaptive"},
    output_config={"effort": "high"},
    messages=[...],
)
```

Note: Adaptive thinking and effort are GA features - no beta SDK namespace or headers required.

## The Effort Parameter

Opus 4.6 is the only model supporting the `max` effort level.

| Level | Description | Use Case |
|-------|-------------|----------|
| `max` | Maximum capability, no constraints. **Opus 4.6 only.** | Deepest reasoning, most thorough analysis |
| `high` | High capability (default) | Complex reasoning, difficult coding, agentic tasks |
| `medium` | Balanced approach | Good performance with moderate token savings |
| `low` | Most efficient | Simple tasks, speed-critical, subagents |

```python
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=4096,
    thinking={"type": "adaptive"},
    output_config={"effort": "max"},
    messages=[{"role": "user", "content": "Deep analysis..."}],
)
```

Effect on tool use: Lower effort makes fewer tool calls, combines operations, skips preamble. Higher effort explains plans and provides detailed summaries.

## Long-Horizon Reasoning and State Tracking

Opus 4.6 excels at long-horizon reasoning with exceptional state tracking across extended sessions.

### Context Awareness

Opus 4.6 tracks its remaining context window throughout conversations:

```
Your context window will be automatically compacted as it approaches its limit.
Do not stop tasks early due to token budget concerns. Save progress and state
to memory before the context window refreshes. Always be persistent and complete
tasks fully.
```

### Multi-Context Window Workflows

1. **First window:** Set up framework (write tests, create setup scripts)
2. **Future windows:** Iterate on a todo-list
3. **Write tests in structured format** (e.g., `tests.json`) before starting work
4. **Create setup scripts** (e.g., `init.sh`) for servers, test suites, linters
5. **Fresh starts vs compaction:** Opus 4.6 is effective at discovering state from filesystem

### State Management

- **JSON** for structured state (test results, task status)
- **Plain text** for progress notes
- **Git** for version control and state tracking across sessions

## Subagent Orchestration

Opus 4.6 proactively recognizes when tasks benefit from delegation and spawns subagents without instruction.

**Warning:** Has a strong predilection for subagents and may overuse them (e.g., spawning subagents for code exploration when a direct grep is faster).

```
Use subagents when tasks can run in parallel, require isolated context, or
involve independent workstreams. For simple tasks, sequential operations,
or single-file edits, work directly rather than delegating.
```

## Balancing Autonomy and Safety

Without guidance, Opus 4.6 may take hard-to-reverse actions (deleting files, force-pushing, posting to external services).

```
Consider the reversibility and impact of your actions. Take local, reversible
actions freely. For hard-to-reverse or destructive actions, ask the user first.

Actions that warrant confirmation:
- Deleting files/branches, dropping database tables
- git push --force, git reset --hard
- Pushing code, commenting on PRs, sending messages
```

## Overthinking / Excessive Thoroughness

Opus 4.6 does significantly more upfront exploration than previous models, especially at higher effort settings.

Key fixes:
1. **Remove anti-laziness prompts** - "be thorough", "do not be lazy" amplify already-proactive behavior
2. **Soften tool-use language** - "Use [tool] when helpful" instead of "You must use [tool]"
3. **Remove explicit think tool instructions** - causes over-planning
4. **Use effort as primary control** - lower effort instead of adding prompt constraints

```
Prioritize execution over deliberation. Choose one approach and start immediately.
Do not compare alternatives or plan entirely before writing. Write each piece once.
```

## Overeagerness / Overengineering

Tends to create extra files, unnecessary abstractions, and unrequested flexibility.

```
Avoid over-engineering. Only make directly requested or clearly necessary changes.
Don't add features, refactor code, or make "improvements" beyond what was asked.
Don't add docstrings to unchanged code. Don't add error handling for impossible
scenarios. Don't create helpers for one-time operations.
```

## Tool Usage Patterns

- Precise instruction following; responds well to explicit direction
- "Can you suggest changes" = suggestions only. "Change this function" = action
- More responsive to system prompts; may overtrigger on aggressive tool language

### Parallel Tool Calling

```
<use_parallel_tool_calls>
Make all independent tool calls in parallel. When reading 3 files, run 3 parallel
tool calls. If some calls depend on previous results, call those sequentially.
</use_parallel_tool_calls>
```

## Frontend Design

Excels at complex web apps but may default to generic "AI slop" patterns without guidance.

```
<frontend_aesthetics>
- Typography: Choose beautiful, unique fonts. Avoid Arial, Inter, Roboto.
- Color: Commit to cohesive aesthetics. Dominant colors with sharp accents.
- Motion: Animations for effects and micro-interactions.
- Backgrounds: Atmosphere and depth, not solid colors.
Avoid overused fonts, purple gradients, predictable layouts.
</frontend_aesthetics>
```

## Vision Capabilities

Improved image processing and data extraction, especially with multiple images. Effective technique: provide a crop tool for Claude to "zoom" into relevant regions.

## LaTeX Output Defaults

Opus 4.6 defaults to LaTeX for math. For plain text:

```
Format in plain text only. Do not use LaTeX, MathJax, or markup like \( \), $.
Write math using standard characters (/ for division, * for multiplication, ^ for exponents).
```

## Prefill Deprecation

Prefilling assistant messages returns a 400 error on Opus 4.6. Alternatives:
- **Output formatting:** Use Structured Outputs or `output_config.format`
- **Eliminating preambles:** System prompt: "Respond directly without preamble"
- **Avoiding refusals:** Clear prompting without prefill is sufficient
- **Continuations:** User message: "Your previous response ended with [text]. Continue."

## Communication Style

More concise and natural than previous models:
- Direct, fact-based progress reports
- More conversational, less machine-like
- May skip summaries unless prompted

If you want updates: "After completing a task that involves tool use, provide a quick summary."

## 128K Output Tokens

Supports up to 128K output tokens (double previous limit). SDKs require streaming for large `max_tokens` values. Use `.stream()` with `.get_final_message()` for complete responses.

# Chapter 4: Claude Sonnet 4.6 Best Practices

Sonnet 4.6 combines strong intelligence with fast performance. Ideal for coding, analysis, content tasks, and agentic workflows where fast turnaround and cost efficiency matter.

## Default Effort Level

**Important:** Sonnet 4.6 defaults to `high` effort (unlike Sonnet 4.5 which had none). Set effort explicitly to avoid unexpected latency.

### Recommended Settings
- **Medium** (recommended default): Balance of speed, cost, performance for most applications
- **Low**: High-volume or latency-sensitive workloads (chat, classification)
- **High**: Maximum Sonnet intelligence

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=64000,
    output_config={"effort": "medium"},
    messages=[{"role": "user", "content": "Your prompt here"}],
)
```

## Thinking Modes

Sonnet 4.6 supports BOTH adaptive thinking AND manual extended thinking with interleaved mode - more flexibility than any other model.

### Adaptive Thinking

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=64000,
    thinking={"type": "adaptive"},
    output_config={"effort": "medium"},
    messages=[...],
)
```

### Manual Extended Thinking with Interleaved Mode

```python
response = client.beta.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=16384,
    thinking={"type": "enabled", "budget_tokens": 16384},
    output_config={"effort": "medium"},
    betas=["interleaved-thinking-2025-05-14"],
    messages=[...],
)
```

Note: `budget_tokens` is deprecated on Sonnet 4.6 but still functional. The `interleaved-thinking-2025-05-14` beta header is still supported on Sonnet 4.6.

## Migration from Sonnet 4.5

### Without Extended Thinking
Set effort explicitly. At `low` effort, expect similar or better performance vs Sonnet 4.5:

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=8192,
    output_config={"effort": "low"},
    messages=[...],
)
```

### With Extended Thinking
No changes needed. Keep budget around 16K tokens - provides headroom for harder problems.

## Coding Use Cases

Start with `medium` effort, budget_tokens ~16K:

```python
response = client.beta.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=16384,
    thinking={"type": "enabled", "budget_tokens": 16384},
    output_config={"effort": "medium"},
    betas=["interleaved-thinking-2025-05-14"],
    messages=[...],
)
```

If latency too high: reduce to `low`. Need more intelligence: increase to `high` or use Opus 4.6.

## Chat and Non-Coding Use Cases

Start with `low` effort:

```python
response = client.beta.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=8192,
    thinking={"type": "enabled", "budget_tokens": 16384},
    output_config={"effort": "low"},
    betas=["interleaved-thinking-2025-05-14"],
    messages=[...],
)
```

## When to Try Adaptive Thinking

Best for:
- **Autonomous multi-step agents:** Calibrates reasoning per step over long trajectories. Start at `high` effort.
- **Computer use agents:** Best-in-class accuracy on computer use evaluations in adaptive mode.
- **Bimodal workloads:** Mix of easy/hard tasks. Skips thinking on simple queries, reasons deeply on complex ones.

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=64000,
    thinking={"type": "adaptive"},
    output_config={"effort": "medium"},
    messages=[...],
)
```

If inconsistent behavior: switch back to `budget_tokens` for predictability.

## When to Use Opus 4.6 Instead

- Large-scale code migrations
- Deep research requiring extensive autonomous work
- Tasks needing `max` effort level
- Problems where absolute maximum intelligence matters more than speed

## Breaking Changes

1. Prefilling assistant messages returns 400 error
2. Tool parameter JSON escaping may differ - use standard JSON parsers
3. From Claude 3.x: Use only `temperature` OR `top_p`, not both
4. From Claude 3.x: Update tool versions (`text_editor_20250728`, `code_execution_20250825`)
5. Handle new `refusal` stop reason

# Chapter 5: Claude Haiku 4.5 Best Practices

Haiku 4.5 is the fastest model with near-frontier intelligence, ideal for high-volume processing at lowest cost.

## Key Specifications

| Feature | Value |
|---------|-------|
| API ID | `claude-haiku-4-5-20251001` (alias: `claude-haiku-4-5`) |
| Context Window | 200K tokens |
| Max Output | 64K tokens |
| Pricing | $1/input MTok, $5/output MTok |
| Latency | Fastest |

## Extended Thinking Support

Supports manual mode only (NOT adaptive thinking):

```python
response = client.messages.create(
    model="claude-haiku-4-5-20251001",
    max_tokens=16000,
    thinking={"type": "enabled", "budget_tokens": 10000},
    messages=[{"role": "user", "content": "Complex task..."}],
)
```

### When to Enable Thinking
- Complex classification requiring reasoning
- Multi-step analysis tasks
- Coding tasks where accuracy matters

### When to Skip Thinking
- Simple classification (positive/negative/neutral)
- Token routing or triage
- High-volume latency-critical tasks

## Best Use Cases

### High-Volume, Latency-Sensitive Workloads
- Real-time chat applications
- Content moderation at scale
- Document summarization pipelines

### Simple Classification
```python
response = client.messages.create(
    model="claude-haiku-4-5-20251001",
    max_tokens=100,
    system="Classify as positive, negative, or neutral. Respond with only the classification.",
    messages=[{"role": "user", "content": text}],
)
```

### Subagent Tasks
- Tool result summarization
- Routing decisions
- Quick validation checks
- Data extraction

## Cost Optimization

At $1/$5 per MTok, Haiku is 5x cheaper than Opus on input and output.

Strategies:
1. **Preprocess with Haiku:** Route docs through Haiku first, send complex cases to Sonnet/Opus
2. **Batch processing:** Use Batch API for non-time-sensitive work
3. **Minimize output tokens:** Instruct to respond with only the label
4. **Skip thinking for simple tasks:** Only enable when genuinely beneficial

## Prompting Tips

### Be Even More Explicit
Haiku particularly benefits from:
- Very specific output format instructions
- Explicit constraints on response length
- Clear examples of desired output

### Handle Missing Parameters
Haiku (like Sonnet) may guess missing tool parameters rather than asking. Use chain-of-thought prompting for better assessment.

## Haiku vs Sonnet

| Factor | Haiku 4.5 | Sonnet 4.6 |
|--------|-----------|------------|
| Speed | Fastest | Fast |
| Cost | $1/$5 MTok | $3/$15 MTok |
| Intelligence | Near-frontier | Strong frontier |
| Adaptive Thinking | No | Yes |
| Best for | High-volume, simple tasks | Complex coding, analysis |

# Chapter 6: Extended Thinking Deep Dive

Extended thinking gives Claude enhanced reasoning capabilities for complex tasks, providing transparency into its step-by-step thought process before delivering a final answer.

## How Extended Thinking Works

When enabled, Claude creates `thinking` content blocks containing its internal reasoning, then incorporates insights from this reasoning into its final response.

### Response Format
```json
{
  "content": [
    {
      "type": "thinking",
      "thinking": "Let me analyze this step by step...",
      "signature": "WaUjzkypQ2mUEVM36O2T..."
    },
    {
      "type": "text",
      "text": "Based on my analysis..."
    }
  ]
}
```

### Summarized Thinking (Claude 4 Models)
For Claude 4 models, the API returns a **summary** of Claude's full thinking process rather than raw thinking output. Key considerations:

- You're charged for the **full** thinking tokens generated, not the summary tokens
- The billed output token count will NOT match visible token count
- First few lines are more verbose (helpful for prompt engineering)
- Summarization is processed by a different model than the target model
- Claude Sonnet 3.7 continues to return full thinking output

## Three Thinking Modes

### 1. Adaptive Thinking (Recommended for Opus 4.6 & Sonnet 4.6)

```python
response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=16000,
    thinking={"type": "adaptive"},
    output_config={"effort": "high"},
    messages=[...],
)
```

- Claude dynamically determines when and how much to think
- Automatically enables interleaved thinking
- Use `effort` parameter to guide thinking depth
- No beta headers required

### 2. Manual Extended Thinking

```python
response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=16000,
    thinking={"type": "enabled", "budget_tokens": 10000},
    messages=[...],
)
```

- You set a fixed thinking token budget
- Deprecated on Opus 4.6 and Sonnet 4.6 (still functional)
- Required for Haiku 4.5, Opus 4.5, and older models
- `budget_tokens` must be less than `max_tokens` (except with interleaved thinking)

### 3. Disabled (Default)

Simply omit the `thinking` parameter for lowest latency when thinking isn't needed.

### Mode Comparison

| Mode | Config | Models | When to Use |
|------|--------|--------|-------------|
| Adaptive | `thinking: {type: "adaptive"}` | Opus 4.6, Sonnet 4.6 | Claude determines when/how much to think |
| Manual | `thinking: {type: "enabled", budget_tokens: N}` | All models (deprecated on 4.6) | Precise control over thinking spend |
| Disabled | Omit `thinking` | All models | Lowest latency, no thinking needed |

## Interleaved Thinking for Tool Use

Interleaved thinking enables Claude to think **between** tool calls, making more sophisticated reasoning after receiving tool results.

### Without Interleaved Thinking:
```
Turn 1: [thinking] → [tool_use: calculator]
Turn 2: [tool_use: database_query]     ← no thinking
Turn 3: [text: final answer]           ← no thinking
```

### With Interleaved Thinking:
```
Turn 1: [thinking] → [tool_use: calculator]
Turn 2: [thinking] → [tool_use: database_query]   ← thinks about result
Turn 3: [thinking] → [text: final answer]          ← thinks before answering
```

### Enabling Interleaved Thinking

| Model | How to Enable |
|-------|---------------|
| **Opus 4.6** | Automatic with adaptive thinking. Beta header deprecated. |
| **Sonnet 4.6** | Beta header `interleaved-thinking-2025-05-14` with manual thinking, OR automatic with adaptive thinking |
| **Other Claude 4 models** | Beta header `interleaved-thinking-2025-05-14` |

With interleaved thinking, `budget_tokens` can exceed `max_tokens` as it represents total budget across all thinking blocks in one assistant turn.

## Budget Optimization

### Starting Points
- **Minimum budget:** 1,024 tokens
- **Start small** and increase incrementally
- **16K+ tokens** for complex tasks
- **Above 32K:** Use batch processing to avoid networking timeouts

### Effort Levels (Adaptive Thinking)

| Effort | Thinking Behavior |
|--------|-------------------|
| `max` | Always thinks, no constraints (Opus 4.6 only) |
| `high` | Always thinks, deep reasoning |
| `medium` | Moderate thinking, may skip for simple queries |
| `low` | Minimal thinking, skips for simple tasks |

### Cost Control
Use `max_tokens` as a hard limit on total output (thinking + response). The `effort` parameter provides soft guidance on thinking allocation.

## Prompting Tips for Extended Thinking

### Use General Instructions First
Claude often performs better with high-level instructions rather than step-by-step prescriptive guidance:

**Less effective:**
```
Think through this math problem step by step:
1. First, identify the variables
2. Then, set up the equation
3. Next, solve for x
```

**More effective:**
```
Please think about this math problem thoroughly and in great detail.
Consider multiple approaches and show your complete reasoning.
Try different methods if your first approach doesn't work.
```

### Multishot Prompting with Extended Thinking
Use XML tags like `<thinking>` or `<scratchpad>` in examples to indicate canonical thinking patterns:

```
Problem 1: What is 15% of 80?
<thinking>
To find 15% of 80:
1. Convert 15% to a decimal: 0.15
2. Multiply: 0.15 × 80 = 12
</thinking>
The answer is 12.

Now solve: Problem 2: What is 35% of 240?
```

### Debugging with Thinking Output
Use thinking output to debug Claude's logic:
- **Do NOT** pass thinking output back in user text blocks (degrades performance)
- Prefilling extended thinking is explicitly not allowed
- Manually changing output text after thinking degrades results

### Have Claude Verify Its Work
```
Write a function to calculate factorial. Before you finish, verify your
solution with test cases for n=0, n=1, n=5, n=10. Fix any issues you find.
```

## Handling Redacted Thinking Blocks

Occasionally, Claude's reasoning is flagged by safety systems and returned as encrypted `redacted_thinking` blocks:

```json
{
  "type": "redacted_thinking",
  "data": "EmwKAhgBEgy3va3pzix/LafPsn4a..."
}
```

- Redacted blocks are decrypted when passed back to the API
- Claude can still use this reasoning without losing context
- Consider explaining to users: "Some internal reasoning has been automatically encrypted for safety reasons"
- Test handling with: `ANTHROPIC_MAGIC_STRING_TRIGGER_REDACTED_THINKING_46C9A13E193C177646C7398A98432ECCCE4C1253D5E2D82641AC0E52CC2876CB`

## Prompt Caching Considerations

### What's Cached
- System prompts and tool definitions remain cached despite thinking parameter changes
- Consecutive requests with same thinking mode preserve cache breakpoints

### What Invalidates Cache
- Changes to thinking parameters (enabled/disabled or budget allocation)
- Switching between adaptive and enabled/disabled modes
- Interleaved thinking amplifies cache invalidation

### Best Practice
For extended thinking tasks that take 5+ minutes, use 1-hour cache duration to maintain cache hits across longer sessions.

## Thinking Block Preservation (Claude Opus 4.5+)

Starting with Claude Opus 4.5 (including Opus 4.6), thinking blocks from previous assistant turns are **preserved in context by default**:

- **Cache optimization:** Preserved blocks enable cache hits with tool results
- **No intelligence impact:** Preserving blocks doesn't affect performance
- **Context usage:** Long conversations consume more context space
- Earlier models (Sonnet 4.5, Opus 4.1) still remove thinking from previous turns

## Pricing

The thinking process charges for:
- **Thinking tokens** (billed as output tokens at full rate)
- **Thinking blocks from last assistant turn** in subsequent requests (input tokens)
- **Standard text output tokens**

**Important:** When using summarized thinking:
- Input tokens: Your original request (excludes previous thinking)
- Output tokens (billed): Full thinking tokens generated internally
- Output tokens (visible): Summarized tokens in response
- No charge for generating the summary itself

The billed output count will NOT match the visible count. You pay for the full thinking process, not the summary.

## Feature Compatibility Notes

- Thinking is NOT compatible with `temperature` or `top_k` modifications
- `top_p` can be set between 1 and 0.95 when thinking is enabled
- Cannot pre-fill responses when thinking is enabled
- Forced tool use (`tool_choice: "any"` or `tool_choice: {type: "tool"}`) is incompatible
- Only `tool_choice: "auto"` or `tool_choice: "none"` work with thinking

# Chapter 7: Agentic Patterns and Tool Use

Claude is capable of interacting with tools and functions, allowing you to extend its capabilities for a wider variety of tasks. This chapter covers tool use fundamentals and agentic patterns specific to Claude's latest models.

## How Tool Use Works

Claude supports two types of tools:

### 1. Client Tools

Tools that execute on your systems:
- User-defined custom tools
- Anthropic-defined tools (computer use, text editor) requiring client implementation

### 2. Server Tools

Tools that execute on Anthropic's servers:
- Web search and web fetch tools
- Server runs a sampling loop with up to 10 iterations
- Handle `pause_turn` stop reason when the loop limit is reached

### Tool Use Flow (Client Tools)

1. **Provide Claude with tools and a user prompt** - Define tools with names, descriptions, and input schemas
2. **Claude decides to use a tool** - Constructs a tool use request (response has `stop_reason: "tool_use"`)
3. **Execute the tool and return results** - Extract tool name/input, execute, return `tool_result`
4. **Claude formulates a response** - Analyzes results for final response

### Basic Tool Definition

```python
import anthropic
client = anthropic.Anthropic()

response = client.messages.create(
    model="claude-opus-4-6",
    max_tokens=1024,
    tools=[{
        "name": "get_weather",
        "description": "Get the current weather in a given location",
        "input_schema": {
            "type": "object",
            "properties": {
                "location": {
                    "type": "string",
                    "description": "The city and state, e.g. San Francisco, CA"
                }
            },
            "required": ["location"]
        }
    }],
    messages=[{"role": "user", "content": "What's the weather in San Francisco?"}],
)
```

### Structured Outputs for Tools

Add `strict: true` to tool definitions for guaranteed schema validation:

```python
tools=[{
    "name": "classify_text",
    "description": "Classify text into categories",
    "strict": True,
    "input_schema": {
        "type": "object",
        "properties": {
            "category": {"type": "string", "enum": ["positive", "negative", "neutral"]}
        },
        "required": ["category"]
    }
}]
```

## Parallel Tool Calling Optimization

Claude's latest models excel at parallel tool execution. They will:
- Run multiple speculative searches during research
- Read several files at once to build context faster
- Execute bash commands in parallel

### Maximizing Parallel Execution

```text
<use_parallel_tool_calls>
If you intend to call multiple tools and there are no dependencies between
the tool calls, make all of the independent tool calls in parallel. Prioritize
calling tools simultaneously whenever the actions can be done in parallel
rather than sequentially. For example, when reading 3 files, run 3 tool calls
in parallel to read all 3 files into context at the same time. Maximize use
of parallel tool calls where possible to increase speed and efficiency.
However, if some tool calls depend on previous calls to inform dependent
values, do NOT call these tools in parallel.
</use_parallel_tool_calls>
```

### Reducing Parallel Execution (if needed)

```text
Execute operations sequentially with brief pauses between each step to
ensure stability.
```

## Tool Usage and Triggering

Claude Opus 4.5 and Claude 4.6 models are more responsive to the system prompt than previous models. If your prompts were designed to reduce undertriggering on tools, these models may now **overtrigger**.

### Fix: Dial back aggressive language

- **Before:** "CRITICAL: You MUST use this tool when..."
- **After:** "Use this tool when..."

### Proactive vs Conservative Action

**For proactive action:**

```text
<default_to_action>
By default, implement changes rather than only suggesting them. If the user's
intent is unclear, infer the most useful likely action and proceed, using tools
to discover any missing details instead of guessing.
</default_to_action>
```

**For conservative action:**

```text
<do_not_act_before_instructions>
Do not jump into implementation unless clearly instructed to make changes.
When the user's intent is ambiguous, default to providing information and
recommendations rather than taking action.
</do_not_act_before_instructions>
```

### Missing Parameters

- **Opus** is much more likely to recognize missing parameters and ask for them
- **Sonnet and Haiku** may guess or infer values
- Use chain-of-thought prompting for better parameter assessment on Sonnet/Haiku

## Reducing File Creation in Agentic Coding

Claude's latest models may create new files for testing and iteration (using files as a "temporary scratchpad"). This can improve outcomes but creates file clutter.

```text
If you create any temporary new files, scripts, or helper files for iteration,
clean up these files by removing them at the end of the task.
```

## Research and Information Gathering Patterns

Claude's latest models demonstrate exceptional agentic search capabilities.

### Structured Research Approach

```text
Search for this information in a structured way. As you gather data, develop
several competing hypotheses. Track your confidence levels in your progress
notes to improve calibration. Regularly self-critique your approach and plan.
Update a hypothesis tree or research notes file to persist information and
provide transparency. Break down this complex research task systematically.
```

This structured approach allows Claude to find and synthesize virtually any information and iteratively critique its findings.

## State Management Best Practices

### Structured Formats for State Data

```json
{
  "tests": [
    {"id": 1, "name": "authentication_flow", "status": "passing"},
    {"id": 2, "name": "user_management", "status": "failing"},
    {"id": 3, "name": "api_endpoints", "status": "not_started"}
  ],
  "total": 200,
  "passing": 150,
  "failing": 25,
  "not_started": 25
}
```

### Unstructured Text for Progress Notes

```text
Session 3 progress:
- Fixed authentication token validation
- Updated user model to handle edge cases
- Next: investigate user_management test failures (test #2)
- Note: Do not remove tests as this could lead to missing functionality
```

### Git for State Tracking

Git provides a log of what's been done and checkpoints that can be restored. Claude's latest models perform especially well at using git to track state across multiple sessions.

### Key Principles:

- Use JSON for structured state (test results, task status)
- Use plain text for progress notes
- Use git for version control and state persistence
- Emphasize incremental progress explicitly in prompts

## Minimizing Hallucinations in Agentic Coding

Claude's latest models are less prone to hallucinations and give more grounded answers. Reinforce this:

```text
<investigate_before_answering>
Never speculate about code you have not opened. If the user references a
specific file, you MUST read the file before answering. Make sure to investigate
and read relevant files BEFORE answering questions about the codebase. Never
make any claims about code before investigating unless you are certain of
the correct answer.
</investigate_before_answering>
```

## Avoiding Hard-Coding and Test-Focused Solutions

Claude can sometimes focus too heavily on making tests pass at the expense of general solutions.

```text
Write a high-quality, general-purpose solution using standard tools. Do not
create helper scripts or workarounds. Implement a solution that works correctly
for all valid inputs, not just test cases. Do not hard-code values or create
solutions that only work for specific test inputs.

If the task is unreasonable or tests are incorrect, inform me rather than
working around them.
```

## Multi-Context Window Workflows

For complex tasks spanning multiple context windows:

### 1. Tests First

Have Claude write tests before implementation and track them in structured format:

```text
Create tests before starting work. Track results in tests.json.
It is unacceptable to remove or edit tests - this could lead to
missing or buggy functionality.
```

### 2. Setup Scripts

Encourage creation of initialization scripts:

```text
Create an init.sh script to gracefully start servers, run test suites,
and linters. This prevents repeated work in fresh context windows.
```

### 3. Fresh Starts

When context is cleared, consider a fresh start over compaction. Prescribe how Claude should begin:

```text
Call pwd; you can only read and write files in this directory.
Review progress.txt, tests.json, and the git logs.
Manually run through a fundamental integration test before implementing
new features.
```

### 4. Efficient Context Usage

```text
This is a very long task, so plan your work clearly. It's encouraged to
spend your entire output context working on the task - just make sure you
don't run out of context with significant uncommitted work. Continue working
systematically until you have completed this task.
```

## Tool Use Pricing

Tool use costs are based on:
1. Total input tokens (including the `tools` parameter)
2. Output tokens generated
3. Server-side tools may have additional usage-based pricing

When tools are provided, a special system prompt is automatically included:
- `auto`/`none` tool choice: ~346 additional tokens
- `any`/`tool` choice: ~313 additional tokens

## MCP Tool Integration

Convert MCP (Model Context Protocol) tools to Claude format by renaming `inputSchema` to `input_schema`:

```python
async def get_claude_tools(mcp_session):
    mcp_tools = await mcp_session.list_tools()
    return [{
        "name": tool.name,
        "description": tool.description or "",
        "input_schema": tool.inputSchema,
    } for tool in mcp_tools.tools]
```

Or use the MCP connector to connect directly to remote MCP servers from the Messages API without implementing a client.