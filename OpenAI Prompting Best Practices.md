# OpenAI Prompting Best Practices

A comprehensive guide to prompting and building with OpenAI's models and APIs, sourced from official OpenAI documentation.

---

## Chapter 1: Introduction & Model Overview

### Current Model Lineup

OpenAI offers a diverse range of models optimized for different use cases. Understanding the model landscape is essential for selecting the right tool for your task.

#### Flagship Models

| Model | Type | API ID | Input $/MTok | Output $/MTok | Context Window | Max Output | Key Features |
|-------|------|--------|-------------|--------------|----------------|------------|--------------|
| **o3** | Reasoning | `o3` | $2.00 | $8.00 | 200K | 100K | Most powerful reasoning, tool use during thinking |
| **o4-mini** | Reasoning | `o4-mini` | $1.10 | $4.40 | 200K | 100K | Fast, cost-efficient reasoning; best on AIME benchmarks |
| **GPT-4.1** | Chat | `gpt-4.1` | $2.00 | $8.00 | 1M | 32K | Best general-purpose, 1M context, strong instruction following |
| **GPT-4.1 mini** | Chat | `gpt-4.1-mini` | $0.40 | $1.60 | 1M | 32K | Cost-optimized with large context |
| **GPT-4.1 nano** | Chat | `gpt-4.1-nano` | $0.10 | $0.40 | 1M | 32K | Fastest, cheapest, large context |
| **GPT-4o** | Chat | `gpt-4o` | $2.50 | $10.00 | 128K | 16K | Multimodal, widely available |
| **GPT-4o mini** | Chat | `gpt-4o-mini` | $0.15 | $0.60 | 128K | 16K | Ultra-low cost multimodal |

#### Specialized & Frontier Models

| Model | Type | API ID | Input $/MTok | Output $/MTok | Context Window | Key Features |
|-------|------|--------|-------------|--------------|----------------|--------------|
| **GPT-5.3-Codex** | Coding Agent | `gpt-5.3-codex` | TBD | TBD | — | Most capable agentic coding model, long-horizon tasks |
| **GPT-4.5** | Research Preview | `gpt-4.5-preview` | $75.00 | $150.00 | 128K | Largest pre-trained model, reduced hallucinations (deprecated) |
| **gpt-image-1.5** | Image Gen | `gpt-image-1.5` | — | — | — | 4x faster image generation, text rendering |
| **gpt-image-1** | Image Gen | `gpt-image-1` | — | — | — | Natively multimodal image generation |
| **gpt-realtime** | Voice | `gpt-realtime` | — | — | — | Production-ready voice model, low-latency |
| **text-embedding-3-small** | Embeddings | `text-embedding-3-small` | $0.02 | — | 8K | Fast, compact embeddings |
| **text-embedding-3-large** | Embeddings | `text-embedding-3-large` | $0.13 | — | 8K | Most capable embeddings |
| **omni-moderation** | Moderation | `omni-moderation-latest` | Free | — | — | Content safety for text + images |

### Reasoning vs. Chat Model Distinction

**Chat models** (GPT-4.1 family, GPT-4o family) generate responses directly. They are fast, cost-efficient, and excel at instruction following, creative writing, coding, and general tasks. They benefit from detailed, explicit prompts.

**Reasoning models** (o3, o4-mini) use internal "reasoning tokens" — a hidden chain of thought — before producing output. They excel at complex multi-step problems, math, science, and tasks requiring deep analysis. They work best with high-level guidance rather than overly prescriptive instructions.

### How to Choose the Right Model

Use this framework to select models:

1. **Cost-sensitive, simple tasks** → GPT-4.1 nano or GPT-4o mini
2. **General-purpose with quality** → GPT-4.1 (best balance of quality, speed, cost)
3. **Complex reasoning required** → o3 (highest quality) or o4-mini (cost-efficient reasoning)
4. **Long documents (100K+ tokens)** → GPT-4.1 family (1M context window)
5. **Agentic coding workflows** → GPT-5.3-Codex (via Codex) or GPT-4.1 (via API)
6. **Image understanding** → GPT-4o or GPT-4.1 (vision capabilities)
7. **Image generation** → gpt-image-1.5 or gpt-image-1
8. **Real-time voice** → gpt-realtime
9. **Embeddings/search** → text-embedding-3-small (fast) or text-embedding-3-large (quality)

### Cost Optimization

- **Prompt Caching**: GPT-5 family gets 90% off cached tokens; GPT-4.1 family gets 75% off; GPT-4o and o-series get 50% off
- **Batch API**: 50% cost reduction with 24-hour turnaround for all supported models
- **Model tiering**: Start with cheaper models and escalate to more capable ones only when needed

---

## Chapter 2: Universal Prompting Techniques

These techniques come from OpenAI's official prompt engineering guide and apply across all models.

### Strategy 1: Write Clear Instructions

Models cannot read your mind. Be explicit about what you want.

**Include relevant details in your query.** The more context you provide, the better the output. Don't assume the model knows your specific context.

**Adopt a persona.** Use the system message to set a persona: "You are a senior software engineer reviewing code for security vulnerabilities."

**Use delimiters to clearly indicate distinct parts of the input.** Triple quotes, XML tags, markdown headers, or section titles help the model distinguish instructions from content:

```
Summarize the text delimited by triple quotes.

"""Insert text here"""
```

**Specify the steps required to complete a task.** Break complex tasks into numbered steps:

```
Step 1: Analyze the user's query for intent
Step 2: Search the knowledge base for relevant articles
Step 3: Synthesize a response using only the found articles
Step 4: Format the response with citations
```

**Provide examples (few-shot prompting).** Show the model what you want through concrete examples of input-output pairs.

**Specify the desired length of the output.** Tell the model whether you want a brief summary, a detailed analysis, or a specific word/paragraph count.

### Strategy 2: Provide Reference Text

**Instruct the model to answer using reference text.** Providing documents or data for the model to cite reduces hallucination:

```
Use the following articles to answer the question. If the answer cannot be found in the articles, write "I could not find an answer."

<articles>
{context}
</articles>

Question: {question}
```

**Instruct the model to answer with citations.** Request inline citations from the reference material to make answers verifiable.

### Strategy 3: Split Complex Tasks into Simpler Subtasks

**Use intent classification to route queries.** For systems handling diverse queries, first classify the intent, then route to specialized handling:

```
Classify the following customer query into one of these categories:
- Billing
- Technical Support
- Account Management
- General Inquiry

Query: {user_message}
```

**Summarize long documents piecewise.** For documents exceeding the context window, use recursive summarization — summarize sections, then summarize the summaries.

**Use dialogue management for multi-turn conversations.** Summarize or filter previous conversation turns to keep context focused and within token limits.

### Strategy 4: Give the Model Time to Think

**Instruct the model to work out its own solution before reaching a conclusion.** Rather than asking "Is this answer correct?", ask the model to solve the problem independently first, then compare.

**Use inner monologue or chain of thought.** Ask the model to put its reasoning in a structured format that can be parsed separately:

```
Think step by step. First, work out your own solution to the problem. Then compare your solution to the student's solution.
```

**Ask the model whether it missed anything.** After an initial response, prompt the model to review its work: "Are there any items from the document that you missed in your analysis?"

### Strategy 5: Use External Tools

**Use embeddings-based retrieval for knowledge search.** Combine vector search (RAG) with model generation for accurate, up-to-date answers.

**Use code execution for accurate calculations.** The model can write and execute code rather than doing mental math, reducing errors.

**Use function calling to interact with external systems.** Define functions the model can call to query databases, APIs, or trigger actions.

### Strategy 6: Test Changes Systematically

**Evaluate outputs with reference to gold-standard answers.** Create evaluation datasets with expected outputs and measure how prompt changes affect quality. Use automated metrics and human review.

**Pin to specific model versions.** Use snapshot IDs like `gpt-4.1-2025-04-14` in production to ensure consistent behavior across prompt iterations.

---

## Chapter 3: GPT-4.1 Family Best Practices

### GPT-4.1: The General-Purpose Workhorse

GPT-4.1 is OpenAI's recommended general-purpose model. Key specs:
- **1M token context window** — the largest among OpenAI's production models
- **$2.00/$8.00 per MTok** (input/output)
- **32K max output tokens**
- **75% prompt caching discount**
- Follows instructions more closely and literally than predecessors
- State-of-the-art for non-reasoning models on SWE-bench Verified (55%)

### GPT-4.1 Mini and Nano

**GPT-4.1 mini** ($0.40/$1.60): Best for cost-sensitive applications needing quality. Same 1M context.

**GPT-4.1 nano** ($0.10/$0.40): Fastest and cheapest. Ideal for classification, autocomplete, routing, and high-volume tasks.

### Instruction Hierarchy and Structure

GPT-4.1 follows instructions more literally than predecessors. This is both powerful and requires care:

**Recommended prompt structure:**
```

# Role and Objective
[Who the model is and what it should accomplish]

# Instructions
## Sub-categories for detailed behaviors
[Specific rules and guidelines]

# Reasoning Steps
[Ordered workflow steps]

# Output Format
[How to structure the response]

# Examples
[Input-output pairs showing desired behavior]

# Context
[Reference documents, data]

# Final instructions
[Recap key rules; prompt for step-by-step thinking]
```

**Key principles:**
- Place instructions at both the beginning AND end of context material (performs better than single placement)
- If instructions appear only once, place them BEFORE the context
- When instructions conflict, GPT-4.1 prioritizes those appearing closer to the end
- Start with high-level "Response Rules" section, then add category-specific subsections
- Use markdown headers (H1-H4) for structure — this is the recommended delimiter format
- XML tags are excellent for nesting and metadata, especially for large document collections
- Avoid JSON for prompt structure (verbose and performed poorly in testing)

### Agentic Coding Patterns

GPT-4.1 excels at agentic workflows. Include these three essential reminders in agent prompts:

**1. Persistence reminder:**
> "You are an agent — please keep going until the user's query is completely resolved, before ending your turn and yielding back to the user."

**2. Tool-calling reminder:**
> "Use your tools to read files and gather the relevant information: do NOT guess or make up an answer."

**3. Planning reminder (optional):**
> "Plan extensively before each function call, and reflect extensively on the outcomes."

These three reminders increased internal SWE-bench Verified scores by approximately 20%.

### Tool Calling Best Practices for GPT-4.1

- **Use the API `tools` field** — don't manually inject tool descriptions into prompts. API-based tool passing showed 2% improvement over manual schema injection.
- **Write clear tool descriptions** — be thorough but concise in the `description` field
- **Put examples in the system prompt** — place complex usage examples in a dedicated `# Examples` section, not in tool descriptions
- **Consider disabling parallel tool calls** — set `parallel_tool_calls: false` if you see incorrect parallel invocations

### Long Context Handling (1M Tokens)

GPT-4.1 has strong performance across its full 1M token context window. Best practices:

- **Tune context reliance:** Choose between "only use provided context" vs. "use context but supplement with your knowledge"
- **Instruction placement matters:** Instructions at both beginning and end of context works best
- **Performance degrades** when retrieval requires accessing many scattered items or complex graph-based reasoning
- **Use XML or pipe-delimited format** for document collections (e.g., `ID: 1 | TITLE: X | CONTENT: Y`)

### Chain of Thought for Non-Reasoning Tasks

GPT-4.1 is not a reasoning model, but responds well to step-by-step prompting:

```
Think carefully step by step about what documents are needed to answer the query.

1. Query Analysis: Break down and analyze the query
2. Context Analysis: Select potentially relevant documents
3. Synthesis: Summarize most relevant documents and reasoning
```

Prompting-induced planning improved task performance by 4% in testing.

### When to Use GPT-4.1 vs. Reasoning Models

| Use Case | GPT-4.1 | o3/o4-mini |
|----------|---------|------------|
| Instruction following | Excellent | Good |
| Speed | Fast | Slower |
| Cost | Lower | Higher |
| Simple reasoning | Good | Overkill |
| Complex math/science | Moderate | Excellent |
| Multi-step analysis | Good with CoT | Excellent (built-in) |
| Agentic coding | Strong (55% SWE-bench) | Strong with tool use |
| Creative writing | Excellent | Good |
| Long context (>200K) | 1M window | 200K window |

---

## Chapter 4: Reasoning Models (o3 & o4-mini) Best Practices

### How Reasoning Tokens Work

Reasoning models (o3, o4-mini) use internal "reasoning tokens" — a hidden chain of thought that is not visible in the output. The model thinks through the problem before producing a final answer. You are billed for reasoning tokens as output tokens.

Key differences from chat models:
- The model generates an internal chain of thought before responding
- Reasoning tokens are hidden from the API response (but you can see summaries)
- Temperature is fixed at 1 (no temperature control)
- No system messages — use **developer messages** instead

### o3: Most Powerful Reasoning

- **$2.00/$8.00 per MTok** (input/output), reasoning tokens at output rate
- **200K context window**, up to 100K output tokens
- Makes 20% fewer major errors than o1 on difficult real-world tasks
- First reasoning model to use tools during the thinking process
- Excels at programming, business/consulting, and creative ideation

### o4-mini: Fast, Cost-Efficient Reasoning

- **$1.10/$4.40 per MTok** (input/output)
- **200K context window**, up to 100K output tokens
- Best-performing benchmarked model on AIME 2024 and 2025
- ~10x cheaper than o3 for input and output
- Supports significantly higher usage limits than o3
- Best for high-volume, high-throughput reasoning tasks

### Reasoning Effort Levels

Control how much the model thinks with the `reasoning_effort` parameter:

| Level | Behavior | Best For |
|-------|----------|----------|
| `low` | Quick, minimal reasoning | Simple queries, classification |
| `medium` | Balanced reasoning | Most tasks |
| `high` | Deep reasoning (default) | Complex problems |

Higher effort = more reasoning tokens = higher cost but better quality on hard problems.

### Reasoning Summaries

You can request summaries of the model's internal reasoning process. This provides visibility into the thinking without exposing the full chain of thought. Useful for debugging and understanding model decisions in streaming contexts.

### Tool Use During Reasoning

o3 and o4-mini are the first models that can use tools during their internal thinking process. This means:
- The model can search the web, read files, or call functions as part of its reasoning
- Tool results are incorporated into the chain of thought before producing the final answer
- This dramatically improves accuracy for tasks requiring external data

### Best Practices for Reasoning Models

1. **Keep prompts high-level.** Reasoning models work best with broad guidance, not overly prescriptive step-by-step instructions. The model will figure out the best approach.

2. **Use developer messages instead of system messages.** Reasoning models use `developer` role rather than `system` role:
   ```json
   {"role": "developer", "content": "You are a helpful math tutor."}
   ```

3. **Don't ask the model to "think step by step."** The model already reasons internally — adding chain-of-thought prompts is redundant and can hurt performance.

4. **Provide context, not procedures.** Give the model the information it needs and the desired outcome, not a rigid procedure to follow.

5. **Use structured outputs for reliable formatting.** Combine reasoning with `strict: true` for guaranteed JSON schema adherence.

6. **Temperature is fixed at 1.** You cannot adjust temperature for reasoning models.

### When to Use Reasoning vs. Chat Models

**Use reasoning models when:**
- The task requires multi-step logical analysis
- Mathematical or scientific reasoning is needed
- The problem has a verifiable correct answer
- You need the model to catch subtle errors
- Complex code analysis or debugging

**Use chat models when:**
- Speed and cost are priorities
- The task is primarily creative or editorial
- You need precise instruction following
- Long context (>200K tokens) is required
- Simple classification, routing, or formatting

---

## Chapter 5: Responses API & Chat Completions

### Responses API: Recommended for New Projects

The Responses API (launched March 2025) is OpenAI's modern interface for building AI applications. It's accessed via `client.responses.create()`.

**Key advantages over Chat Completions:**
- **40-80% better cache utilization** in internal tests
- **Built-in tool orchestration**: web search, file search, code interpreter, computer use, image generation
- **Multi-tool agentic loops** in a single API call
- **Simplified state management** with `previous_response_id` chaining
- **3% improvement on SWE-bench** with same prompt and setup vs Chat Completions

### How the Responses API Works

The Responses API uses **Items** instead of Messages. An Item is a union of many types representing the range of model actions:

```python
from openai import OpenAI
client = OpenAI()

response = client.responses.create(
    model="gpt-4.1",
    input="Explain quantum computing in simple terms",
    tools=[{"type": "web_search_preview"}]
)
```

**Agentic loop:** The model can call multiple tools — web search, image generation, file search, code interpreter, remote MCP servers, and custom functions — within the span of one API request. The API handles the tool execution loop automatically.

### Conversation State Management

Chain responses together using `previous_response_id`:

```python
response1 = client.responses.create(
    model="gpt-4.1",
    input="What is the capital of France?"
)

response2 = client.responses.create(
    model="gpt-4.1",
    input="What is its population?",
    previous_response_id=response1.id
)
```

The Responses API also integrates with the Conversations API for persistent, server-managed conversation state.

### Chat Completions API

The legacy Chat Completions endpoint remains fully supported at `client.chat.completions.create()`. It uses an array of Messages with roles (system, user, assistant).

```python
response = client.chat.completions.create(
    model="gpt-4.1",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
    ]
)
```

**When to stay on Chat Completions:**
- Existing applications with extensive Chat Completions integration
- Third-party libraries that only support Chat Completions
- Simple request-response patterns without tool orchestration

### Migration from Chat Completions to Responses API

1. Update endpoint from `POST /v1/chat/completions` to `POST /v1/responses`
2. If not using functions or multimodal inputs, simple message inputs are compatible
3. Replace `messages` array with `input` parameter
4. Replace `functions`/`tools` with the Responses API `tools` format
5. Update response parsing from `choices[0].message` to Items-based format

### Assistants API Sunset

The Assistants API will be sunset on **August 26, 2026**. All applications should migrate to the Responses API. The Chat Completions API will continue to be supported but Responses API is recommended for all new projects.

---

## Chapter 6: Function Calling & Tool Use

### Overview

Function calling allows models to generate structured JSON arguments for functions you define. The model doesn't execute functions — it produces the arguments, and your code handles execution.

### Function Calling with the Responses API

```python
tools = [{
    "type": "function",
    "name": "get_weather",
    "description": "Get the current weather for a location",
    "parameters": {
        "type": "object",
        "properties": {
            "location": {
                "type": "string",
                "description": "City and state, e.g., 'San Francisco, CA'"
            },
            "unit": {
                "type": "string",
                "enum": ["celsius", "fahrenheit"]
            }
        },
        "required": ["location"],
        "additionalProperties": False
    },
    "strict": True
}]

response = client.responses.create(
    model="gpt-4.1",
    input="What's the weather in San Francisco?",
    tools=tools
)
```

### Tool Choice Modes

| Mode | Behavior |
|------|----------|
| `auto` | Model decides whether to call a function (default) |
| `required` | Model must call at least one function |
| `none` | Model cannot call any function |
| `{"type": "function", "name": "fn_name"}` | Force a specific function |

### Parallel Function Calling

Models can generate multiple function calls in a single response. However:

- **Structured Outputs is not compatible with parallel function calls** — when parallel calls are generated, they may not match supplied schemas
- Set `parallel_tool_calls: false` to disable parallel calling when using `strict: true`

### Built-in Tools (Responses API)

The Responses API provides pre-built tools that run server-side:

| Tool | Description |
|------|-------------|
| `web_search_preview` | Real-time web search with citations |
| `file_search` | Semantic + keyword search across uploaded files |
| `code_interpreter` | Sandboxed Python execution |
| `computer_use_preview` | Automated UI interaction |
| `image_generation` | Generate images inline |

### Remote MCP Server Integration

Connect to external Model Context Protocol (MCP) servers:

```python
tools = [{
    "type": "mcp",
    "server_label": "my_server",
    "server_url": "https://my-mcp-server.example.com",
    "require_approval": "never"
}]
```

### Best Practices for Function Design

1. **Write clear, detailed descriptions** — describe the purpose of each function and parameter, including formats and what the output represents
2. **Use strong typing** — leverage enums, specific types, and object structures to make invalid states unrepresentable
3. **Use `strict: true`** — enables guaranteed schema adherence (Structured Outputs for tools)
4. **Set `additionalProperties: false`** and mark all fields as `required` (required for strict mode)
5. **Limit parameters** — keep functions focused; split complex operations into multiple functions
6. **Include examples and edge cases** in the system prompt, not in tool descriptions
7. **Handle errors gracefully** — return informative error messages that help the model retry
8. **Use the system prompt** to describe when (and when not) to use each function

---

## Chapter 7: Structured Outputs

### JSON Mode vs. Structured Outputs

| Feature | JSON Mode | Structured Outputs (`strict: true`) |
|---------|-----------|-------------------------------------|
| Guarantee | Valid JSON | Valid JSON matching your exact schema |
| Schema adherence | Best effort | Guaranteed (no hallucinated keys/values) |
| Setup | `response_format: {"type": "json_object"}` | `response_format: {"type": "json_schema", "json_schema": {...}}` |
| Refusal handling | N/A | Model can refuse while maintaining schema |

**Always prefer Structured Outputs** when you need reliable, schema-compliant responses.

### Schema Definition

You can define schemas with Pydantic (Python), Zod (TypeScript), or raw JSON Schema.

**Pydantic example:**
```python
from pydantic import BaseModel

class CalendarEvent(BaseModel):
    name: str
    date: str
    participants: list[str]

response = client.responses.create(
    model="gpt-4.1",
    input="Extract the event details: Alice and Bob are meeting for lunch on Friday.",
    text={"format": {"type": "json_schema", "name": "event", "schema": CalendarEvent.model_json_schema()}}
)
```

### Guaranteed Schema Adherence

With `strict: true`, the model will NEVER:
- Add extra keys not in your schema
- Omit required keys
- Use wrong types for values
- Produce malformed JSON

This is achieved through constrained decoding at the token level.

### Where Structured Outputs Are Supported

- Responses API
- Chat Completions API
- Assistants API
- Fine-tuning
- Batch API

### Streaming with Partial JSON

Structured Outputs support streaming with partial JSON delivery. You receive incremental JSON tokens that build up to the complete, valid response.

### Refusal Handling

The model can refuse to generate content that violates safety policies while still returning valid JSON. A refusal is indicated by a special field in the response.

### Schema Requirements for Strict Mode

- `additionalProperties` must be `false` for every object
- All fields in `properties` must be marked as `required`
- Optional fields should use `{"type": ["string", "null"]}` union types
- Recursive schemas are supported
- Some features like `patternProperties` are not supported

### When to Use Each Approach

- **Structured Outputs**: When you need guaranteed schema compliance (data extraction, API responses, structured analysis)
- **JSON Mode**: When you need valid JSON but schema flexibility (exploratory analysis, variable-structure responses)
- **Neither**: When free-form text is acceptable

---

## Chapter 8: Multimodal Capabilities

### Vision: Image Understanding

GPT-4o and GPT-4.1 can analyze images provided as URLs or base64-encoded data.

**Input methods:**
- Image URL: `{"type": "input_image", "image_url": "https://..."}`
- Base64: `{"type": "input_image", "image_url": "data:image/jpeg;base64,..."}`

**Detail levels:**
| Level | Behavior | Token Cost |
|-------|----------|------------|
| `auto` | Model decides resolution (default) | Varies |
| `low` | 512x512, fixed cost | 85 tokens |
| `high` | Detailed analysis, multiple tiles | 85 + 170 per tile |

**Capabilities:** OCR, chart/graph analysis, object identification, spatial reasoning, document understanding, UI screenshot analysis.

**Multiple images:** You can send multiple images in a single request for comparison or multi-page document analysis.

### Image Generation

**gpt-image-1.5** (latest): 4x faster than gpt-image-1, generates in 10-30 seconds. Superior text rendering.

**gpt-image-1**: Natively multimodal — understands text AND image inputs through a unified transformer. Uses broad world knowledge for realistic details.

**Key features:**
- Text rendering in images
- Image editing and variations
- Multiple sizes and quality settings
- Available as a built-in tool in the Responses API for inline generation

**DALL-E 3**: Previous generation, still available. Good for creative illustrations.

### Audio: Speech-to-Text

**Models:**
- `gpt-4o-transcribe`: Best accuracy, improved language recognition
- `gpt-4o-mini-transcribe`: Fast, cost-efficient transcription
- `whisper-1`: Original Whisper model

**Features:** Transcription, translation, timestamps (word-level and segment-level), multiple audio format support.

### Audio: Text-to-Speech

**Model:** `gpt-4o-mini-tts` — steerable TTS that responds to instructions about HOW to speak (tone, emotion, pacing).

**Built-in voices (13+):** alloy, ash, ballad, coral, echo, fable, onyx, nova, sage, shimmer, verse, marin, cedar.

**Marin and Cedar** are the newest voices with the most natural-sounding speech.

**Features:** Streaming output, multiple output formats, speed control, instruction-following for voice characteristics.

### Realtime API

The Realtime API provides WebSocket-based, low-latency voice conversations. It reached general availability in August 2025.

**Model:** `gpt-realtime` — the most advanced production-ready voice model.

**Key features:**
- Low-latency speech-to-speech conversations
- Function calling during audio (tool use mid-conversation)
- Interruption detection
- Context management across turns
- Voice activity detection
- Multiple audio format support

**Use cases:** Customer support agents, personal assistants, education, interactive voice applications.

### PDF & Document Processing

Models can process PDF documents directly, including:
- Text extraction and analysis
- Table understanding
- Multi-page document reasoning
- Combined with file search for large document sets

---

## Chapter 9: Advanced Features

### Web Search Tool

Add real-time information to model responses with `web_search_preview`:

```python
response = client.responses.create(
    model="gpt-4.1",
    tools=[{"type": "web_search_preview"}],
    input="What were the key announcements at the latest AI conference?"
)
```

**Features:**
- Automatic citation generation with source URLs
- Search context size control (how much web content to include)
- Works with GPT-4o, GPT-4o mini, and other supported models
- Combinable with other tools (function calling, file search, etc.)

### File Search

Semantic and keyword search across your uploaded documents:

```python
response = client.responses.create(
    model="gpt-4.1",
    tools=[{
        "type": "file_search",
        "vector_store_ids": ["vs_abc123"]
    }],
    input="What does the contract say about termination clauses?"
)
```

**Features:**
- Hybrid semantic + keyword search
- Vector stores for document collections
- Configurable chunking strategies
- Attribute filtering with arrays
- Multiple vector store search in a single query
- Supports many file types (PDF, DOCX, TXT, etc.)

### Code Interpreter

Sandboxed Python execution environment:

**Capabilities:**
- Run Python code for data analysis
- Process uploaded files (CSV, Excel, etc.)
- Generate charts and visualizations
- Perform complex mathematical calculations
- File I/O within the sandbox

**Best for:** Data analysis, complex math, chart generation, file format conversion, statistical analysis.

### Computer Use (CUA)

The Computer-Using Agent (CUA) model enables automated UI interaction through the Responses API.

**How it works:**
1. **Perception**: Screenshots are added to the model's context
2. **Reasoning**: Chain-of-thought determines next actions
3. **Action**: Virtual mouse and keyboard execute tasks (click, type, scroll)

**Performance benchmarks:**
- 38.1% on OSWorld (full computer use)
- 58.1% on WebArena (web tasks)
- 87% on WebVoyager (web navigation)

**Important:** Available only through the Responses API, not Chat Completions. Safety measures prevent harmful tasks and block restricted websites.

### Predicted Outputs

Speed up API responses when most output tokens are known ahead of time:

```python
response = client.chat.completions.create(
    model="gpt-4.1",
    messages=[...],
    prediction={"type": "content", "content": predicted_text}
)
```

**How it works:** You provide a "prediction" of the expected output. The model can skip generating tokens that match the prediction, dramatically reducing latency.

**Performance:** Testing shows ~50% latency reduction (e.g., 889ms vs 1835ms).

**Best for:** Code editing (regenerating files with small changes), template-based generation, iterative refinement.

**Caveats:**
- Rejected prediction tokens are still billed as output tokens
- Gains are greatest with streaming enabled
- Works best when most of the output is predictable

### Batch API

Process large volumes at 50% cost reduction:

```python

# Create a batch of requests
batch = client.batches.create(
    input_file_id="file-abc123",
    endpoint="/v1/chat/completions",
    completion_window="24h"
)
```

**Features:**
- 50% cost reduction on all supported models
- 24-hour completion guarantee (often much faster)
- Supports Chat Completions and Embeddings endpoints
- Progress monitoring and cancellation

**Best for:** Large-scale data processing, embedding generation, content moderation at scale, non-time-sensitive tasks.

### Embeddings

Convert text to vector representations for search, classification, and clustering.

**Models:**
| Model | Dimensions | Price/MTok | Best For |
|-------|-----------|-----------|----------|
| `text-embedding-3-small` | 512-1536 | $0.02 | Fast, cost-efficient |
| `text-embedding-3-large` | 256-3072 | $0.13 | Highest quality |

**Key feature:** The `dimensions` parameter lets you control embedding size, trading quality for efficiency. Shorter embeddings use less storage and compute.

**Distance functions:** Cosine similarity (recommended), dot product, Euclidean distance.

### Moderation API

Free content safety checking for text and images:

```python
response = client.moderations.create(
    model="omni-moderation-latest",
    input="Content to check"
)
```

**Categories detected:** hate, harassment, self-harm, sexual content, violence, and more.

**omni-moderation-latest** supports both text and image moderation.

**Cost:** Free for all API users.

### Safety Best Practices

1. **Use the Moderation API** as a pre-filter for user inputs and post-filter for model outputs
2. **Red team your applications** — test with adversarial inputs
3. **Implement rate limiting** to prevent abuse
4. **Use system/developer messages** to set behavioral boundaries
5. **Don't rely solely on the model** for safety — use defense in depth
6. **Monitor and log** interactions for safety review
7. **Validate outputs** before taking real-world actions

---

## Chapter 10: Agentic Patterns & OpenAI Agents SDK

### OpenAI Agents SDK Overview

The OpenAI Agents SDK is an open-source framework (Python and TypeScript) for building multi-agent applications. It is deliberately **provider-agnostic** — supporting OpenAI natively, plus 100+ providers via LiteLLM.

**Installation:**
```bash
pip install openai-agents
```

### Core Primitives

#### Agent

The fundamental unit — a configuration object containing instructions, tools, handoffs, and model settings:

```python
from agents import Agent

agent = Agent(
    name="research_assistant",
    instructions="You are a helpful research assistant. Use web search to find current information.",
    tools=[web_search_tool],
    model="gpt-4.1"
)
```

#### Runner

Orchestrates agent execution through three methods:
- `Runner.run()` — async execution
- `Runner.run_sync()` — synchronous execution
- `Runner.run_streamed()` — real-time event streaming

The Runner implements a deterministic turn-based loop:
1. Retrieve session history
2. Merge with new user input
3. Invoke current agent's model
4. Process output (tool calls → execute and loop; handoff → switch agent and loop; final output → exit)
5. Persist to session
6. Return `RunResult`

#### Handoff

Transfer control between agents with full conversation history:

```python
triage_agent = Agent(
    name="triage",
    instructions="Route the user to the appropriate specialist.",
    handoffs=[billing_agent, technical_agent, general_agent]
)
```

**Two orchestration patterns:**
- **Manager Pattern** (`agent.as_tool()`): Manager invokes sub-agents synchronously, retains control
- **Handoff Pattern** (`handoffs` list): Complete control transfer to target agent

#### Guardrail

Validate inputs and outputs for safety and quality:

```python
from agents import input_guardrail, GuardrailFunctionOutput

@input_guardrail
async def check_input(ctx, agent, input):
    # Validate input
    if is_harmful(input):
        return GuardrailFunctionOutput(tripwire_triggered=True)
    return GuardrailFunctionOutput(tripwire_triggered=False)
```

**Guardrail types:**
- `@input_guardrail` — validate before processing
- `@output_guardrail` — validate model output
- `@tool_input_guardrail` — validate tool arguments
- `@tool_output_guardrail` — validate tool results

**Execution modes:** Parallel (default, low latency) or blocking (prevents wasted API calls).

### Tool System

Five tool categories:

| Category | Description | Example |
|----------|-------------|---------|
| **Function Tools** | Python functions with `@function_tool` | Local computations, API calls |
| **Hosted Tools** | OpenAI-managed services | Web search, file search, code interpreter |
| **Local Runtime Tools** | Custom interface implementations | Computer use, shell access |
| **MCP Tools** | Model Context Protocol servers | External service integration |
| **Agent-as-Tool** | Sub-agents via `agent.as_tool()` | Hierarchical agent composition |

### Context & Dependency Injection

Pass application context through execution without sending it to the LLM:

```python
from agents import RunContextWrapper

@function_tool
async def get_user_info(ctx: RunContextWrapper[MyAppContext]):
    user = ctx.context.current_user
    return f"User: {user.name}, Plan: {user.plan}"
```

Context is available to dynamic instructions, tool functions, and handoff callbacks — but never reaches the LLM.

### Session Management

Persist conversation history across turns:

| Implementation | Use Case |
|---------------|----------|
| `SQLiteSession` | Development, file-based storage |
| `RedisSession` | Production, distributed systems |
| `SQLAlchemySession` | Custom database backends |
| `EncryptedSession` | Wraps any session with encryption |

Alternative: Use OpenAI's server-managed conversations via `conversation_id` and `previous_response_id`.

### Tracing and Observability

Automatic hierarchical tracing captures:
- **Trace span**: Workflow-level context
- **Agent span**: Per-agent execution
- **Generation span**: LLM API calls
- **Function span**: Tool invocations
- **Guardrail span**: Validation operations

**Integrations:** OpenAI dashboard (default), Weights & Biases, Phoenix, Logfire, AgentOps, Braintrust, and custom exporters.

**Data sensitivity controls:**
- `trace_include_sensitive_data=False` — omit LLM inputs/outputs
- `trace_include_sensitive_audio_data=False` — omit audio data

### Model Provider Flexibility

The SDK supports multiple model backends:

| Implementation | Description |
|---------------|-------------|
| `OpenAIResponsesModel` | Native Responses API (recommended) |
| `OpenAIChatCompletionsModel` | Chat Completions compatibility |
| `LitellmModel` | 100+ providers (Anthropic, Gemini, DeepSeek, etc.) |

### Multi-Agent Architecture Patterns

**Sequential pipeline:** Agent A → handoff → Agent B → handoff → Agent C

**Triage/routing:** Triage agent classifies and routes to specialist agents

**Hierarchical:** Manager agent delegates subtasks to worker agents via `as_tool()`

**Collaborative:** Multiple agents share context and hand off based on expertise

### Deployment Patterns

- **Responses API as the agentic backbone** — built-in tool loops handle multi-step execution
- **Stateless agents** with session-managed state for horizontal scaling
- **Streaming** for real-time user feedback during long-running agent tasks
- **Guardrails** for production safety and quality gates
- **Tracing** for debugging and monitoring in production

### Voice Agents

The SDK supports real-time voice agents with:
- Interruption detection
- Context management
- Guardrail enforcement during audio
- Function calling during voice conversations

```python
from agents.voice import VoiceAgent

voice_agent = VoiceAgent(
    name="support",
    instructions="You are a customer support agent.",
    tools=[lookup_order, check_status],
    voice="nova"
)
```

---

## Quick Reference: Model Selection Decision Tree

```
Start Here
│
├── Need complex reasoning/math? ──→ o3 (quality) or o4-mini (cost)
│
├── Need long context (>200K)? ──→ GPT-4.1 family
│
├── Need agentic coding? ──→ GPT-5.3-Codex (Codex) or GPT-4.1 (API)
│
├── Need image generation? ──→ gpt-image-1.5 (fast) or gpt-image-1
│
├── Need real-time voice? ──→ gpt-realtime
│
├── Need embeddings? ──→ text-embedding-3-small (fast) or large (quality)
│
├── Budget-critical? ──→ GPT-4.1 nano ($0.10/$0.40)
│
└── General-purpose? ──→ GPT-4.1 (recommended default)
```

---

*This guide is based on official OpenAI documentation as of February 2026. Model capabilities, pricing, and API features are subject to change. Always consult the latest documentation at platform.openai.com for current information.*