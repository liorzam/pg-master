# Chapter 1: Introduction & Model Overview

Google Gemini is a family of multimodal large language models developed by Google DeepMind. Designed to understand and generate across text, images, audio, video, and code, the Gemini model family offers a range of options tailored to different performance, latency, and cost requirements. This chapter provides a comprehensive overview of the current model lineup, their capabilities, pricing, and guidance on choosing the right model for your use case.

## Current Gemini Model Lineup

The Gemini model family is organized into three active series, each representing a generation of capability improvements. Understanding which series and model to use is the first decision you need to make when building with Gemini.

### Gemini 3 Series (Preview)

The Gemini 3 series represents the latest generation of models, currently available in preview. These models introduce simplified thinking controls, improved conciseness, and enhanced multimodal capabilities.

- **Gemini 3.1 Pro** -- The most capable model in the 3 series, offering top-tier reasoning and generation quality. API ID: `gemini-3.1-pro-preview`.
- **Gemini 3 Pro** -- A highly capable reasoning model balancing quality and efficiency. API ID: `gemini-3-pro-preview`.
- **Gemini 3 Flash** -- Optimized for speed and cost efficiency while maintaining strong reasoning. API ID: `gemini-3-flash-preview`.
- **Gemini 3 Pro Image (Nano Banana Pro)** -- Specialized for professional-grade image generation with 4K output, advanced text rendering, and search grounding. API ID: `gemini-3-pro-image-preview`.

All Gemini 3 models share a **1 million token context window**, a **64K maximum output**, and a knowledge cutoff of **January 2025**. They introduce the new `thinkingLevel` parameter (replacing `thinkingBudget`) and default to more concise responses compared to previous generations.

### Gemini 2.5 Series (Generally Available)

The Gemini 2.5 series is the current generally available (GA) production-ready lineup. These models offer strong reasoning with configurable thinking budgets and are suitable for production deployments.

- **Gemini 2.5 Pro** -- The most advanced GA reasoning model for complex tasks. API ID: `gemini-2.5-pro`. Input pricing: **$1.25 per MTok** (up to 200K context) / **$2.50 per MTok** (over 200K context). Output pricing: **$10 per MTok** (up to 200K) / **$15 per MTok** (over 200K). Thinking budget range: **128 to 32,768 tokens**.
- **Gemini 2.5 Flash** -- The best price-performance reasoning model. API ID: `gemini-2.5-flash`. Input pricing: **$0.30 per MTok**. Output pricing: **$2.50 per MTok**. Thinking budget range: **0 to 24,576 tokens** (can be fully disabled).
- **Gemini 2.5 Flash-Lite** -- The most cost-efficient model for high-volume workloads. API ID: `gemini-2.5-flash-lite`. Input pricing: **$0.10 per MTok**. Output pricing: **$0.40 per MTok**. No thinking support.

### Gemini 2.0 (Deprecated)

- **Gemini 2.0 Flash** -- Previously the workhorse model, now deprecated in favor of the 2.5 series. Existing integrations should plan migration to Gemini 2.5 Flash or Flash-Lite.

## Model Comparison Table

| Model | API ID | Status | Context Window | Max Output | Thinking | Input Price (per MTok) | Output Price (per MTok) | Best For |
|-------|--------|--------|---------------|------------|----------|----------------------|------------------------|----------|
| 3.1 Pro | `gemini-3.1-pro-preview` | Preview | 1M | 64K | thinkingLevel | $2.00 | $12.00 | Most demanding reasoning tasks |
| 3 Pro | `gemini-3-pro-preview` | Preview | 1M | 64K | thinkingLevel | $2.00 | $12.00 | Complex reasoning and generation |
| 3 Flash | `gemini-3-flash-preview` | Preview | 1M | 64K | thinkingLevel | $0.50 | $3.00 | Fast, cost-effective reasoning |
| 3 Pro Image | `gemini-3-pro-image-preview` | Preview | 1M | 64K | Yes | -- | -- | Professional image generation |
| 2.5 Pro | `gemini-2.5-pro` | GA | 1M | 64K | 128-32768 | $1.25 / $2.50 | $10.00 / $15.00 | Complex coding, math, analysis |
| 2.5 Flash | `gemini-2.5-flash` | GA | 1M | 64K | 0-24576 | $0.30 | $2.50 | High-volume reasoning tasks |
| 2.5 Flash-Lite | `gemini-2.5-flash-lite` | GA | 1M | 64K | None | $0.10 | $0.40 | Classification, extraction, high-volume |
| 2.0 Flash | `gemini-2.0-flash` | Deprecated | 1M | 64K | None | -- | -- | Legacy workloads (migrate away) |

## Platform Availability

Gemini models are accessible through three primary platforms, each serving different audiences and use cases.

### Gemini Developer API

The Gemini Developer API is the most direct way to access Gemini models. It provides RESTful endpoints and official SDKs in Python, JavaScript/TypeScript, Go, and Java. This is the recommended starting point for individual developers, startups, and rapid prototyping.

### Google AI Studio

Google AI Studio is a web-based IDE for experimenting with Gemini models. It provides a visual interface for testing prompts, adjusting parameters, and comparing model outputs without writing code. AI Studio is excellent for prompt development, few-shot example curation, and quick experimentation before committing to code.

### Vertex AI

Vertex AI is Google Cloud's enterprise ML platform. It offers the same Gemini models but adds enterprise features including VPC security, data residency controls, IAM-based access management, model monitoring, batch prediction, and SLA-backed uptime guarantees. Use Vertex AI for production deployments in regulated industries or organizations with strict compliance requirements.

## Specialized Variants

Beyond the core text-and-reasoning models, Google offers several specialized Gemini variants designed for specific tasks.

| Variant | Purpose |
|---------|---------|
| **Flash Image / Nano Banana** | Fast image generation optimized for high-volume, low-latency workflows |
| **Nano Banana Pro** | Professional 4K image generation with text rendering and search grounding |
| **Live** | Real-time voice and video streaming over WebSocket connections |
| **TTS (Text-to-Speech)** | High-quality speech synthesis with multiple voice options |
| **Embeddings** | Dense vector representations for semantic search and retrieval |
| **Deep Research** | Extended multi-step research workflows with automatic source gathering |
| **Computer Use** | Browser automation through screenshots and UI action commands |
| **Robotics** | Physical world interaction and robotic control planning |

## Decision Framework for Choosing the Right Model

Selecting the right Gemini model depends on four key dimensions: **task complexity**, **latency requirements**, **cost sensitivity**, and **feature needs**.

### By Task Complexity

- **Simple tasks** (classification, extraction, formatting): Use **2.5 Flash-Lite** for maximum cost efficiency, or **3 Flash** with `thinkingLevel: minimal` if you need the latest capabilities.
- **Moderate tasks** (summarization, Q&A, content generation): Use **2.5 Flash** with dynamic thinking, or **3 Flash** with `thinkingLevel: medium`.
- **Complex tasks** (multi-step reasoning, code generation, mathematical proofs): Use **2.5 Pro** or **3 Pro** with high thinking budgets.
- **Most demanding tasks** (competition math, novel algorithm design, deep analysis): Use **2.5 Pro with Deep Think** (budget 32768) or **3.1 Pro** with `thinkingLevel: high`.

### By Latency Requirements

- **Real-time / sub-second**: Use the **Live API** variants for streaming, or **2.5 Flash-Lite** for fastest batch responses.
- **Interactive (1-5 seconds)**: Use **2.5 Flash** or **3 Flash** with moderate thinking.
- **Batch / async**: Use any model; consider **batch API** pricing discounts for non-urgent workloads.

### By Cost Sensitivity

- **Budget-constrained**: Start with **2.5 Flash-Lite** ($0.10/$0.40 per MTok) and only upgrade if quality is insufficient.
- **Balanced**: Use **2.5 Flash** ($0.30/$2.50 per MTok) for the best quality-per-dollar with reasoning.
- **Quality-first**: Use **2.5 Pro** or **3.1 Pro** when output quality justifies the cost.

## Free vs Paid vs Enterprise Tier Comparison

| Feature | Free Tier | Paid Tier | Enterprise (Vertex AI) |
|---------|-----------|-----------|----------------------|
| Rate limits | 15 RPM / 1,500 RPD | 2,000+ RPM | Custom / negotiated |
| Context caching | Limited | Full access | Full access + SLA |
| Batch API | Not available | 50% discount pricing | Full access |
| Grounding (Search) | 1,500 RPD free | $14-$35 per 1K queries | Volume pricing |
| Support | Community | Standard | Premium / dedicated |
| Data residency | No control | Limited | Full control |
| SLA | None | 99.9% | 99.95%+ |
| Compliance | Basic | SOC 2 | HIPAA, FedRAMP, etc. |

The free tier is suitable for development and testing. The paid tier unlocks production-level rate limits and features. Enterprise (Vertex AI) adds the security, compliance, and operational controls required by large organizations.

# Chapter 2: Universal Prompting Techniques

Effective prompting is both an art and an engineering discipline. Regardless of which Gemini model you use, certain prompting techniques consistently produce better results. This chapter covers the foundational techniques that apply across all Gemini models and task types, from writing clear instructions to managing context and tuning model parameters.

## Clear and Specific Instructions

The single most impactful thing you can do to improve model output is to write clear, specific instructions. Vague prompts produce vague results. There are four primary input types, and understanding which one you are providing helps you frame better prompts.

### Input Types

| Input Type | Description | Example |
|-----------|-------------|---------|
| **Question** | A direct query expecting an informational answer | "What causes ocean tides?" |
| **Task** | An instruction to perform a specific action | "Translate this paragraph into French." |
| **Entity** | A subject or object to analyze, describe, or transform | "Python decorators" (with context implying explanation) |
| **Completion** | A partial text to be continued or finished | "The three main benefits of cloud computing are: 1." |

### Before and After Examples

**Vague prompt (before):**
```
Tell me about machine learning.
```

**Clear, specific prompt (after):**
```
Explain the three main categories of machine learning (supervised, unsupervised,
and reinforcement learning) in 2-3 sentences each. For each category, include
one real-world application example. Target audience: software engineers with no
ML background.
```

**Vague task prompt (before):**
```
Write a summary.
```

**Clear task prompt (after):**
```
Summarize the following article in exactly 3 bullet points. Each bullet should
be one sentence and capture a distinct key finding. Use plain language suitable
for a non-technical executive audience.
```

The pattern is consistent: specify the **scope**, **format**, **length**, **audience**, and **constraints** of the desired output.

## Few-Shot Prompting

Few-shot prompting provides the model with 2 to 5 examples of the desired input-output pattern before presenting the actual task. This is one of the most reliable ways to steer model behavior without fine-tuning.

### Guidelines

- Use **2 to 5 examples** for most tasks. Fewer than 2 may not establish a clear pattern; more than 5 rarely improves results and wastes tokens.
- Maintain **consistent formatting** across all examples. If your first example uses bullet points, all examples should use bullet points.
- Use **delimiters** (XML tags, Markdown headers, or separators) to clearly distinguish between examples and between input/output pairs.
- **Avoid overfitting** by ensuring your examples are diverse enough to represent the range of expected inputs. If all examples share the same structure, the model may become rigid.

### Example with XML Delimiters

```
Classify the sentiment of customer reviews as Positive, Negative, or Neutral.

<example>
<input>The product arrived on time and works perfectly. Very satisfied!</input>
<output>Positive</output>
</example>

<example>
<input>Decent quality for the price, nothing special but it gets the job done.</input>
<output>Neutral</output>
</example>

<example>
<input>Broke after two days. Complete waste of money. Returning immediately.</input>
<output>Negative</output>
</example>

Now classify:
<input>I love the design but the battery life is disappointing. Mixed feelings overall.</input>
```

### Example with Markdown Delimiters

```
Convert the following natural language descriptions into SQL queries.

**Example 1:**
- Description: Get all users who signed up in 2024
- SQL: `SELECT * FROM users WHERE EXTRACT(YEAR FROM signup_date) = 2024;`

**Example 2:**
- Description: Count orders by product category
- SQL: `SELECT category, COUNT(*) FROM orders GROUP BY category;`

**Now convert:**
- Description: Find the top 5 customers by total spending
```

## Chain of Thought Prompting

Chain of thought (CoT) prompting instructs the model to reason through a problem step by step before producing a final answer. This technique dramatically improves performance on tasks involving logic, mathematics, multi-step reasoning, and complex analysis.

### Basic Chain of Thought

Add the phrase **"think step by step"** or **"explain your reasoning"** to encourage the model to show its work.

```
A store sells apples for $1.50 each and oranges for $2.00 each. If Maria buys
3 apples and 4 oranges, and she has a 10% discount coupon, how much does she pay?

Think step by step before giving the final answer.
```

### Combining CoT with Thinking Mode

For Gemini 2.5 and 3 models, chain of thought prompting works synergistically with the built-in **thinking mode**. The model's internal thinking process handles the step-by-step reasoning, while explicit CoT in the prompt provides additional structure and guidance.

```
Analyze this algorithm's time complexity. First, identify all loops and
recursive calls. Then, determine the work done at each level. Finally,
combine them using the Master Theorem if applicable. Show your reasoning
at each step.
```

### Explicit Planning

For particularly complex tasks, ask the model to create a plan before executing it.

```
I need to refactor this Python class to follow the Strategy pattern.

Before writing any code:
1. Identify the behaviors that vary
2. Define the strategy interface
3. List the concrete strategies needed
4. Describe how the context class will use them

Then implement the refactored code.
```

## System Instructions

System instructions set the overall behavior, persona, and constraints for the model across an entire conversation or session. They are processed before any user messages and establish the ground rules the model should follow.

### Recommended Structure

Organize system instructions using a **role, context, formatting, constraints** structure.

```xml
<system_instruction>
  <role>
    You are a senior Python developer with 15 years of experience specializing
    in backend systems and API design.
  </role>

  <context>
    You are assisting a team of junior developers who are building a REST API
    using FastAPI. The codebase follows PEP 8 style and uses type hints throughout.
  </context>

  <formatting>
    - Always include type hints in code examples
    - Use docstrings in Google format
    - Present code in fenced code blocks with language identifiers
    - When explaining concepts, use bullet points for clarity
  </formatting>

  <constraints>
    - Never suggest deprecated Python features (Python 3.10+ only)
    - Always consider security implications and mention them
    - If you are unsure about a library version, say so explicitly
    - Do not generate code longer than 50 lines without breaking it into functions
  </constraints>
</system_instruction>
```

### Key Principles

- System instructions should be **stable** across a conversation. Do not change them between turns unless you have a specific reason.
- Keep system instructions **concise but complete**. Every token in the system instruction is processed with every request.
- Use **XML tags** or clear section headers to organize complex system instructions.

## Response Format Control

You can steer the format of the model's response by providing explicit formatting instructions or by starting the response with a specific prefix.

### Using Prefixes

```
List the pros and cons of microservices architecture.

Format your response as:

## Pros
- ...

## Cons
- ...

## Verdict
...
```

### Requesting Specific Formats

```
Analyze this CSV data and present your findings as:
1. A markdown table summarizing key metrics
2. Three bullet points highlighting trends
3. A single-sentence recommendation
```

The model reliably follows format instructions when they are explicit and unambiguous.

## Breaking Down Complex Prompts

When a task involves multiple steps or aspects, breaking it into smaller, focused prompts often produces better results than a single monolithic prompt.

### Sequential Chaining

Pass the output of one prompt as input to the next.

```
Step 1: "Extract all product names and prices from this catalog text."
Step 2: "Given this list of products and prices, identify the top 5 by value."
Step 3: "Write a recommendation email featuring these top 5 products."
```

### Parallel Aggregation

Run multiple prompts independently, then combine the results.

```
Prompt A: "Summarize the technical specifications of this product."
Prompt B: "Summarize the customer reviews for this product."
Prompt C: "Summarize the competitive landscape for this product category."
Final:    "Given these three summaries, write a comprehensive product brief."
```

### Separating Instructions from Content

When the prompt contains both instructions and a large body of content, clearly separate them.

```
<instructions>
Extract all dates mentioned in the following document. Return them in a JSON
array with the format {"date": "YYYY-MM-DD", "context": "brief description"}.
</instructions>

<document>
[... long document content ...]
</document>
```

## Context Management

How you structure and position context within your prompts significantly affects response quality, especially for long-context tasks.

### Context First, Questions Last

Always place reference material, documents, and data **before** your question or instruction. This aligns with how transformer models process sequential input.

```
Here is the quarterly earnings report:
[... report content ...]

Based on the report above, what were the three largest expense categories?
```

### Anchoring Phrases

Use anchoring phrases to direct the model's attention to specific parts of the context.

```
Referring specifically to Section 3.2 of the document above, explain the
proposed changes to the authentication flow.
```

### Strict Grounding

When you need the model to answer strictly from provided context (no outside knowledge), say so explicitly.

```
Answer the following question using ONLY the information provided in the
document above. If the answer is not found in the document, respond with
"Information not found in the provided document."
```

## Iteration Strategies

Prompting is iterative. If your first attempt does not produce satisfactory results, try these strategies.

### Rephrase the Prompt

Different phrasings can activate different reasoning pathways in the model.

```
Original: "What are the risks of this approach?"
Rephrased: "As a security auditor, identify the top 5 vulnerabilities in this approach, ranked by severity."
```

### Use Analogous Tasks

If the model struggles with an abstract task, frame it as a more concrete, analogous one.

```
Original: "Evaluate the coherence of this argument."
Analogous: "If this argument were presented in a college debate, what would the opposing team's strongest counterpoints be?"
```

### Reorder Content

Sometimes changing the order of information in the prompt improves results. Move the most important context closer to the query.

### Adjust Parameters

If the output is too generic, lower the temperature. If it is too repetitive, raise it. See the Model Parameter Tuning section below.

## Model Parameter Tuning

Beyond prompt text, several API parameters influence model behavior.

### Temperature

- **Default for Gemini 3:** `1.0`. Google recommends keeping this default unless you have a specific reason to change it.
- **For deterministic tasks** (classification, extraction): Use `0.0`.
- **For creative tasks** (brainstorming, creative writing): Use `1.0` to `2.0`.

### Top-K

Limits token selection to the top K candidates. Lower values (10-20) increase focus; higher values (80-100) increase diversity.

### Top-P (Nucleus Sampling)

Sets a cumulative probability threshold for token selection. Values of `0.90` to `0.95` work well for most tasks.

### Max Output Tokens

Controls the maximum response length. Set this based on your expected output size to avoid truncation or unnecessary verbosity.

### Stop Sequences

Strings that cause generation to halt when encountered. Useful for controlling output boundaries in structured generation tasks.

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Classify this text as positive, negative, or neutral: 'Great product!'",
    config=types.GenerateContentConfig(
        temperature=0.0,
        top_k=1,
        top_p=1.0,
        max_output_tokens=10,
        stop_sequences=["\n"],
    ),
)
```

## Output Verbosity Control

Gemini 3 models are **more concise by default** compared to Gemini 2.5 models. If you need detailed, verbose output from a Gemini 3 model, you must request it explicitly.

```
Provide a detailed, comprehensive explanation of how garbage collection works
in Java. Include all major algorithms (Serial, Parallel, CMS, G1, ZGC),
their tradeoffs, and when to use each one. Be thorough -- I want a complete
reference, not a summary.
```

Conversely, if you want brief output from a Gemini 2.5 model, explicitly request conciseness:

```
In one sentence, explain what Kubernetes is.
```

## Things to Avoid

Several common pitfalls can degrade the quality and reliability of Gemini outputs.

- **Ungrounded factual claims**: Do not assume the model's factual statements are accurate for time-sensitive or critical information. Use Google Search grounding for current data, and always verify critical facts.
- **Unverified math**: The model can make arithmetic errors, especially in multi-step calculations. Use the code execution tool for any computation that requires precision.
- **High temperature for factual tasks**: Using `temperature > 1.0` for tasks requiring factual accuracy increases the risk of hallucinations. Reserve high temperatures for creative tasks only.
- **Too many few-shot examples**: Beyond 5-7 examples, additional examples rarely help and may cause the model to overfit to superficial patterns in your examples rather than understanding the underlying task.
- **Contradictory instructions**: Ensure your system instructions, few-shot examples, and user prompt do not contain conflicting directives. The model may follow one and ignore another unpredictably.
- **Assuming determinism**: Even at `temperature=0`, outputs may vary slightly across API calls due to infrastructure-level factors. Do not build pipelines that assume byte-identical output across calls.

# Chapter 3: Gemini 2.5 Pro Best Practices

Gemini 2.5 Pro is the most advanced generally available reasoning model in the Gemini family. It is purpose-built for tasks that require deep analysis, complex multi-step reasoning, and the ability to process massive amounts of context. This chapter covers its unique capabilities, configuration options, and the strategies that help you get the most out of it.

## Overview

| Property | Value |
|----------|-------|
| API ID | `gemini-2.5-pro` |
| Status | Generally Available (GA) |
| Context Window | 1 million tokens |
| Max Output | 64K tokens |
| Knowledge Cutoff | January 2025 |
| Thinking Support | Yes (128 to 32,768 token budget) |

Gemini 2.5 Pro excels at tasks where reasoning depth matters more than speed. Its thinking mode allows it to allocate substantial internal computation to work through complex problems before producing a final answer.

## Thinking Configuration

The thinking budget controls how many tokens the model can use for internal reasoning before generating the visible response. This is the primary lever for controlling the quality-latency-cost tradeoff.

### thinkingBudget Parameter

- **Minimum:** 128 tokens
- **Maximum:** 32,768 tokens
- **Dynamic (-1):** Let the model decide how much thinking is needed (recommended default)
- **Cannot be fully disabled:** Unlike Gemini 2.5 Flash, you cannot set the thinking budget to 0 for 2.5 Pro. The minimum is always 128.

### Python Code Example

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents="Prove that the square root of 2 is irrational.",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(
            thinking_budget=8192,
            include_thoughts=True,
        ),
    ),
)

# Access the thinking process
for part in response.candidates[0].content.parts:
    if part.thought:
        print(f"[THINKING]: {part.text}")
    else:
        print(f"[RESPONSE]: {part.text}")
```

### Setting `include_thoughts=True`

When you enable `include_thoughts`, the model returns its internal reasoning steps alongside the final response. This is invaluable for debugging, quality assurance, and understanding how the model arrived at its answer. The thinking tokens are included in the response parts with a `thought=True` flag.

## Deep Think Mode

For the most challenging problems -- competition-level mathematics, novel algorithm design, complex logical proofs -- use the maximum thinking budget of **32,768 tokens**. This is referred to as **Deep Think mode**.

```python
response = client.models.generate_content(
    model="gemini-2.5-pro",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(
            thinking_budget=32768,
            include_thoughts=True,
        ),
    ),
    contents="""Solve this competition math problem:

    Find all functions f: R -> R such that for all real numbers x and y:
    f(x + f(y)) = f(x) + y
    """,
)
```

Deep Think mode significantly increases latency and cost but can solve problems that lower budgets cannot. Use it selectively for your hardest problems.

## Pricing

| Tier | Input (up to 200K) | Input (over 200K) | Output (up to 200K) | Output (over 200K) |
|------|--------------------|--------------------|---------------------|--------------------|
| Free | Free (rate-limited) | Free (rate-limited) | Free (rate-limited) | Free (rate-limited) |
| Paid | $1.25 / MTok | $2.50 / MTok | $10.00 / MTok | $15.00 / MTok |
| Batch | $0.625 / MTok | $1.25 / MTok | $5.00 / MTok | $7.50 / MTok |

**Important:** Thinking tokens are billed as output tokens. A response with 4,000 thinking tokens and 2,000 output tokens is billed for 6,000 output tokens. Monitor `thoughtsTokenCount` in usage metadata to track thinking costs separately.

## Best Use Cases

Gemini 2.5 Pro delivers the strongest results for:

- **Complex coding tasks**: Multi-file refactoring, architecture design, debugging intricate issues, generating production-quality code with comprehensive error handling.
- **Advanced mathematics**: Proofs, optimization problems, competition math, symbolic computation.
- **Data analysis**: Analyzing large datasets, identifying patterns, generating statistical insights.
- **Deep research**: Synthesizing information from multiple long documents, legal and regulatory analysis.
- **Legal and medical reasoning**: Interpreting complex legal language, analyzing medical literature (with appropriate disclaimers).
- **Multi-step planning**: Project planning, strategic analysis, decision frameworks.

## Prompting Tips for 2.5 Pro

### Leverage the Large Context Window

Gemini 2.5 Pro's 1 million token context window means you can include entire codebases, full legal documents, or comprehensive datasets directly in the prompt. Do not pre-summarize or truncate unless absolutely necessary.

```python

# Include a full codebase for analysis
codebase_contents = load_all_source_files("./src/")

response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents=[
        f"Here is our complete codebase:\n\n{codebase_contents}",
        "Identify all potential SQL injection vulnerabilities and suggest fixes.",
    ],
)
```

### Use Dynamic Thinking as Default

Set `thinking_budget=-1` to let the model allocate thinking resources dynamically based on problem complexity. This avoids under-allocating for hard problems or wasting tokens on easy ones.

### Include Thought Summaries for Debugging

Enable `include_thoughts=True` during development and testing to understand the model's reasoning. This helps you identify whether errors come from flawed reasoning or from correct reasoning applied to misunderstood context.

### Place Queries at the End

When working with long contexts, always place your question or instruction after the context material. This is especially important for 2.5 Pro given the large contexts it typically processes.

## When NOT to Use 2.5 Pro

Despite its capabilities, Gemini 2.5 Pro is not always the right choice:

- **Simple classification or extraction**: Use 2.5 Flash-Lite for straightforward tasks that do not require reasoning. The cost difference is 12.5x on input and 25x on output.
- **High-volume, low-latency applications**: If you need sub-second responses at scale, use 2.5 Flash or Flash-Lite. Pro's thinking overhead adds latency.
- **Budget-constrained projects**: At $10-$15 per MTok of output, Pro is expensive for high-volume use cases. Run cost projections before committing.
- **Batch tasks that do not require deep reasoning**: Summarization, translation, and reformatting tasks rarely benefit from Pro's additional reasoning capacity.

# Chapter 4: Gemini 2.5 Flash Best Practices

Gemini 2.5 Flash offers the best price-performance ratio in the Gemini family for reasoning tasks. It delivers strong reasoning capabilities at a fraction of the cost of Gemini 2.5 Pro, making it the go-to model for production workloads that need both quality and efficiency.

## Overview

| Property | Value |
|----------|-------|
| API ID | `gemini-2.5-flash` |
| Status | Generally Available (GA) |
| Context Window | 1 million tokens |
| Max Output | 64K tokens |
| Knowledge Cutoff | January 2025 |
| Thinking Support | Yes (0 to 24,576 token budget, can be fully disabled) |

The defining feature of Flash compared to Pro is the ability to **fully disable thinking** by setting the budget to 0. This gives you a smooth spectrum from zero-overhead inference to substantial reasoning, all within the same model.

## Thinking Configuration

### thinkingBudget Parameter

- **Minimum:** 0 tokens (fully disabled)
- **Maximum:** 24,576 tokens
- **Dynamic (-1):** Let the model decide (recommended default)
- **Key difference from Pro:** Flash CAN fully disable thinking (set budget to 0), while Pro cannot go below 128.

### Thinking Enabled

```python
from google import genai
from google.genai import types

client = genai.Client()

# Moderate thinking for a reasoning task
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Compare the time complexity of quicksort and mergesort. "
             "Which is better for nearly-sorted data and why?",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(
            thinking_budget=4096,
        ),
    ),
)

print(response.text)
```

### Thinking Disabled

```python

# Zero thinking for a simple classification task
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Classify this email subject as spam or not spam: "
             "'You've won a free iPhone! Click here now!'",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(
            thinking_budget=0,
        ),
    ),
)

print(response.text)
```

Disabling thinking is ideal for tasks that do not require reasoning: classification, extraction, reformatting, simple Q&A from provided context.

## Pricing

| Input Type | Price per MTok |
|-----------|---------------|
| Text input | $0.30 |
| Image input | $0.30 |
| Video input | $0.30 |
| Audio input | $1.00 |
| Text output | $2.50 |
| Thinking output | $2.50 |

Batch API pricing offers an additional **50% discount** on all token types.

## Flash-Lite Variant

Gemini 2.5 Flash-Lite is the stripped-down sibling designed for maximum throughput at minimum cost.

| Property | Value |
|----------|-------|
| API ID | `gemini-2.5-flash-lite` |
| Input Price | $0.10 / MTok |
| Output Price | $0.40 / MTok |
| Thinking | Not supported |
| Context Window | 1M tokens |
| Max Output | 64K tokens |

Flash-Lite is purpose-built for high-volume tasks where reasoning is unnecessary: classification, entity extraction, text reformatting, and simple template-based generation.

## Comparison: Flash vs Flash-Lite vs 2.0 Flash

| Feature | 2.5 Flash | 2.5 Flash-Lite | 2.0 Flash |
|---------|-----------|----------------|-----------|
| Status | GA | GA | Deprecated |
| Input Price | $0.30/MTok | $0.10/MTok | -- |
| Output Price | $2.50/MTok | $0.40/MTok | -- |
| Thinking | 0-24576 | None | None |
| Context Window | 1M | 1M | 1M |
| Max Output | 64K | 64K | 64K |
| Best For | Reasoning at scale | High-volume simple tasks | Legacy (migrate) |
| Recommended Use | Default production model | Classification, extraction | Do not use for new work |

**Migration guidance:** If you are using Gemini 2.0 Flash, migrate to **2.5 Flash with thinking disabled** for equivalent behavior with better quality, or to **2.5 Flash-Lite** for equivalent cost with improved capabilities.

## Best Use Cases

- **High-volume reasoning**: Processing thousands of documents that each require moderate analysis.
- **Cost-sensitive production**: Applications where 2.5 Pro's quality is desirable but its cost is prohibitive.
- **Adjustable thinking**: Workloads with mixed complexity where you can route simple tasks to zero-thinking and complex tasks to higher budgets.
- **Real-time applications**: Interactive applications where latency matters but reasoning quality cannot be fully sacrificed.

## Prompting Tips for 2.5 Flash

### Adjust Thinking Budget by Task Complexity

Do not use a fixed thinking budget for all tasks. Classify your incoming requests and assign budgets accordingly.

```python
def get_thinking_budget(task_type: str) -> int:
    budgets = {
        "classification": 0,
        "extraction": 0,
        "summarization": 2048,
        "analysis": 4096,
        "coding": 8192,
        "complex_reasoning": 16384,
    }
    return budgets.get(task_type, -1)  # Default to dynamic
```

### Use Dynamic Thinking as Default

When you cannot predict task complexity, use `thinking_budget=-1` (dynamic). The model will allocate thinking tokens proportional to the problem's difficulty.

### Disable Thinking for Classification

For binary or multi-class classification tasks, setting `thinking_budget=0` eliminates unnecessary latency and cost without affecting accuracy.

### Use Batch API for Non-Urgent Workloads

The batch API offers a 50% discount on all token prices. For tasks that do not require real-time responses (nightly report generation, bulk data processing), batch is the most cost-effective option.

```python

# Batch API example
batch_job = client.batches.create(
    model="gemini-2.5-flash",
    requests=[
        types.BatchRequest(
            contents="Summarize this article: ...",
            config=types.GenerateContentConfig(
                thinking_config=types.ThinkingConfig(thinking_budget=2048),
            ),
        )
        for article in articles
    ],
)
```

# Chapter 5: Gemini 3 Series Best Practices

The Gemini 3 series represents the next generation of Gemini models, introducing simplified configuration, improved conciseness, and new multimodal capabilities. While currently in preview, these models offer significant advances that merit understanding and early adoption planning.

## Models Overview

| Model | API ID | Input Price | Output Price |
|-------|--------|------------|-------------|
| 3.1 Pro | `gemini-3.1-pro-preview` | $2.00 / MTok | $12.00 / MTok |
| 3 Pro | `gemini-3-pro-preview` | $2.00 / MTok | $12.00 / MTok |
| 3 Flash | `gemini-3-flash-preview` | $0.50 / MTok | $3.00 / MTok |
| 3 Pro Image | `gemini-3-pro-image-preview` | -- | -- |

All models share a **1 million token input** context window, **64K maximum output**, and a **January 2025 knowledge cutoff**.

## The New thinkingLevel Parameter

Gemini 3 replaces the numerical `thinkingBudget` from Gemini 2.5 with a simpler, categorical `thinkingLevel` parameter. This makes thinking configuration more intuitive and less error-prone.

| Level | Availability | Behavior | Best For |
|-------|-------------|----------|----------|
| `minimal` | Flash only | Minimal internal reasoning; fastest responses | Simple classification, extraction, formatting |
| `low` | All models | Light reasoning; quick analysis | Simple Q&A, basic summarization |
| `medium` | All models | Moderate reasoning; balanced quality and speed | General analysis, comparison, standard coding |
| `high` (default) | All models | Full reasoning; deepest analysis | Complex reasoning, proofs, multi-step problems |

### Python Code Example

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-3-flash-preview",
    contents="Explain the CAP theorem and its practical implications for "
             "distributed database design.",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(
            thinking_level="medium",
        ),
    ),
)

print(response.text)
```

## Migration from Gemini 2.5 to Gemini 3

### Key Differences

1. **Simplified prompts + thinkingLevel**: Replace verbose reasoning instructions with the appropriate `thinkingLevel`. Gemini 3 models are better at determining how to reason without explicit CoT prompting.

2. **Keep temperature at 1.0**: Gemini 3 models are calibrated for `temperature=1.0`. Changing this value is explicitly discouraged by Google unless you have a specific, tested reason.

3. **More concise answers by default**: Gemini 3 models produce shorter, more direct responses. If you need verbose output, you must explicitly request it in your prompt (e.g., "Provide a detailed, comprehensive explanation...").

4. **thinkingLevel replaces thinkingBudget**: Map your existing budgets to levels:
   - Budget 0-512 maps to `minimal` or `low`
   - Budget 512-4096 maps to `medium`
   - Budget 4096+ maps to `high`

## Media Resolution Control

Gemini 3 introduces explicit media resolution controls that let you optimize the tradeoff between processing fidelity and token consumption for different content types.

| Content Type | Recommended Setting | Tokens per Unit |
|-------------|-------------------|-----------------|
| Images | `media_resolution_high` | Up to 1,120 tokens per image |
| PDFs | `media_resolution_medium` | 560 tokens per page |
| Video (general) | `media_resolution_low` | 70 tokens per frame |
| Video (text-heavy) | `media_resolution_high` | 280 tokens per frame |

```python
response = client.models.generate_content(
    model="gemini-3-pro-preview",
    contents=[
        types.Part.from_uri(file_uri=pdf_file.uri, mime_type="application/pdf"),
        "Extract all tables from this document.",
    ],
    config=types.GenerateContentConfig(
        media_resolution="media_resolution_medium",
    ),
)
```

## Structured Output with Built-in Tools

Gemini 3 supports combining structured output schemas with built-in tools like Google Search and URL Context. This enables grounded, structured responses in a single call.

### Pydantic Example with google_search and url_context

```python
from pydantic import BaseModel
from typing import List

class ResearchResult(BaseModel):
    topic: str
    summary: str
    key_findings: List[str]
    sources: List[str]

response = client.models.generate_content(
    model="gemini-3-pro-preview",
    contents="Research the current state of quantum computing hardware.",
    config=types.GenerateContentConfig(
        tools=[
            types.Tool(google_search=types.GoogleSearch()),
            types.Tool(url_context=types.UrlContext()),
        ],
        response_mime_type="application/json",
        response_schema=ResearchResult,
    ),
)

result = ResearchResult.model_validate_json(response.text)
print(f"Topic: {result.topic}")
for finding in result.key_findings:
    print(f"  - {finding}")
```

## Code Execution with Images (Gemini 3 Flash)

Gemini 3 Flash extends code execution to include image manipulation, enabling visual investigation workflows where the model can annotate images, overlay data, and generate visualizations from visual inputs.

```python
response = client.models.generate_content(
    model="gemini-3-flash-preview",
    contents=[
        types.Part.from_uri(file_uri=chart_image.uri, mime_type="image/png"),
        "Analyze this chart image. Extract the data points, recreate the chart "
        "using matplotlib with better styling, and add a trend line.",
    ],
    config=types.GenerateContentConfig(
        tools=[types.Tool(code_execution=types.ToolCodeExecution())],
    ),
)
```

## Multimodal Function Responses with FunctionResponseBlob

Gemini 3 supports returning binary data (images, audio, files) from function calls using `FunctionResponseBlob`. This enables rich multimodal function calling pipelines.

```python

# Return an image from a function call
function_response = types.FunctionResponse(
    name="generate_chart",
    response={
        "description": "Monthly revenue chart",
    },
    response_blobs=[
        types.FunctionResponseBlob(
            data=chart_image_bytes,
            mime_type="image/png",
        )
    ],
)
```

## Computer Use

Computer Use is natively supported in both Gemini 3 Pro and Gemini 3 Flash. The model can observe screenshots, reason about the UI state, and issue structured action commands (click, type, scroll, navigate) to automate browser-based tasks.

```python
response = client.models.generate_content(
    model="gemini-3-pro-preview",
    contents=[
        types.Part.from_bytes(data=screenshot_bytes, mime_type="image/png"),
        "Navigate to the settings page and enable two-factor authentication.",
    ],
    config=types.GenerateContentConfig(
        tools=[types.Tool(computer_use=types.ComputerUse(
            environment=types.Environment(
                display_width=1920,
                display_height=1080,
            ),
        ))],
    ),
)
```

## Limitations

Be aware of current Gemini 3 limitations:

- **Cannot combine built-in tools with custom tools**: You must use either built-in tools (Google Search, URL Context, Code Execution) or custom function declarations in a single request, not both.
- **No image segmentation**: Unlike Gemini 2.5, Gemini 3 does not currently support image segmentation with pixel-level masks.
- **No Google Maps grounding**: Maps grounding is not yet available for Gemini 3 models (GA on 2.5 only).
- **Preview status**: All Gemini 3 models are in preview. APIs and behaviors may change before GA release.

# Chapter 6: Thinking Mode Deep Dive

Thinking mode is one of the most distinctive features of modern Gemini models. It allows the model to perform extended internal reasoning before generating a visible response, dramatically improving performance on complex tasks. This chapter provides a comprehensive guide to understanding, configuring, and optimizing thinking mode across all Gemini model families.

## How Thinking Works

When thinking mode is active, the model generates an internal chain of reasoning that is not directly visible to the user (unless you request thought summaries). This internal reasoning acts as a scratchpad where the model can:

- Break complex problems into sub-problems
- Explore multiple solution approaches
- Check its own work for errors
- Organize information before synthesizing a final answer

The thinking tokens are generated before the final response begins. The model "thinks first, then speaks." This is fundamentally different from chain-of-thought prompting, which generates reasoning tokens as part of the visible output.

## thinkingLevel (Gemini 3)

Gemini 3 uses the `thinkingLevel` parameter, a categorical control that maps to internal token budget ranges.

| Level | Behavior | Best For | Available On |
|-------|----------|----------|-------------|
| `minimal` | Almost no internal reasoning. Fastest possible response. | Classification, extraction, reformatting, simple lookups | Flash only |
| `low` | Light reasoning. Quick pattern matching and simple analysis. | Basic Q&A, short summaries, template-based generation | All Gemini 3 models |
| `medium` | Moderate reasoning. Can handle multi-step analysis and comparisons. | Comparison tasks, standard coding, structured summarization | All Gemini 3 models |
| `high` (default) | Full reasoning. Deep analysis, multi-step proofs, complex coding. | Competition math, complex algorithms, legal/medical analysis, research synthesis | All Gemini 3 models |

### When to Use Each Level

**Minimal (Flash only):**
```python

# Fast classification -- no reasoning needed
response = client.models.generate_content(
    model="gemini-3-flash-preview",
    contents="Is this sentence grammatically correct? 'She don't like apples.'",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(thinking_level="minimal"),
    ),
)
```

**Medium:**
```python

# Comparing two approaches -- moderate reasoning
response = client.models.generate_content(
    model="gemini-3-pro-preview",
    contents="Compare REST and GraphQL for a mobile application backend. "
             "Consider performance, caching, and developer experience.",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(thinking_level="medium"),
    ),
)
```

**High:**
```python

# Complex mathematical proof -- full reasoning
response = client.models.generate_content(
    model="gemini-3.1-pro-preview",
    contents="Prove that every continuous function on a closed interval "
             "is uniformly continuous.",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(thinking_level="high"),
    ),
)
```

## thinkingBudget (Gemini 2.5)

Gemini 2.5 uses the numerical `thinkingBudget` parameter, which specifies the exact number of tokens available for internal reasoning.

| Model | Minimum Budget | Maximum Budget | Can Disable? |
|-------|---------------|----------------|-------------|
| Gemini 2.5 Pro | 128 | 32,768 | No (minimum 128) |
| Gemini 2.5 Flash | 0 | 24,576 | Yes (set to 0) |

### Budget Recommendations by Task

| Task Type | Recommended Budget | Rationale |
|-----------|-------------------|-----------|
| Classification / extraction | 0 (Flash) or 128 (Pro) | No reasoning needed |
| Simple Q&A | 512-1024 | Light fact retrieval |
| Summarization | 1024-2048 | Moderate synthesis |
| Code generation | 4096-8192 | Multi-step logic |
| Complex analysis | 8192-16384 | Deep reasoning |
| Competition math / proofs | 24576-32768 | Maximum reasoning |

## Dynamic Thinking

Setting `thinking_budget=-1` (Gemini 2.5) or omitting `thinking_level` (Gemini 3, which defaults to `high`) enables **dynamic thinking**. This is the recommended default for most applications.

With dynamic thinking, the model assesses the complexity of each prompt and allocates thinking tokens proportionally. Simple questions get minimal thinking; complex problems get extensive reasoning. This eliminates the need to predict task complexity in advance.

```python

# Dynamic thinking -- recommended default
config = types.GenerateContentConfig(
    thinking_config=types.ThinkingConfig(
        thinking_budget=-1,  # Gemini 2.5
    ),
)
```

## Thought Summaries

Thought summaries let you inspect the model's internal reasoning process, which is valuable for debugging, quality assurance, and building transparent AI systems.

### Enabling Thought Summaries

```python
config = types.GenerateContentConfig(
    thinking_config=types.ThinkingConfig(
        thinking_budget=8192,
        include_thoughts=True,
    ),
)

response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents="Why is the sky blue?",
    config=config,
)

for part in response.candidates[0].content.parts:
    if part.thought:
        print(f"[THOUGHT]: {part.text}")
    else:
        print(f"[ANSWER]: {part.text}")
```

### Streaming vs Non-Streaming Behavior

- **Non-streaming**: Thought summaries appear as separate parts in the response, before the final answer parts.
- **Streaming**: Thought summary chunks arrive first in the stream, followed by answer chunks. Each chunk is tagged with `thought=True` or `thought=False`.

```python

# Streaming with thoughts
for chunk in client.models.generate_content_stream(
    model="gemini-2.5-pro",
    contents="Explain P vs NP.",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(
            thinking_budget=4096,
            include_thoughts=True,
        ),
    ),
):
    for part in chunk.candidates[0].content.parts:
        prefix = "[THOUGHT]" if part.thought else "[ANSWER]"
        print(f"{prefix}: {part.text}", end="")
```

## Thought Signatures

Thought signatures are encrypted representations of the model's reasoning context. They serve critical roles in maintaining reasoning continuity across multi-turn conversations and function calling workflows.

### What They Are

When Gemini models produce thinking tokens, they generate a cryptographic signature of the reasoning process. This signature is embedded in the response and must be preserved in conversation history for subsequent turns.

### Why They Matter

- **Required for function calling in Gemini 3**: Thought signatures carry the reasoning context that the model needs to correctly interpret function results and continue the conversation coherently.
- **SDK auto-handles**: If you use the official Python, JavaScript, or Go SDKs in their standard workflows, thought signatures are managed automatically. You do not need to extract, store, or inject them manually.
- **Do not tamper with or strip them**: Altering thought signatures causes 400 validation errors from the API.

### Dummy Signatures for Imported History

If you are importing conversation history from an external source (e.g., migrating from another model or loading from a database), the history will not contain valid thought signatures. In this case, you can provide a dummy signature to satisfy the API requirement.

```python

# When importing external conversation history
imported_history = [
    types.Content(role="user", parts=[types.Part.from_text("Hello")]),
    types.Content(role="model", parts=[
        types.Part.from_text("Hi! How can I help you?")
    ]),
]

# The SDK handles signature requirements when you use the chat interface
chat = client.chats.create(
    model="gemini-3-pro-preview",
    history=imported_history,
    config=types.GenerateContentConfig(tools=[my_tools]),
)
```

## Pricing: Thinking Tokens

Thinking tokens are **billed as output tokens** at the standard output rate for each model. This means:

| Model | Thinking Token Price |
|-------|---------------------|
| Gemini 2.5 Pro | $10.00 / MTok (same as output) |
| Gemini 2.5 Flash | $2.50 / MTok (same as output) |
| Gemini 3.1 Pro | $12.00 / MTok (same as output) |
| Gemini 3 Pro | $12.00 / MTok (same as output) |
| Gemini 3 Flash | $3.00 / MTok (same as output) |

To monitor thinking costs, check `thoughtsTokenCount` in the response usage metadata:

```python
usage = response.usage_metadata
print(f"Input tokens: {usage.prompt_token_count}")
print(f"Output tokens: {usage.candidates_token_count}")
print(f"Thinking tokens: {usage.thoughts_token_count}")
print(f"Total tokens: {usage.total_token_count}")
```

## When to Use vs Skip Thinking

### Skip Thinking (budget 0 or minimal level)

- **Classification tasks**: Spam detection, sentiment analysis, category assignment
- **Entity extraction**: Pulling names, dates, amounts from text
- **Simple reformatting**: JSON to CSV conversion, text templating
- **Direct lookup**: Retrieving specific facts from provided context

### Medium Thinking (budget 2048-8192 or medium level)

- **Comparison tasks**: Analyzing tradeoffs between options
- **Summarization**: Condensing long documents into key points
- **Standard code generation**: Writing functions, classes, or scripts
- **Structured analysis**: SWOT analysis, pros/cons evaluation

### Maximum Thinking (budget 16384-32768 or high level)

- **Competition math**: Olympiad problems, advanced proofs
- **Complex coding**: Algorithm design, system architecture, multi-file refactoring
- **Formal proofs**: Mathematical, logical, or legal arguments
- **Deep analysis**: Research synthesis, comprehensive policy evaluation

## Deep Think Mode (Gemini 2.5 Pro)

Deep Think mode refers to using Gemini 2.5 Pro with the maximum thinking budget of **32,768 tokens**. This configuration is reserved for the hardest problems where standard reasoning is insufficient.

Use Deep Think when:
- Standard thinking budgets produce incorrect or incomplete answers
- The problem is known to be extremely difficult (competition-level)
- Accuracy is more important than latency or cost
- You need the model to explore multiple solution paths

```python

# Deep Think for a hard optimization problem
response = client.models.generate_content(
    model="gemini-2.5-pro",
    config=types.GenerateContentConfig(
        thinking_config=types.ThinkingConfig(
            thinking_budget=32768,
            include_thoughts=True,
        ),
    ),
    contents="""Design an optimal algorithm for the Traveling Salesman Problem
    with 50 cities. Provide both an exact solution approach and a practical
    heuristic, analyze their time and space complexity, and prove the
    approximation ratio of the heuristic.""",
)
```

# Chapter 7: Function Calling & Tool Use

Function calling is one of the most powerful capabilities of Gemini models, enabling them to interact with external systems, APIs, and real-time data sources. Rather than being limited to generating text from their training data, models equipped with function calling can bridge the gap between language understanding and real-world action. This chapter covers the complete function calling ecosystem in Gemini, from the foundational four-step flow through advanced patterns like parallel and compositional calling, built-in tools, structured outputs, and MCP integration.

## The 4-Step Function Calling Flow

Function calling in Gemini follows a well-defined four-step lifecycle. Understanding this flow is essential because the model never executes functions directly -- your application is always in control of what actually runs.

### Step 1: Define Function Declarations

The first step is to describe the functions you want the model to be aware of. Each declaration includes a function name, a human-readable description, and a parameter schema following the OpenAPI specification format.

```json
{
  "name": "get_weather",
  "description": "Returns the current weather conditions for a given location, including temperature, humidity, and a short description of conditions.",
  "parameters": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "The city and state, e.g. 'San Francisco, CA'"
      },
      "unit": {
        "type": "string",
        "enum": ["celsius", "fahrenheit"],
        "description": "The temperature unit to use. Defaults to fahrenheit."
      }
    },
    "required": ["location"]
  }
}
```

In the Python SDK, you can define declarations using dictionaries or leverage automatic conversion from Python functions (covered later in the Automatic Function Calling section).

```python
from google.genai import types

get_weather_func = types.FunctionDeclaration(
    name="get_weather",
    description="Returns the current weather conditions for a given location.",
    parameters=types.Schema(
        type=types.Type.OBJECT,
        properties={
            "location": types.Schema(
                type=types.Type.STRING,
                description="The city and state, e.g. 'San Francisco, CA'"
            ),
            "unit": types.Schema(
                type=types.Type.STRING,
                enum=["celsius", "fahrenheit"],
                description="The temperature unit to use."
            ),
        },
        required=["location"],
    ),
)

weather_tool = types.Tool(function_declarations=[get_weather_func])
```

Key points for writing good declarations:

- **Function names** should be descriptive and self-explanatory (e.g., `search_products` not `sp`).
- **Descriptions** should clarify what the function does, what it returns, and any constraints.
- **Parameter descriptions** should include examples, valid ranges, or format expectations.
- **Required fields** should be explicitly marked to prevent the model from omitting critical arguments.

### Step 2: Call the LLM with Declarations

Send the user's prompt alongside the function declarations. The model analyzes the prompt, determines whether a function call is needed, and responds with structured JSON indicating which function to call and with what arguments.

```python
from google import genai

client = genai.Client()

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What's the weather like in Tokyo right now?",
    config=types.GenerateContentConfig(
        tools=[weather_tool],
    ),
)

# The response contains a function call, not text
function_call = response.candidates[0].content.parts[0].function_call
print(function_call.name)  # "get_weather"
print(function_call.args)  # {"location": "Tokyo, Japan", "unit": "celsius"}
```

The model does not execute anything at this stage. It simply signals its intent by returning a structured function call object. Your application is responsible for parsing this and deciding what to do next.

### Step 3: Execute the Function Code

Your application extracts the function name and arguments from the model's response, then executes the corresponding function in your own code. This is the critical separation of concerns -- the model reasons about which function to call and how, but your application retains full control over execution.

```python
import json

def get_weather(location: str, unit: str = "fahrenheit") -> dict:
    """Call your actual weather API here."""
    # Example: call an external weather service
    # response = weather_api.get(location=location, unit=unit)
    return {
        "location": location,
        "temperature": 22,
        "unit": "celsius",
        "conditions": "Partly cloudy",
        "humidity": 65,
    }

# Extract and execute
func_name = function_call.name
func_args = function_call.args

if func_name == "get_weather":
    result = get_weather(**func_args)
else:
    result = {"error": f"Unknown function: {func_name}"}
```

### Step 4: Generate the Final Response

Send the function execution result back to the model so it can generate a natural language response for the user. The model synthesizes the raw data into a conversational answer.

```python
from google.genai import types

# Build the function response
function_response = types.FunctionResponse(
    name="get_weather",
    response=result,
)

# Send the full conversation back to the model
final_response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Content(
            role="user",
            parts=[types.Part.from_text("What's the weather like in Tokyo right now?")],
        ),
        types.Content(
            role="model",
            parts=[types.Part.from_function_call(function_call)],
        ),
        types.Content(
            role="user",
            parts=[types.Part.from_function_response(function_response)],
        ),
    ],
    config=types.GenerateContentConfig(
        tools=[weather_tool],
    ),
)

print(final_response.text)

# "The weather in Tokyo is currently 22 degrees Celsius and partly cloudy

#  with 65% humidity."
```

This four-step cycle can repeat multiple times in a single conversation as the model calls different functions or chains them together.

## Calling Modes

Gemini provides four function calling modes that control when and how the model invokes functions. You set the mode via the `tool_config` parameter.

| Mode | Behavior |
|------|----------|
| **AUTO** (default) | The model decides whether to respond with text or a function call based on the user's prompt and available declarations. This is the most flexible mode. |
| **ANY** | The model is forced to always predict a function call. You can optionally restrict which functions are allowed via `allowed_function_names`. |
| **NONE** | Function calling is completely disabled. The model behaves as if no function declarations were provided, responding only with text. |
| **VALIDATED** (preview) | Schema adherence is strictly enforced. The model may predict function calls or text, but any function calls are guaranteed to match the declared schema exactly. |

### Using AUTO Mode

AUTO is the default and most commonly used mode. The model intelligently decides when a function call is appropriate.

```python
config = types.GenerateContentConfig(
    tools=[weather_tool],
    tool_config=types.ToolConfig(
        function_calling_config=types.FunctionCallingConfig(
            mode="AUTO",
        )
    ),
)
```

With AUTO, if the user says "Hello, how are you?" the model responds with text. If the user says "What's the weather in Paris?" the model responds with a function call.

### Using ANY Mode

ANY forces the model to always make a function call. This is useful in scenarios where you know the user's input should always trigger a specific action.

```python
config = types.GenerateContentConfig(
    tools=[weather_tool, search_tool, calendar_tool],
    tool_config=types.ToolConfig(
        function_calling_config=types.FunctionCallingConfig(
            mode="ANY",
            allowed_function_names=["get_weather", "search_products"],
        )
    ),
)
```

By providing `allowed_function_names`, you can restrict the model to a subset of declared functions. This is especially useful in multi-step workflows where only certain functions are valid at a given stage.

### Using NONE Mode

NONE disables function calling entirely, which is equivalent to not providing any function declarations.

```python
config = types.GenerateContentConfig(
    tools=[weather_tool],
    tool_config=types.ToolConfig(
        function_calling_config=types.FunctionCallingConfig(
            mode="NONE",
        )
    ),
)
```

This is useful when you want to temporarily disable function calling in certain conversation turns without removing the tool definitions from your configuration.

### Using VALIDATED Mode (Preview)

VALIDATED mode ensures that any function calls the model makes strictly adhere to the declared schemas. This is particularly useful in production systems where schema violations could cause downstream errors.

```python
config = types.GenerateContentConfig(
    tools=[weather_tool],
    tool_config=types.ToolConfig(
        function_calling_config=types.FunctionCallingConfig(
            mode="VALIDATED",
        )
    ),
)
```

Note that VALIDATED is currently in preview and its behavior may change in future releases.

## Parallel Function Calling

When a user's request requires multiple independent pieces of information, Gemini can issue several function calls simultaneously in a single turn. This significantly reduces latency by allowing your application to execute these calls in parallel rather than sequentially.

### How It Works

If a user asks "What's the weather in New York, London, and Tokyo?", the model recognizes these are three independent requests and returns all three function calls in one response.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What's the weather in New York, London, and Tokyo?",
    config=types.GenerateContentConfig(
        tools=[weather_tool],
    ),
)

# The response contains multiple function call parts
for part in response.candidates[0].content.parts:
    if part.function_call:
        print(f"Call: {part.function_call.name}({part.function_call.args})")

# Output:

# Call: get_weather({"location": "New York, NY"})

# Call: get_weather({"location": "London, UK"})

# Call: get_weather({"location": "Tokyo, Japan"})
```

### Executing in Parallel

Your application can then execute these calls concurrently:

```python
import asyncio

async def execute_parallel_calls(function_calls):
    """Execute multiple function calls concurrently."""
    tasks = []
    for fc in function_calls:
        if fc.name == "get_weather":
            tasks.append(asyncio.create_task(
                async_get_weather(**fc.args)
            ))
    return await asyncio.gather(*tasks)

# Extract all function calls from the response
function_calls = [
    part.function_call
    for part in response.candidates[0].content.parts
    if part.function_call
]

# Execute them all at once
results = asyncio.run(execute_parallel_calls(function_calls))
```

### Returning Results

When sending results back to the model, the order of function responses does not need to match the order of the original calls. The API maps each response back to its corresponding call using internal identifiers.

```python

# Build all function responses
function_response_parts = []
for fc, result in zip(function_calls, results):
    function_response_parts.append(
        types.Part.from_function_response(
            types.FunctionResponse(name=fc.name, response=result)
        )
    )

# Send all results back in a single turn
final_response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Content(role="user", parts=[
            types.Part.from_text(
                "What's the weather in New York, London, and Tokyo?"
            )
        ]),
        types.Content(role="model", parts=[
            types.Part.from_function_call(fc) for fc in function_calls
        ]),
        types.Content(role="user", parts=function_response_parts),
    ],
    config=types.GenerateContentConfig(tools=[weather_tool]),
)

print(final_response.text)

# "Here's the current weather:

#  - New York: 18°C, sunny

#  - London: 12°C, overcast

#  - Tokyo: 22°C, partly cloudy"
```

## Compositional Function Calling

Compositional function calling allows the model to chain sequential function calls where the output of one function becomes the input to the next. Unlike parallel calling, these calls have dependencies and must execute in order.

### How It Works

Consider a scenario where the user asks "What's the weather where I am right now?" The model needs to first determine the user's location, then use that location to get the weather. This requires two sequential calls.

```python

# Define both function declarations
get_location_func = types.FunctionDeclaration(
    name="get_current_location",
    description="Returns the user's current city and state based on their IP address.",
    parameters=types.Schema(type=types.Type.OBJECT, properties={}),
)

get_weather_func = types.FunctionDeclaration(
    name="get_weather",
    description="Returns the current weather for a given location.",
    parameters=types.Schema(
        type=types.Type.OBJECT,
        properties={
            "location": types.Schema(
                type=types.Type.STRING,
                description="The city and state"
            ),
        },
        required=["location"],
    ),
)

tools = types.Tool(function_declarations=[get_location_func, get_weather_func])
```

### The Chaining Process

The model manages the dependency chain automatically. Here is how the conversation unfolds across multiple turns.

**Turn 1:** The model calls `get_current_location()` first because it needs the result before it can call `get_weather()`.

```python
response_1 = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What's the weather where I am right now?",
    config=types.GenerateContentConfig(tools=[tools]),
)

# Model returns: get_current_location()
```

**Turn 2:** Your app executes `get_current_location()`, returns the result, and the model then calls `get_weather()` with that result.

```python
location_result = get_current_location()

# Returns: {"city": "San Francisco", "state": "CA"}

response_2 = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Content(role="user", parts=[
            types.Part.from_text("What's the weather where I am right now?")
        ]),
        types.Content(role="model", parts=[
            types.Part.from_function_call(
                response_1.candidates[0].content.parts[0].function_call
            )
        ]),
        types.Content(role="user", parts=[
            types.Part.from_function_response(
                types.FunctionResponse(
                    name="get_current_location",
                    response=location_result,
                )
            )
        ]),
    ],
    config=types.GenerateContentConfig(tools=[tools]),
)

# Model returns: get_weather({"location": "San Francisco, CA"})
```

**Turn 3:** Your app executes `get_weather()` and sends the result back for a final natural language response.

```python
weather_result = get_weather(location="San Francisco, CA")

final_response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        # ... full conversation history including all function calls and responses
    ],
    config=types.GenerateContentConfig(tools=[tools]),
)
print(final_response.text)

# "You're currently in San Francisco, CA. The weather is 18°C and foggy."
```

The key insight is that the model understands the dependency: it knows it cannot call `get_weather()` until it has a location, so it sequences the calls correctly without any explicit orchestration logic on your part.

## Automatic Function Calling (Python SDK)

The Python SDK provides an automatic function calling feature that dramatically reduces boilerplate. Instead of manually defining declarations, executing functions, and sending results back, the SDK handles the entire lifecycle automatically.

### How It Works

You simply pass Python functions directly as tools. The SDK uses type hints and docstrings to generate function declarations automatically, executes the functions when the model calls them, and sends results back without any manual intervention.

```python
from google import genai
from google.genai import types

client = genai.Client()

def get_weather(location: str, unit: str = "celsius") -> dict:
    """Get the current weather for a location.

    Args:
        location: The city and state, e.g. 'San Francisco, CA'.
        unit: Temperature unit, either 'celsius' or 'fahrenheit'.

    Returns:
        A dictionary containing temperature, conditions, and humidity.
    """
    # Your actual implementation
    return {
        "temperature": 22,
        "conditions": "Sunny",
        "humidity": 45,
    }

def search_restaurants(
    query: str,
    cuisine: str = "any",
    max_results: int = 5,
) -> list:
    """Search for restaurants near the user.

    Args:
        query: Search query describing what kind of restaurant to find.
        cuisine: Type of cuisine to filter by.
        max_results: Maximum number of results to return.

    Returns:
        A list of restaurant dictionaries with name, rating, and address.
    """
    # Your actual implementation
    return [{"name": "Example Restaurant", "rating": 4.5}]

# Pass functions directly -- the SDK handles everything
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What's the weather in Seattle and find me some good sushi "
             "restaurants there?",
    config=types.GenerateContentConfig(
        tools=[get_weather, search_restaurants],
    ),
)

# The response is already the final text -- no manual function execution needed
print(response.text)
```

### Docstring Requirements

The SDK uses **Google-style docstrings** to generate parameter descriptions. This means your docstring format matters for the quality of the generated schema.

```python
def book_flight(
    origin: str,
    destination: str,
    date: str,
    passengers: int = 1,
    cabin_class: str = "economy",
) -> dict:
    """Book a flight between two airports.

    Args:
        origin: The departure airport IATA code (e.g., 'SFO').
        destination: The arrival airport IATA code (e.g., 'NRT').
        date: The departure date in YYYY-MM-DD format.
        passengers: Number of passengers. Must be between 1 and 9.
        cabin_class: Cabin class, one of 'economy', 'business', or 'first'.

    Returns:
        Booking confirmation with reference number and price.
    """
    ...
```

The SDK extracts:

- The function name (`book_flight`) as the declaration name.
- The first line of the docstring as the function description.
- Each `Args:` entry as a parameter description.
- Type hints (`str`, `int`, etc.) as parameter types.
- Default values to determine which parameters are optional vs required.

### Important Notes

- **Python SDK only**: Automatic function calling is not available in the JavaScript/TypeScript, Go, or REST SDKs. Those platforms require manual declaration and execution handling.
- **Synchronous execution**: By default, functions are called synchronously. For async functions, use the async client.
- **Error handling**: If a function raises an exception, the SDK captures it and sends the error back to the model, which can then inform the user or try an alternative approach.
- **Multi-turn support**: The SDK automatically manages multi-turn conversations where the model makes multiple sequential function calls, handling the full request-response cycle until the model produces a final text response.

## Built-in Tools

Gemini provides several built-in tools that extend the model's capabilities without requiring custom function declarations. These tools are maintained by Google and are available out of the box.

| Tool | Description |
|------|-------------|
| **Google Search** | Grounds responses in real-time web content with inline citations. Ideal for answering questions about current events, recent data, or verifiable facts. |
| **File Search** | Searches through uploaded documents using semantic retrieval. Useful for finding information across large document collections. |
| **Code Execution** | Runs Python code in a sandboxed environment and returns results. Best for mathematical computations, data analysis, and generating charts. |
| **URL Context** | Fetches and analyzes the content of specified web pages. Useful for summarizing articles or extracting data from URLs. |
| **Computer Use** | Automates browser interactions via screenshots and UI actions. Enables web automation, form filling, and UI testing workflows. |

### Google Search (Grounding)

Google Search grounding connects Gemini to real-time web data. When enabled, the model can search the web to find current information and include citations in its response.

```python
from google.genai import types

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What were the top news stories today?",
    config=types.GenerateContentConfig(
        tools=[types.Tool(google_search=types.GoogleSearch())],
    ),
)

print(response.text)

# Response includes current news with source citations

# Access grounding metadata for source attribution
grounding_metadata = response.candidates[0].grounding_metadata
for chunk in grounding_metadata.grounding_chunks:
    print(f"Source: {chunk.web.title} - {chunk.web.uri}")
```

Google Search grounding is particularly valuable when you need the model to provide verifiable, up-to-date information rather than relying solely on its training data. The grounding metadata provides full source attribution, enabling you to display citations alongside the response.

### Code Execution

The code execution tool lets Gemini write and run Python code in a secure sandbox. This is particularly useful for tasks that require computation, data manipulation, or visualization.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Calculate the first 20 Fibonacci numbers and plot them.",
    config=types.GenerateContentConfig(
        tools=[types.Tool(code_execution=types.ToolCodeExecution())],
    ),
)
```

The model writes Python code, executes it in the sandbox, and incorporates the output (including generated images and computed values) into its response. The sandbox supports common scientific Python libraries including NumPy, pandas, and Matplotlib.

### URL Context

URL Context allows the model to fetch and process the content of web pages you specify.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Content(
            role="user",
            parts=[
                types.Part.from_text(
                    "Summarize the key points from this article: "
                    "https://example.com/article"
                ),
            ],
        ),
    ],
    config=types.GenerateContentConfig(
        tools=[types.Tool(url_context=types.UrlContext())],
    ),
)
```

This is useful when users reference specific URLs in their prompts and you want the model to access the actual page content rather than relying on its training data.

### File Search

File Search enables semantic search across uploaded documents. You first upload files to Gemini's file storage, then the model can search through them to answer questions.

```python

# Upload a file first
uploaded_file = client.files.upload(path="quarterly_report.pdf")

# Use file search to query across uploaded documents
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What were Q3 revenue figures?",
    config=types.GenerateContentConfig(
        tools=[types.Tool(file_search=types.FileSearch())],
    ),
)
```

File Search uses semantic retrieval rather than simple keyword matching, meaning it can find relevant passages even when the user's query uses different terminology than the source document.

### Computer Use

Computer Use enables Gemini to interact with a browser through screenshots and UI actions. The model sees the screen, reasons about what actions to take, and sends structured commands (click, type, scroll, etc.).

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Go to the Google Cloud console and check my current billing.",
    config=types.GenerateContentConfig(
        tools=[types.Tool(computer_use=types.ComputerUse(
            environment=types.Environment(
                display_width=1920,
                display_height=1080,
            ),
        ))],
    ),
)
```

Computer Use is an experimental feature that requires appropriate infrastructure to capture screenshots and execute UI actions. It is best suited for browser automation tasks that cannot be accomplished through APIs alone.

## Structured Outputs for Tools

When working with function calling and tool use, you often need the model's responses to conform to a strict schema. Gemini supports structured outputs that guarantee the response matches your expected format.

### Strict Schema Adherence

Use `strict: true` in your response schema to enforce that the model's output exactly matches the defined structure. This eliminates the need for post-processing or error handling around malformed responses.

```python
from google.genai import types

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Analyze the sentiment of: 'This product is amazing!'",
    config=types.GenerateContentConfig(
        response_mime_type="application/json",
        response_schema=types.Schema(
            type=types.Type.OBJECT,
            properties={
                "sentiment": types.Schema(
                    type=types.Type.STRING,
                    enum=["positive", "negative", "neutral"],
                ),
                "confidence": types.Schema(
                    type=types.Type.NUMBER,
                    description="Confidence score between 0 and 1",
                ),
                "reasoning": types.Schema(
                    type=types.Type.STRING,
                    description="Brief explanation of the sentiment classification",
                ),
            },
            required=["sentiment", "confidence", "reasoning"],
        ),
    ),
)
```

### Using Pydantic (Python)

Pydantic models provide an elegant way to define schemas in Python with built-in validation.

```python
from pydantic import BaseModel, Field
from enum import Enum

class Sentiment(str, Enum):
    POSITIVE = "positive"
    NEGATIVE = "negative"
    NEUTRAL = "neutral"

class SentimentAnalysis(BaseModel):
    sentiment: Sentiment = Field(description="The detected sentiment")
    confidence: float = Field(
        description="Confidence score between 0 and 1", ge=0, le=1
    )
    reasoning: str = Field(description="Explanation of the classification")

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Analyze: 'This product is amazing!'",
    config=types.GenerateContentConfig(
        response_mime_type="application/json",
        response_schema=SentimentAnalysis,
    ),
)

# Parse the response directly into a Pydantic model
result = SentimentAnalysis.model_validate_json(response.text)
print(result.sentiment)    # Sentiment.POSITIVE
print(result.confidence)   # 0.95
```

### Using Zod (JavaScript/TypeScript)

For JavaScript and TypeScript applications, Zod schemas serve the same purpose as Pydantic in Python.

```typescript
import { z } from "zod";

const SentimentSchema = z.object({
  sentiment: z.enum(["positive", "negative", "neutral"]),
  confidence: z.number().min(0).max(1),
  reasoning: z.string(),
});

const response = await client.models.generateContent({
  model: "gemini-2.5-flash",
  contents: "Analyze: 'This product is amazing!'",
  config: {
    responseMimeType: "application/json",
    responseSchema: SentimentSchema,
  },
});
```

### Streaming with Structured Outputs

When streaming responses with structured outputs, Gemini returns valid partial JSON strings that can be parsed incrementally. Each streamed chunk is a syntactically valid JSON prefix, allowing you to display results progressively as they arrive.

```python
for chunk in client.models.generate_content_stream(
    model="gemini-2.5-flash",
    contents="List 10 famous scientists and their contributions.",
    config=types.GenerateContentConfig(
        response_mime_type="application/json",
        response_schema=ScientistList,
    ),
):
    print(chunk.text, end="", flush=True)
```

This is particularly useful for long structured responses where you want to provide incremental feedback to the user rather than waiting for the complete response.

## MCP (Model Context Protocol) Integration

The Model Context Protocol (MCP) is an open standard for connecting AI models to external data sources and tools. Gemini SDKs include built-in support for MCP, making it straightforward to integrate MCP-compatible tool servers into your Gemini applications.

### How It Works

MCP support in the Gemini SDKs wraps MCP clients and automatically converts MCP tool definitions into Gemini function declarations. This means you can connect to any MCP-compatible server and the model will be able to discover and call its tools without any manual declaration mapping.

```python
from google.genai import types
from google.genai.mcp import MCPClient

# Connect to an MCP server
mcp_client = MCPClient(
    server_url="http://localhost:8080",
)

# List available tools from the MCP server
tools = await mcp_client.list_tools()

# Use MCP tools with Gemini
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Look up the latest sales data from our CRM.",
    config=types.GenerateContentConfig(
        tools=[mcp_client],  # Pass the MCP client directly as a tool
    ),
)
```

### Automatic Tool Calling with MCP

When combined with automatic function calling, the SDK handles the entire lifecycle: discovering MCP tools, sending declarations to the model, executing tool calls via the MCP server, and returning results.

```python

# The SDK handles everything automatically
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What are our top 5 customers by revenue this quarter?",
    config=types.GenerateContentConfig(
        tools=[mcp_client],
    ),
)

print(response.text)

# Final answer incorporating data from the MCP tool server
```

### Limitations

- **Tools only**: Only MCP tools are supported. MCP resources and prompts are not currently supported by the Gemini SDK integration.
- **Experimental**: MCP integration is experimental and may have breaking changes in future SDK releases. Pin your SDK version in production to avoid unexpected behavior.
- **Server compatibility**: Ensure your MCP server implements the tools specification correctly. Incompatible implementations may cause unexpected behavior or silent failures.

## Best Practices

Following these best practices will help you build reliable, performant, and maintainable function calling integrations with Gemini.

### 1. Use Descriptive Function Names and Descriptions

The model relies heavily on function names and descriptions to determine which function to call and when. Vague or ambiguous names lead to incorrect selections.

```python

# Bad: Unclear what this does
types.FunctionDeclaration(
    name="process",
    description="Processes data.",
    ...
)

# Good: Clear purpose and behavior
types.FunctionDeclaration(
    name="search_product_catalog",
    description="Searches the product catalog by keyword, category, or price "
                "range. Returns up to 20 matching products with name, price, "
                "and availability status.",
    ...
)
```

### 2. Use Strong Typing with Enums

Specific types and enum values reduce parameter errors and help the model make correct choices.

```python

# Bad: Free-form string invites errors
"sort_order": types.Schema(
    type=types.Type.STRING,
    description="How to sort the results",
)

# Good: Constrained enum prevents invalid values
"sort_order": types.Schema(
    type=types.Type.STRING,
    enum=["price_asc", "price_desc", "rating", "newest"],
    description="Sort order for results.",
)
```

### 3. Limit Tools to 10-20 Per Request

Model performance is optimal with 10 to 20 tool declarations per request. Beyond this range, the model may struggle to select the correct function or may hallucinate parameter values.

For applications with larger tool sets, implement dynamic tool selection -- use a routing layer or the model itself to select a relevant subset of tools based on the user's query before making the main function calling request.

```python
def select_relevant_tools(user_query: str, all_tools: list) -> list:
    """Use a lightweight model call to select relevant tools."""
    # First pass: determine which tool category applies
    category = classify_query(user_query)

    # Return only tools in that category (max 15)
    return [t for t in all_tools if t.category == category][:15]
```

### 4. Configure Temperature Appropriately

For deterministic, reproducible function calls, set `temperature=0`. This is important for production systems where consistent behavior is critical.

However, note that **Gemini 2.5 models perform better at their default temperature of 1.0**. Reducing temperature below 1.0 for these models can actually degrade function calling accuracy. Test both settings with your specific use case before committing to either.

```python

# For older Gemini models or when you need strict determinism
config = types.GenerateContentConfig(
    tools=[weather_tool],
    temperature=0,
)

# For Gemini 2.5 models -- default temperature often works best
config = types.GenerateContentConfig(
    tools=[weather_tool],
    temperature=1.0,
)
```

### 5. Validate High-Consequence Actions

Always confirm with users before executing actions that have significant real-world impact, such as financial transactions, data deletions, or sending communications.

```python
def handle_function_call(function_call, user_session):
    """Execute function calls with confirmation for high-risk actions."""
    HIGH_RISK_FUNCTIONS = {
        "transfer_money",
        "delete_account",
        "send_email",
        "place_order",
    }

    if function_call.name in HIGH_RISK_FUNCTIONS:
        # Request user confirmation before executing
        confirmation = request_user_confirmation(
            f"The assistant wants to call {function_call.name} "
            f"with arguments: {function_call.args}. Proceed?"
        )
        if not confirmation:
            return {"status": "cancelled", "reason": "User declined"}

    return execute_function(function_call.name, function_call.args)
```

### 6. Check the Finish Reason

Always inspect the `finish_reason` in the response to detect failures or unexpected behavior. Different finish reasons require different handling strategies.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=user_message,
    config=config,
)

candidate = response.candidates[0]

if candidate.finish_reason == "STOP":
    # Normal text response
    process_text_response(candidate.content)
elif candidate.finish_reason == "TOOL_CALLS":
    # Model wants to call functions
    process_function_calls(candidate.content)
elif candidate.finish_reason == "SAFETY":
    # Response blocked by safety filters
    handle_safety_block()
elif candidate.finish_reason == "MAX_TOKENS":
    # Response truncated due to token limit
    handle_truncation()
else:
    # Unexpected finish reason
    log_warning(f"Unexpected finish_reason: {candidate.finish_reason}")
```

### 7. Implement Robust Error Handling

Return informative error messages from your functions so the model can communicate failures gracefully to the user.

```python
def get_weather(location: str) -> dict:
    """Get weather with comprehensive error handling."""
    try:
        result = weather_api.get(location)
        return {
            "status": "success",
            "temperature": result.temp,
            "conditions": result.conditions,
        }
    except LocationNotFoundError:
        return {
            "status": "error",
            "error_type": "invalid_location",
            "message": f"Could not find weather data for '{location}'. "
                       "Please check the city name and try again.",
        }
    except RateLimitError:
        return {
            "status": "error",
            "error_type": "rate_limit",
            "message": "Weather service is temporarily unavailable. "
                       "Please try again in a few moments.",
        }
    except Exception as e:
        return {
            "status": "error",
            "error_type": "unknown",
            "message": f"An unexpected error occurred: {str(e)}",
        }
```

When the model receives structured error responses like these, it can generate helpful user-facing messages such as "I was unable to find weather data for that location. Could you double-check the city name?" rather than producing a confusing or generic error.

### 8. Be Mindful of Token Usage

Function declarations consume tokens from your context window. Each function name, description, and parameter schema is serialized and sent with every request. When working near context limits, this overhead matters.

```python

# Calculate approximate token usage from declarations
def estimate_declaration_tokens(tools):
    """Rough estimate of tokens consumed by tool declarations."""
    import json
    serialized = json.dumps([tool.to_dict() for tool in tools])
    # Rough approximation: ~4 characters per token
    return len(serialized) // 4

token_estimate = estimate_declaration_tokens(my_tools)
print(f"Tool declarations consume approximately {token_estimate} tokens")
```

Strategies to reduce token overhead:

- Keep descriptions concise but informative. Avoid repeating information already conveyed by the function or parameter name.
- Remove tools that are not relevant to the current conversation turn.
- Use dynamic tool selection to only include necessary tools per request.
- Prefer enums over long description text when constraining parameter values.

## Gemini 2.5 Limitation: Built-in Tools and Custom Functions

An important limitation to be aware of: **Gemini 2.5 models cannot combine built-in tools with custom function calling in the same request.** You must choose one or the other.

```python

# This will NOT work with Gemini 2.5 models
config = types.GenerateContentConfig(
    tools=[
        weather_tool,  # Custom function declaration
        types.Tool(google_search=types.GoogleSearch()),  # Built-in tool
    ],
)

# Instead, use EITHER custom functions:
config_custom = types.GenerateContentConfig(
    tools=[weather_tool, search_tool, calendar_tool],
)

# OR built-in tools:
config_builtin = types.GenerateContentConfig(
    tools=[types.Tool(google_search=types.GoogleSearch())],
)
```

If your application needs both capabilities, implement a routing layer that decides which type of tools to use based on the user's query, or use separate sequential model calls for each type.

```python
def route_request(user_message: str) -> types.GenerateContentConfig:
    """Route to either custom tools or built-in tools based on the query."""
    if needs_web_search(user_message):
        return types.GenerateContentConfig(
            tools=[types.Tool(google_search=types.GoogleSearch())],
        )
    else:
        return types.GenerateContentConfig(
            tools=[weather_tool, calendar_tool, search_tool],
        )
```

An alternative pattern is a two-pass approach: use built-in tools (e.g., Google Search) to gather information first, then use custom functions in a second call to take actions based on that information.

## Thought Signatures in Function Calling

Gemini 2.5 models use thought signatures during function calling as part of their internal reasoning process. These signatures are critical for the function calling pipeline to work correctly.

### What Are Thought Signatures?

When Gemini 2.5 models make function calls, they include cryptographic thought signatures in the response. These signatures must be preserved and sent back in subsequent turns. If signatures are missing or tampered with, the API returns a **400 validation error**.

### Key Rules

1. **Always preserve thought signatures.** When you receive a model response containing function calls, pass the entire response content back unchanged in the conversation history. Do not strip, alter, or reconstruct the model's function call parts.

2. **Parallel calls have a single signature.** For parallel function calls, the thought signature is attached only to the **first function call part** in the response. All subsequent function call parts in the same turn do not have their own signatures. This means you must preserve the order and integrity of the parts array.

3. **Do not modify the model's response.** Use the model's content parts exactly as received when building the conversation history for the next turn.

```python

# Correct: Preserve the model's response exactly as received
model_response = response.candidates[0].content

# Send it back unchanged in the conversation history
final_response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        user_message_content,
        model_response,  # Preserved exactly -- do not reconstruct
        function_results_content,
    ],
    config=config,
)
```

```python

# INCORRECT: Reconstructing function calls loses the thought signature

# Do NOT do this:
reconstructed_parts = []
for part in response.candidates[0].content.parts:
    if part.function_call:
        reconstructed_parts.append(
            types.Part.from_function_call(
                types.FunctionCall(
                    name=part.function_call.name,
                    args=part.function_call.args,
                )
            )
        )

# This loses the thought signature and will cause a 400 error
```

### SDK Handling

If you are using the official Gemini SDKs (Python, JavaScript, Go) in their standard workflows, thought signatures are handled automatically. The SDK preserves signatures when managing conversation history and function call cycles. You only need to worry about signatures if you are:

- Building custom conversation management outside the SDK.
- Manually constructing API requests via REST.
- Serializing and deserializing conversation history in a custom format (e.g., storing conversation state in a database between requests).

```python

# Using the SDK's built-in chat -- signatures are handled automatically
chat = client.chats.create(
    model="gemini-2.5-flash",
    config=types.GenerateContentConfig(tools=[weather_tool]),
)

response = chat.send_message("What's the weather in Paris?")

# SDK automatically handles function execution, signatures, and response cycles
print(response.text)
```

By letting the SDK manage the conversation lifecycle, you avoid signature-related issues entirely.

# Chapter 8: Multimodal Capabilities

Gemini is a natively multimodal model family, meaning it was designed from the ground up to understand and generate across text, images, audio, video, and code. Unlike earlier approaches that bolted vision or audio modules onto a text-only backbone, Gemini's architecture treats all modalities as first-class inputs. This chapter covers how to effectively prompt Gemini for each modality, the technical constraints you need to respect, and the best practices that lead to reliable results.

## Image Understanding

Gemini supports multimodal image processing without requiring specialized training or fine-tuning. You can send images alongside text prompts and receive rich, contextual responses that demonstrate genuine visual comprehension.

### Core Capabilities

Gemini can perform a broad range of image understanding tasks out of the box:

- **Image captioning and description** -- Generate detailed natural-language descriptions of image contents, including scene composition, objects, colors, text, and spatial relationships.
- **Visual question answering (VQA)** -- Answer specific questions about an image, such as "What brand is on the sign?" or "How many people are in the room?"
- **Image classification** -- Categorize images into predefined or open-ended classes based on visual content.
- **Object detection with bounding boxes** -- Identify and locate objects within an image, returning normalized bounding box coordinates in the format `[ymin, xmin, ymax, xmax]` where each value falls in the 0--1000 range.
- **Image segmentation (Gemini 2.5+)** -- Produce probability masks that delineate object boundaries at the pixel level, enabling fine-grained spatial understanding.

### Input Methods

There are two primary ways to send images to the Gemini API:

**Inline Data (Base64-Encoded)**

For small to medium images, you can embed image data directly in the request as base64-encoded strings. The total inline data payload must not exceed 20MB.

```python
import base64
from google import genai
from google.genai import types

client = genai.Client()

with open("photo.jpg", "rb") as f:
    image_data = base64.b64encode(f.read()).decode("utf-8")

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_bytes(
            data=base64.b64decode(image_data),
            mime_type="image/jpeg"
        ),
        "Describe what you see in this image in detail."
    ]
)
print(response.text)
```

**File API (Recommended for Larger Files)**

For larger files or images you plan to reference across multiple requests, upload them via the File API first. This avoids redundant data transfer and keeps your requests compact.

```python
from google import genai
from google.genai import types

client = genai.Client()

# Upload the file once
uploaded_file = client.files.upload(file="large_photo.jpg")

# Reference it in as many requests as needed
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=uploaded_file.uri,
            mime_type=uploaded_file.mime_type
        ),
        "What objects can you identify in this image? Return them as a JSON array."
    ]
)
print(response.text)
```

### Supported Formats and Limits

| Property | Value |
|----------|-------|
| Supported formats | PNG, JPEG, WEBP, HEIC, HEIF |
| Maximum images per request | 3,600 |
| Maximum inline data size | 20MB total |

### Token Calculation for Images

Understanding how images consume tokens is essential for managing costs and staying within context limits:

- If **both dimensions are 384 pixels or smaller**, the image costs a flat **258 tokens**.
- For **larger images**, Gemini tiles the image into 768x768 pixel tiles. Each tile costs **258 tokens**.

For example, a 1536x768 image would be split into 2 tiles (2 x 258 = 516 tokens), while a 384x200 image would cost only 258 tokens.

### Object Detection with Bounding Boxes

One of Gemini's most powerful image capabilities is returning structured bounding box coordinates. The coordinates use a normalized 0--1000 scale, making them resolution-independent.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=uploaded_file.uri,
            mime_type="image/jpeg"
        ),
        """Detect all vehicles in this image. For each vehicle, return a JSON object with:
        - "label": the type of vehicle
        - "bounding_box": [ymin, xmin, ymax, xmax] in normalized 0-1000 coordinates

        Return the results as a JSON array."""
    ]
)
print(response.text)
```

Example output:

```json
[
  {
    "label": "sedan",
    "bounding_box": [320, 150, 680, 490]
  },
  {
    "label": "truck",
    "bounding_box": [280, 520, 710, 850]
  }
]
```

To convert these normalized coordinates to actual pixel positions, multiply each value by the corresponding image dimension and divide by 1000:

```python
def normalize_to_pixels(bbox, img_width, img_height):
    ymin, xmin, ymax, xmax = bbox
    return {
        "ymin": int(ymin * img_height / 1000),
        "xmin": int(xmin * img_width / 1000),
        "ymax": int(ymax * img_height / 1000),
        "xmax": int(xmax * img_width / 1000)
    }
```

### Best Practices for Image Prompting

1. **Verify correct image rotation.** Gemini processes images as-is. If an image is rotated or flipped, the model will interpret it in that orientation, which can lead to incorrect spatial descriptions or bounding box coordinates.

2. **Use clear, non-blurry images.** While Gemini can handle some noise and compression artifacts, sharp images with good lighting produce significantly better results for fine-grained tasks like text extraction or small object detection.

3. **Place text prompts after image parts in the contents array.** When constructing the `contents` list, always include image parts first, followed by your text prompt. This ordering aligns with how the model processes multimodal inputs and yields more accurate responses.

```python

# Correct ordering: image first, then text
contents = [
    types.Part.from_uri(file_uri=file.uri, mime_type="image/jpeg"),
    "What text appears on the sign in this image?"
]

# Avoid: text first, then image
contents = [
    "What text appears on the sign in this image?",
    types.Part.from_uri(file_uri=file.uri, mime_type="image/jpeg")
]
```

4. **Be specific about the output format.** Instead of asking "What's in this image?", specify whether you want JSON, a bulleted list, a single sentence, or a structured table.

5. **Use single-image prompts for precision tasks.** While Gemini supports up to 3,600 images per request, detection and classification accuracy is highest when working with one or a small number of images at a time.

## Video Understanding

Gemini can process video content natively, enabling description, question answering, and timestamp-based referencing of video segments.

### Capabilities

- **Video description** -- Generate summaries or detailed narrations of video content.
- **Visual question answering** -- Answer specific questions about events, objects, or actions in a video.
- **Timestamp referencing** -- Reference specific moments in a video by timestamp, enabling precise queries like "What happened at 1:23?"
- **Action recognition** -- Identify and classify actions or events occurring across frames.

### Limits and Processing

| Property | Value |
|----------|-------|
| Maximum videos per request | 10 |
| Frame processing | Configurable resolution |

Gemini processes videos by extracting frames at a configurable rate. Each frame is treated as an image and consumes tokens accordingly. The frame extraction rate can be adjusted to balance between detail and token usage.

### Video Prompting Examples

```python
from google import genai
from google.genai import types

client = genai.Client()

# Upload video via File API
video_file = client.files.upload(file="presentation.mp4")

# Wait for processing to complete
import time
while video_file.state.name == "PROCESSING":
    time.sleep(5)
    video_file = client.files.get(name=video_file.name)

# Ask questions about the video
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=video_file.uri,
            mime_type="video/mp4"
        ),
        "Summarize the key points discussed in this presentation. Include timestamps."
    ]
)
print(response.text)
```

### Timestamp-Based Queries

You can ask Gemini to analyze specific segments or reference particular moments:

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=video_file.uri,
            mime_type="video/mp4"
        ),
        "What is the speaker demonstrating between 2:15 and 3:45?"
    ]
)
```

### Best Practices for Video Prompting

- Upload videos via the File API rather than inline encoding -- video files are typically too large for inline data.
- Allow processing time after upload before sending queries; check the file state until it moves from `PROCESSING` to `ACTIVE`.
- Use timestamp references to focus the model's attention on specific segments.
- For long videos, consider asking for a structured timeline or chapter-by-chapter summary rather than a single monolithic description.

## Audio Processing

Gemini provides robust audio understanding capabilities, including speech recognition, audio analysis, and real-time streaming through the Live API.

### Speech Recognition and Analysis

Gemini can transcribe speech, identify speakers, analyze tone, and answer questions about audio content.

```python
from google import genai
from google.genai import types

client = genai.Client()

audio_file = client.files.upload(file="meeting_recording.wav")

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=audio_file.uri,
            mime_type="audio/wav"
        ),
        """Transcribe this meeting recording. For each speaker:
        1. Assign a speaker label (Speaker A, Speaker B, etc.)
        2. Include timestamps for each utterance
        3. Note any action items mentioned"""
    ]
)
print(response.text)
```

### Live API for Real-Time Audio Streaming

The Live API enables real-time audio interactions over WebSockets, supporting use cases like voice assistants, live transcription, and interactive audio applications.

**Audio Format Specifications:**

| Direction | Format | Sample Rate | Channels |
|-----------|--------|-------------|----------|
| Input | 16-bit PCM | 16 kHz | Mono |
| Output | 16-bit PCM | 24 kHz | Mono |

```python
import asyncio
from google import genai

client = genai.Client()

async def audio_stream():
    async with client.aio.live.connect(
        model="gemini-2.5-flash-live-preview"
    ) as session:
        # Send audio chunks
        audio_chunk = read_audio_chunk()  # Your audio capture function
        await session.send(
            input=types.LiveClientRealtimeInput(
                media_chunks=[
                    types.Blob(data=audio_chunk, mime_type="audio/pcm")
                ]
            )
        )

        # Receive responses
        async for response in session.receive():
            if response.data:
                play_audio(response.data)  # Your audio playback function
            if response.text:
                print(response.text)

asyncio.run(audio_stream())
```

### Audio Prompting Best Practices

- Ensure input audio meets the 16-bit PCM, 16 kHz, mono specification for optimal recognition accuracy.
- For long recordings, provide context about the expected content (e.g., "This is a technical interview about distributed systems").
- When requesting transcription, specify the expected language if it is not English.
- Use the Live API for interactive applications where latency matters; use the standard API with file uploads for batch processing of recorded audio.

## PDF Document Understanding

Gemini can process PDF documents natively, extracting text, understanding layouts, interpreting charts and tables, and answering questions about document content.

### Native PDF Processing

PDFs are treated as a sequence of page images, allowing Gemini to understand both textual content and visual layout elements like tables, charts, headers, and figures.

```python
from google import genai
from google.genai import types

client = genai.Client()

pdf_file = client.files.upload(file="annual_report.pdf")

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=pdf_file.uri,
            mime_type="application/pdf"
        ),
        "Extract all financial figures from the quarterly results table on page 12."
    ]
)
print(response.text)
```

### Resolution Settings for PDFs

The resolution at which PDF pages are processed directly affects both accuracy and token consumption:

- **`media_resolution_medium`** -- Consumes approximately **560 tokens per page**. This is the recommended default for standard PDFs with normal text density, charts, and simple tables.
- **`media_resolution_high`** -- Consumes more tokens per page but provides finer detail. Use this setting for dense documents, small-font text, or PDFs with intricate diagrams where precision matters.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=pdf_file.uri,
            mime_type="application/pdf"
        ),
        "Extract all data from every table in this document."
    ],
    config=types.GenerateContentConfig(
        media_resolution="media_resolution_high"
    )
)
```

### PDF Prompting Best Practices

- Reference specific page numbers when you know where the relevant content is located.
- For large documents, break your queries into page ranges rather than asking about the entire document at once.
- Use `media_resolution_medium` as the default and switch to `media_resolution_high` only when standard resolution produces incomplete or inaccurate extractions.
- When extracting structured data (tables, forms), ask for the output in a specific format like JSON or Markdown tables.

## Media Resolution Control (Gemini 3)

Gemini 3 introduces explicit media resolution controls that let you fine-tune the tradeoff between processing fidelity and token consumption for each content type. Choosing the right resolution setting can dramatically reduce costs without sacrificing quality for your specific use case.

### Resolution Settings Reference

| Content Type | Recommended Setting | Tokens per Unit |
|-------------|-------------------|-----------------|
| Images | `media_resolution_high` | 1120 max per image |
| PDFs | `media_resolution_medium` | 560 per page |
| Video (general) | `media_resolution_low` | 70 per frame |
| Video (text-heavy) | `media_resolution_high` | 280 per frame |

### Choosing the Right Resolution

**Images** -- Default to `media_resolution_high` (up to 1120 tokens per image). Images typically require the highest resolution because tasks like object detection, text extraction, and fine-grained classification depend on pixel-level detail.

**PDFs** -- Default to `media_resolution_medium` (560 tokens per page). Most documents are readable at medium resolution, and the token savings across a multi-page document are substantial. Only escalate to high resolution for documents with very small text or dense technical diagrams.

**Video (general)** -- Default to `media_resolution_low` (70 tokens per frame). For general video understanding -- summarization, action recognition, scene description -- low resolution captures sufficient visual information while keeping token usage manageable across thousands of frames.

**Video (text-heavy)** -- Use `media_resolution_high` (280 tokens per frame). When the video contains on-screen text, code, slides, or diagrams that need to be read accurately, higher resolution is essential.

```python

# Example: Processing a long video at low resolution for general summary
response = client.models.generate_content(
    model="gemini-3-pro",
    contents=[
        types.Part.from_uri(
            file_uri=video_file.uri,
            mime_type="video/mp4"
        ),
        "Provide a 5-bullet summary of this 30-minute lecture."
    ],
    config=types.GenerateContentConfig(
        media_resolution="media_resolution_low"
    )
)

# Example: Processing a code walkthrough video at high resolution
response = client.models.generate_content(
    model="gemini-3-pro",
    contents=[
        types.Part.from_uri(
            file_uri=video_file.uri,
            mime_type="video/mp4"
        ),
        "Transcribe all code shown on screen between 5:00 and 10:00."
    ],
    config=types.GenerateContentConfig(
        media_resolution="media_resolution_high"
    )
)
```

## Native Image Generation

In addition to understanding images, Gemini can generate images natively. Two model variants serve different use cases, from fast prototyping to professional-grade output.

### Nano Banana (gemini-2.5-flash-image)

Nano Banana is optimized for fast, efficient image generation, making it ideal for high-volume and low-latency workflows such as chatbot responses, rapid prototyping, and real-time applications.

**Key characteristics:**

- Fast inference with low latency
- Optimized for high-volume generation workloads
- Supports interleaved text-and-image output

**Supported aspect ratios:** `1:1`, `2:3`, `3:2`, `3:4`, `4:3`, `4:5`, `5:4`, `9:16`, `16:9`, `21:9`

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-2.5-flash-image",
    contents="Generate a watercolor painting of a coastal village at sunset.",
    config=types.GenerateContentConfig(
        response_modalities=["TEXT", "IMAGE"],
        image_config=types.ImageConfig(
            aspect_ratio="16:9"
        )
    )
)

# Save the generated image
for part in response.candidates[0].content.parts:
    if part.inline_data:
        with open("village_sunset.png", "wb") as f:
            f.write(part.inline_data.data)
    elif part.text:
        print(part.text)
```

### Nano Banana Pro (gemini-3-pro-image-preview)

Nano Banana Pro is the professional-grade image generation model, designed for high-fidelity output with advanced capabilities that go far beyond basic generation.

**Key characteristics:**

- Professional **4K image generation**
- **Advanced text rendering** for infographics, menus, diagrams, and presentations
- **Google Search grounding** for generating images with current, real-world data
- Support for up to **14 reference images** (maximum 6 objects, 5 humans)
- **Thinking mode** with internal reasoning for complex generation tasks

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="Generate an infographic of the current weather in Tokyo.",
    config=types.GenerateContentConfig(
        tools=[{"google_search": {}}],
        image_config=types.ImageConfig(
            aspect_ratio="16:9",
            image_size="4K"
        )
    )
)

for part in response.candidates[0].content.parts:
    if part.inline_data:
        with open("tokyo_weather.png", "wb") as f:
            f.write(part.inline_data.data)
    elif part.text:
        print(part.text)
```

### Prompting Tips for Image Generation

Effective image generation prompts differ significantly from text prompts. Here are the key principles:

**1. Describe the scene, do not just list keywords.**

```

# Less effective
"mountain, sunset, lake, reflection, dramatic"

# More effective
"A serene mountain lake at golden hour, with snow-capped peaks reflected
perfectly in the still water. The sky transitions from deep orange near
the horizon to soft purple overhead. A small wooden dock extends into
the foreground."
```

**2. Use photography terminology for photorealistic results.**

Reference specific camera settings, lens types, lighting conditions, and photographic styles to guide the model toward realistic output:

```
"A portrait of an elderly craftsman in his workshop, shot with an 85mm
lens at f/1.8. Soft window light illuminates his face from the left side.
Shallow depth of field blurs the tools hanging on the wall behind him.
Shot on medium format film with natural color grading."
```

**3. Be explicit about text, font style, and design for text rendering.**

When generating images that contain text (signs, labels, infographics), specify the exact text, font characteristics, and placement:

```
"A minimalist restaurant menu card on cream-colored textured paper. The
restaurant name 'BOTANICA' appears at the top in elegant serif font,
all caps, dark green color. Below it, three sections: Starters, Mains,
and Desserts, each with 4 items listed in a clean sans-serif font."
```

**4. Leverage multi-turn editing with thought signatures.**

Nano Banana Pro supports iterative refinement through multi-turn conversations. Thought signatures maintain context across turns, so you can progressively refine a generated image:

```
Turn 1: "Generate a product photo of a minimalist ceramic mug on a
         wooden table with soft morning light."

Turn 2: "Change the mug color from white to terracotta, and add a
         small succulent plant next to it."

Turn 3: "Make the background slightly more blurred and add steam
         rising from the mug."
```

### Reference Image Support (Nano Banana Pro)

Nano Banana Pro can accept reference images to guide generation, enabling style transfer, product visualization, and consistent character depiction.

```python

# Upload reference images
ref_product = client.files.upload(file="my_product.jpg")
ref_style = client.files.upload(file="desired_style.jpg")

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents=[
        types.Part.from_uri(
            file_uri=ref_product.uri,
            mime_type="image/jpeg"
        ),
        types.Part.from_uri(
            file_uri=ref_style.uri,
            mime_type="image/jpeg"
        ),
        """Using the product from the first image and the visual style
        from the second image, generate a product advertisement.
        Place the product on a marble surface with dramatic side lighting.
        Add the text 'ARTISAN COLLECTION' in gold serif font at the top."""
    ],
    config=types.GenerateContentConfig(
        image_config=types.ImageConfig(
            aspect_ratio="4:5",
            image_size="4K"
        )
    )
)
```

**Limits for reference images:**
- Maximum 14 reference images per request
- Maximum 6 distinct objects
- Maximum 5 human subjects

## Live API

The Live API enables low-latency, real-time voice and video interactions over WebSocket connections. It is designed for conversational AI applications that require sub-second response times.

### Architecture and Connection Model

The Live API uses a persistent WebSocket connection, allowing bidirectional streaming of audio, video, and text. This eliminates the overhead of repeated HTTP requests and enables truly interactive experiences.

**Available Models:**

- `gemini-2.5-flash-live-preview` -- Optimized for sub-second native audio streaming

### Key Features

**Voice Activity Detection (VAD)**

The Live API includes built-in voice activity detection that automatically segments speech, handles pauses, and supports natural turn-taking. It also detects interruptions, allowing the model to stop speaking when the user begins talking.

```python
import asyncio
from google import genai
from google.genai import types

client = genai.Client()

async def voice_assistant():
    config = types.LiveConnectConfig(
        response_modalities=["AUDIO"],
        speech_config=types.SpeechConfig(
            voice_config=types.VoiceConfig(
                prebuilt_voice_config=types.PrebuiltVoiceConfig(
                    voice_name="Kore"
                )
            )
        )
    )

    async with client.aio.live.connect(
        model="gemini-2.5-flash-live-preview",
        config=config
    ) as session:
        # Send system instruction
        await session.send(
            input="You are a helpful voice assistant. Keep responses concise.",
            end_of_turn=True
        )

        # Stream audio input and receive audio output
        while True:
            audio_input = await capture_microphone()
            await session.send(
                input=types.LiveClientRealtimeInput(
                    media_chunks=[
                        types.Blob(
                            data=audio_input,
                            mime_type="audio/pcm"
                        )
                    ]
                )
            )

            async for response in session.receive():
                if response.data:
                    play_audio(response.data)

asyncio.run(voice_assistant())
```

**Session Management for Long Conversations**

The Live API supports session management to maintain context across extended interactions. Sessions can be paused, resumed, and managed to handle conversations that span minutes or hours.

**Ephemeral Tokens for Client-Side Authentication**

For browser-based or mobile applications, the Live API supports ephemeral tokens that provide time-limited access without exposing your API key to client-side code.

```python

# Server-side: Generate an ephemeral token
from google import genai

client = genai.Client()
token = client.auth.create_ephemeral_token(
    config={"expires_in_seconds": 300}
)

# Send token.token to the client application

# Client uses this token to connect directly to the Live API
```

### Partner Integrations

The Live API integrates with several third-party platforms and frameworks for building production voice and video applications:

| Partner | Use Case |
|---------|----------|
| **Pipecat** | Open-source framework for voice AI pipelines |
| **LiveKit** | Real-time communication infrastructure |
| **Fishjam** | WebRTC-based media server |
| **ADK (Agent Development Kit)** | Google's agent framework integration |
| **Vision Agents** | Computer vision agent workflows |
| **Voximplant** | Cloud communications platform |

These integrations handle the complexity of media capture, encoding, WebSocket management, and playback, letting you focus on the conversational logic and prompt engineering.

## Multi-Turn Image Editing

Gemini supports iterative image editing through multi-turn chat interfaces. Rather than generating a new image from scratch each time, you can refine and modify previously generated images through conversation.

### How It Works

When you generate an image in a conversation, Gemini maintains context about the generated output. Subsequent turns can reference and modify the image without re-describing it from scratch. Thought signatures -- internal representations of the generated image -- maintain continuity across turns.

### Editing Operations

You can perform a wide range of edits through natural language:

**Add elements:**
```
"Add a row of cypress trees along the left edge of the landscape."
```

**Remove elements:**
```
"Remove the person standing in the background."
```

**Modify existing elements:**
```
"Make the building in the center taller and change its color to red brick."
```

**Change style or color grading:**
```
"Convert this to a noir style with high contrast black and white."
```

**Adjust composition:**
```
"Zoom out to show more of the surrounding environment."
```

### Multi-Turn Editing Example

```python
from google import genai
from google.genai import types

client = genai.Client()

chat = client.chats.create(
    model="gemini-3-pro-image-preview",
    config=types.GenerateContentConfig(
        response_modalities=["TEXT", "IMAGE"]
    )
)

# Turn 1: Generate the initial image
response = chat.send_message(
    "Generate a modern living room with floor-to-ceiling windows, "
    "a gray sectional sofa, and a minimalist coffee table. "
    "Afternoon sunlight streams in from the left."
)
save_image(response, "living_room_v1.png")

# Turn 2: Add elements
response = chat.send_message(
    "Add a large abstract painting on the wall behind the sofa. "
    "Use warm tones -- burnt orange, gold, and deep red."
)
save_image(response, "living_room_v2.png")

# Turn 3: Modify the lighting
response = chat.send_message(
    "Change the time of day to evening. The windows should show a "
    "twilight sky, and add warm interior lighting from table lamps."
)
save_image(response, "living_room_v3.png")

# Turn 4: Style change
response = chat.send_message(
    "Apply a soft film photography look with slightly muted colors "
    "and gentle grain."
)
save_image(response, "living_room_v4.png")
```

### Best Practices for Multi-Turn Editing

1. **Be specific about what to change and what to preserve.** Vague instructions like "make it better" give the model too much latitude and may alter elements you wanted to keep.

2. **Make incremental changes.** Large, sweeping edits in a single turn are more likely to produce unexpected results. Build up changes gradually.

3. **Reference spatial positions explicitly.** Use terms like "top-left corner," "foreground," "behind the main subject" rather than ambiguous references.

4. **Thought signatures maintain context automatically.** You do not need to re-describe the entire image in each turn. The model remembers what it generated previously.

5. **Start a new conversation for a fundamentally different image.** If the edits diverge so far from the original that little of the initial generation remains relevant, it is more effective to start fresh with a new prompt.

## Combining Modalities in a Single Request

One of Gemini's most powerful features is the ability to combine multiple modalities in a single request. You can send images, audio, video, PDFs, and text together, enabling complex workflows that would otherwise require multiple models or processing stages.

### Cross-Modal Analysis

```python

# Analyze a video alongside its transcript PDF
response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents=[
        types.Part.from_uri(
            file_uri=video_file.uri,
            mime_type="video/mp4"
        ),
        types.Part.from_uri(
            file_uri=transcript_pdf.uri,
            mime_type="application/pdf"
        ),
        """Compare the video content with the provided transcript.
        Identify any discrepancies between what is shown in the video
        and what is described in the transcript. Return findings as
        a structured table with columns: Timestamp, Video Content,
        Transcript Content, Discrepancy."""
    ]
)
```

### Image-to-Image Comparison

```python

# Compare two product images
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=[
        types.Part.from_uri(
            file_uri=product_v1.uri,
            mime_type="image/jpeg"
        ),
        types.Part.from_uri(
            file_uri=product_v2.uri,
            mime_type="image/jpeg"
        ),
        """Compare these two product photos. The first is the current
        design, the second is the proposed revision. List all visible
        differences in a bulleted format, organized by: Shape, Color,
        Material, Branding, and Overall Impression."""
    ]
)
```

### Best Practices for Multi-Modal Requests

- Order the content parts logically: place the primary content first, supporting content second, and the text prompt last.
- When combining modalities, be explicit about which input you are referring to (e.g., "In the first image..." or "In the audio recording...").
- Monitor token usage carefully when combining large media files, as the total token count across all modalities must fit within the model's context window.
- Use the File API for any media content that exceeds a few hundred kilobytes to keep request payloads manageable.

## Summary

Gemini's multimodal capabilities represent a unified approach to understanding and generating across text, images, audio, video, and documents. The key principles to remember:

- **Image understanding** supports captioning, VQA, classification, object detection with bounding boxes, and segmentation, all without specialized training.
- **Video and audio processing** extend these capabilities to temporal media, with the Live API enabling real-time streaming interactions.
- **PDF processing** treats documents as visual content, preserving layout understanding beyond raw text extraction.
- **Media resolution controls** in Gemini 3 give you fine-grained control over the quality-cost tradeoff for each content type.
- **Native image generation** through Nano Banana and Nano Banana Pro covers everything from rapid prototyping to professional 4K output with text rendering and search grounding.
- **Multi-turn editing** enables iterative refinement of generated images through natural conversation.
- **The Live API** powers real-time voice and video applications with sub-second latency, VAD, and broad partner ecosystem support.

Effective multimodal prompting follows the same core principles as text prompting -- be specific, provide context, and structure your requests clearly -- but adds the additional dimension of managing media inputs, resolution settings, and token budgets across modalities.

# Chapter 9: Grounding, Caching & Advanced Features

Gemini models become significantly more powerful when you move beyond basic prompt-and-response interactions. This chapter covers the advanced features that connect Gemini to real-time information, reduce costs through intelligent caching, handle massive contexts, and give you fine-grained control over output format, code execution, and safety behavior.

## Grounding with Google Search

Grounding with Google Search connects the Gemini model to live web content at inference time. Instead of relying solely on training data, the model can retrieve and reason over current information from the web, then return citations linking back to the original source material.

This is particularly valuable for queries about recent events, rapidly changing data (stock prices, weather, sports scores), or any topic where the model's training data may be outdated.

### How It Works

When you enable Google Search grounding, the model:

1. Analyzes your prompt to determine if web search would improve the response
2. Generates and executes one or more search queries
3. Retrieves relevant snippets from search results
4. Synthesizes the information into a coherent response
5. Returns inline citations linking to the source URLs

### Basic Usage

```python
from google import genai
from google.genai import types

client = genai.Client()

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What are the latest developments in quantum computing?",
    config=types.GenerateContentConfig(
        tools=[types.Tool(google_search=types.GoogleSearch())]
    ),
)

# Access the response text
print(response.text)

# Access grounding metadata and citations
if response.candidates[0].grounding_metadata:
    for chunk in response.candidates[0].grounding_metadata.grounding_chunks:
        print(f"Source: {chunk.web.title} - {chunk.web.uri}")
```

### Dynamic Retrieval Configuration

You can configure the model to decide dynamically whether to use search for a given query. This avoids unnecessary search calls (and associated costs) for prompts the model can answer confidently from its training data.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What happened at the tech conference yesterday?",
    config=types.GenerateContentConfig(
        tools=[types.Tool(
            google_search=types.GoogleSearch(),
        )],
    ),
)
```

The model will assess whether grounding is needed based on the query's recency requirements and the model's confidence in its existing knowledge.

### Pricing

- **Free tier:** 1,500 requests per day (RPD) at no charge
- **Paid tier:** $14 to $35 per 1,000 grounded queries, depending on the model and configuration

This pricing is in addition to standard token-based billing. Plan your usage accordingly -- if only a fraction of your queries need real-time data, avoid enabling search globally and instead apply it selectively.

### Combining Google Search with URL Context

For maximum depth, combine Google Search grounding with URL Context. Google Search finds the relevant pages, and URL Context lets you do deep analysis of specific pages from the results.

```python

# Step 1: Use Google Search to find relevant sources
search_response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Find recent research papers on transformer architecture improvements",
    config=types.GenerateContentConfig(
        tools=[types.Tool(google_search=types.GoogleSearch())]
    ),
)

# Step 2: Extract URLs from grounding metadata, then use URL Context

# for deep analysis of the most relevant pages
urls_of_interest = []
if search_response.candidates[0].grounding_metadata:
    for chunk in search_response.candidates[0].grounding_metadata.grounding_chunks:
        urls_of_interest.append(chunk.web.uri)

# Use URL Context to deeply analyze specific pages
deep_response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=f"Analyze the key findings from these sources: {urls_of_interest[:5]}",
    config=types.GenerateContentConfig(
        tools=[types.Tool(url_context=types.UrlContext())]
    ),
)
```

## Grounding with Google Maps

Google Maps grounding enables location-aware responses by connecting Gemini to Google's mapping and places data. This is ideal for queries involving physical locations, businesses, directions, and geographic context.

### Availability

- **Generally Available (GA):** Gemini 2.5 models
- **Not yet supported:** Gemini 3 models (support expected in a future release)

### Usage

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What are the best-rated Italian restaurants near downtown San Francisco?",
    config=types.GenerateContentConfig(
        tools=[types.Tool(google_maps=types.GoogleMaps())]
    ),
)

print(response.text)
```

The model returns location-aware responses enriched with data from Google Maps, including business names, ratings, addresses, and other relevant place information.

### Pricing

- **Free tier:** 1,500 requests per day (RPD) at no charge
- **Paid tier:** $25 per 1,000 requests

### Best Use Cases

- Local business discovery and recommendations
- Travel planning and itinerary building
- Geographic context for location-based queries
- Comparing venues, restaurants, or services by area

## Context Caching

Context caching lets you store and reuse large blocks of input tokens across multiple requests. This is one of the most impactful cost optimization strategies available, especially when you repeatedly query against the same large document, codebase, or system prompt.

There are two distinct approaches: implicit (automatic) and explicit (manual).

### Implicit Caching (Automatic)

Implicit caching requires no configuration on your part. The system automatically detects when multiple requests share common prefixes and caches them behind the scenes.

```python

# These two requests share the same system instruction prefix.

# The system may automatically cache the shared content.

config = types.GenerateContentConfig(
    system_instruction="You are an expert financial analyst. Always cite sources and provide quantitative backing for your claims.",
)

# Request 1
response1 = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Analyze Apple's Q4 2025 earnings report.",
    config=config,
)

# Request 2 - the shared system instruction may be served from cache
response2 = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Compare Apple's margins to Microsoft's over the last 3 years.",
    config=config,
)
```

**Key characteristics of implicit caching:**

- Fully automatic -- no setup or management required
- The system detects repeated content prefixes across requests
- Cached content is served at reduced token rates when a cache hit occurs
- You have no direct control over cache eviction or lifetime

### Explicit Caching (Manual TTL)

Explicit caching gives you direct control. You create named caches with a custom time-to-live (TTL), then reference them in subsequent requests.

```python

# Step 1: Create a cache with a large document
long_document = open("/path/to/large_document.txt").read()

cache = client.caches.create(
    model="gemini-2.5-flash",
    config=types.CreateCachedContentConfig(
        display_name="quarterly-report-analysis",
        contents=[
            types.Content(
                role="user",
                parts=[types.Part(text=long_document)]
            )
        ],
        system_instruction="You are a financial analyst. Analyze the provided quarterly report.",
        ttl="7200s",  # Cache lives for 2 hours
    ),
)

print(f"Cache created: {cache.name}")
print(f"Token count: {cache.usage_metadata.total_token_count}")
```

```python

# Step 2: Use the cache in subsequent requests
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What were the top 3 revenue drivers mentioned in the report?",
    config=types.GenerateContentConfig(
        cached_content=cache.name,
    ),
)

print(response.text)
```

```python

# Step 3: Update the TTL if you need more time
client.caches.update(
    name=cache.name,
    config=types.UpdateCachedContentConfig(
        ttl="14400s",  # Extend to 4 hours
    ),
)

# Step 4: Delete the cache when done
client.caches.delete(name=cache.name)
```

**Requirements and constraints:**

- Minimum **2,048 tokens** of content to create a cache
- Cached content must be identical across requests (same tokens in the same order)
- Caches are model-specific -- a cache created for `gemini-2.5-flash` cannot be used with `gemini-2.5-pro`

### Managing Caches

```python

# List all active caches
for cache in client.caches.list():
    print(f"{cache.display_name}: {cache.name} "
          f"(expires: {cache.expire_time})")

# Get details of a specific cache
cache_info = client.caches.get(name=cache.name)
print(f"Total tokens cached: {cache_info.usage_metadata.total_token_count}")
```

### Pricing

Context caching delivers substantial savings for repeated queries against the same content:

| Cost Component | Rate |
|---|---|
| Cached input tokens | ~10% of standard input token price |
| Cache storage | Hourly rate per million tokens (e.g., $4.50/MTok/hour for Gemini 3.1 Pro) |
| Output tokens | Standard output token pricing (no discount) |

**Break-even analysis:** Caching pays for itself quickly when you make more than a handful of requests against the same content. The storage cost is minimal compared to the 90% discount on input tokens.

**Example calculation:**
- A 100,000-token document queried 50 times
- Without caching: 50 x 100,000 = 5,000,000 input tokens at full price
- With caching: 100,000 tokens at full price (first request) + 49 x 100,000 at 10% price + storage cost
- Net savings: approximately 85-88% on input token costs

### Best Use Cases for Context Caching

| Use Case | Why Caching Helps |
|---|---|
| Repeated analysis of a large document | Avoid re-processing the same document on every query |
| Chatbots with fixed system prompts and context | System instructions and reference materials stay cached |
| Code analysis across multiple queries | Cache a large codebase, then ask different questions about it |
| Multi-turn conversations with large context | Keep the conversation history cached between turns |
| Batch processing with shared context | Same reference document applied to many different inputs |

## Long Context Tips

Gemini models support context windows up to 1 million tokens (and beyond for some models). This is an enormous amount of information, but using it effectively requires understanding the practical implications.

### What 1 Million Tokens Looks Like

To put the context window in perspective:

| Content Type | Approximate Equivalent |
|---|---|
| Lines of code (80 chars/line) | ~50,000 lines |
| Text messages | ~5 years of messaging history |
| Novels | ~8 average-length novels |
| Podcast transcripts | 200+ episode transcripts |
| PDF pages | ~3,000 pages of dense text |

### Retrieval Accuracy

Gemini achieves approximately **99% accuracy** on single-needle retrieval tasks -- finding one specific piece of information buried somewhere in a massive context. This is impressive and reliable for most practical applications.

However, **multiple-needle retrieval** (finding several distinct pieces of information scattered throughout the context) sees accuracy degrade. When you need to extract multiple facts from a large context, use separate queries for each piece of information rather than asking for all of them in a single prompt.

```python

# LESS EFFECTIVE: Asking for multiple facts in one query
response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents=[
        large_document_content,
        "Find the CEO's name, the Q3 revenue figure, the number of "
        "employees, and the date of the next board meeting."
    ],
)

# MORE EFFECTIVE: Separate queries for each fact
queries = [
    "What is the CEO's name?",
    "What was the Q3 revenue figure?",
    "How many employees does the company have?",
    "When is the next board meeting?",
]

for query in queries:
    response = client.models.generate_content(
        model="gemini-2.5-pro",
        contents=[large_document_content, query],
    )
    print(f"Q: {query}\nA: {response.text}\n")
```

### Query Placement Is Critical

When working with long contexts, **always place your query at the END of the prompt**, after all context material. This is not a minor optimization -- it is a critical best practice that significantly affects response quality.

```python

# CORRECT: Query after context
response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents=[
        types.Content(role="user", parts=[
            types.Part(text=large_document),           # Context FIRST
            types.Part(text="Summarize the key findings."),  # Query LAST
        ]),
    ],
)

# INCORRECT: Query before context
response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents=[
        types.Content(role="user", parts=[
            types.Part(text="Summarize the key findings."),  # Query first -- BAD
            types.Part(text=large_document),                 # Context after -- BAD
        ]),
    ],
)
```

### Performance Characteristics

- **No significant quality degradation** with more relevant tokens -- adding more context does not confuse the model if the content is relevant
- **Higher latency** with longer contexts -- time-to-first-token increases proportionally with context length
- **Cost scales linearly** with token count -- budget accordingly for very large contexts

### Practical Recommendations

1. **Use caching** for repeated queries against the same large context (see the Context Caching section above)
2. **Break multi-needle tasks** into separate single-needle queries
3. **Put the query last** in the prompt, after all context
4. **Trim irrelevant content** when possible -- while the model handles large contexts well, smaller contexts are faster and cheaper
5. **Monitor latency** -- if time-to-first-token is a concern, consider whether the full context is necessary for every request

## URL Context

URL Context allows Gemini to fetch and analyze web page content directly during inference. The model retrieves the content from the specified URLs and incorporates it into its reasoning.

### How Retrieval Works

URL Context uses a 2-step retrieval process:

1. **Index cache check:** The system first checks if the URL content exists in an internal index cache
2. **Live fetch fallback:** If not cached, it performs a live HTTP fetch of the URL

This two-step process optimizes for both speed and freshness.

### Basic Usage

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Summarize the key points from this article: https://example.com/article",
    config=types.GenerateContentConfig(
        tools=[types.Tool(url_context=types.UrlContext())],
    ),
)

print(response.text)
```

### Analyzing Multiple URLs

You can include up to 20 URLs in a single request:

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="""Compare the approaches described in these three articles:
    1. https://example.com/approach-a
    2. https://example.com/approach-b
    3. https://example.com/approach-c

    Create a comparison table highlighting strengths and weaknesses of each.""",
    config=types.GenerateContentConfig(
        tools=[types.Tool(url_context=types.UrlContext())],
    ),
)
```

### Limits and Constraints

| Parameter | Limit |
|---|---|
| Maximum URLs per request | 20 |
| Maximum size per URL | 34 MB |
| Token counting | Retrieved content counts as input tokens |

### Supported Content Types

URL Context can process a wide range of formats:

- **Web content:** HTML, CSS, JavaScript
- **Data formats:** JSON, XML, CSV
- **Documents:** PDF files, plain text
- **Images:** Standard image formats (PNG, JPG, etc.)

### Unsupported Content Types

The following are explicitly **not supported**:

- Paywalled or login-gated content
- YouTube videos and transcripts
- Google Workspace documents (Docs, Sheets, Slides)
- Video and audio files

### Token Billing

All retrieved content counts as input tokens. If a URL returns a 50,000-token page, those tokens are billed at the standard input rate. Be mindful of this when fetching large pages, especially across multiple URLs in a single request.

## Structured Output / JSON Mode

Structured output forces Gemini to generate responses that conform to a specific JSON Schema. This is essential for building reliable pipelines where downstream code needs to parse the model's output programmatically.

### Defining Schemas with Pydantic (Python)

```python
from pydantic import BaseModel
from typing import List, Optional

class MovieReview(BaseModel):
    title: str
    year: int
    rating: float
    genres: List[str]
    summary: str
    pros: List[str]
    cons: List[str]
    recommended: bool

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Review the movie 'Inception' (2010)",
    config=types.GenerateContentConfig(
        response_mime_type="application/json",
        response_schema=MovieReview,
    ),
)

# The response is guaranteed to be valid JSON matching the schema
import json
review = json.loads(response.text)
print(f"Title: {review['title']}, Rating: {review['rating']}")
```

### Defining Schemas with Raw JSON Schema

```python
schema = {
    "type": "object",
    "properties": {
        "name": {"type": "string"},
        "age": {"type": "integer"},
        "skills": {
            "type": "array",
            "items": {"type": "string"}
        },
        "employed": {"type": "boolean"},
        "salary": {"type": ["number", "null"]}
    },
    "required": ["name", "age", "skills", "employed"]
}

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Create a profile for a senior Python developer",
    config=types.GenerateContentConfig(
        response_mime_type="application/json",
        response_schema=schema,
    ),
)
```

### Supported JSON Schema Types

| Type | Description |
|---|---|
| `string` | Text values |
| `number` | Floating-point numbers |
| `integer` | Whole numbers |
| `boolean` | True/false values |
| `object` | Nested objects with defined properties |
| `array` | Ordered lists of items |
| `null` | Null/empty values |

### Streaming with Structured Output

When streaming structured output, each chunk is a valid partial JSON string. Your application can begin parsing incrementally as chunks arrive.

### Compatibility with Other Tools

Structured output works alongside several other Gemini features:

- Google Search grounding
- URL Context
- Code Execution
- File Search (Gemini 3 models)

This means you can, for example, ground a query with Google Search and still receive the response in a structured JSON format.

## Code Execution

Code execution allows Gemini to write Python code, run it in a sandboxed environment, observe the output, and iterate on errors -- all within a single API call. This is powerful for tasks involving computation, data analysis, visualization, and algorithmic problem-solving.

### How It Works

1. The model analyzes your prompt and determines that code execution would help
2. It generates Python code
3. The code runs in a secure sandbox
4. The model inspects the output (or error)
5. If the code fails, the model can retry -- up to **5 retries** on failure
6. The final response incorporates the code's output

### Basic Usage

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Calculate the first 20 prime numbers and show them in a formatted table",
    config=types.GenerateContentConfig(
        tools=[types.Tool(code_execution=types.ToolCodeExecution())],
    ),
)

print(response.text)

# Access the executed code and its output
for part in response.candidates[0].content.parts:
    if part.executable_code:
        print(f"Code:\n{part.executable_code.code}")
    if part.code_execution_result:
        print(f"Output:\n{part.code_execution_result.output}")
```

### Runtime Constraints

| Constraint | Limit |
|---|---|
| Language | Python only |
| Execution timeout | 30 seconds per execution |
| Max retries on failure | 5 attempts |
| File input size | ~1-2 MB for text/CSV files |

### Available Libraries (40+)

The sandbox includes a rich set of pre-installed Python libraries:

| Category | Libraries |
|---|---|
| Data & Math | NumPy, Pandas, SciPy, SymPy |
| Visualization | Matplotlib, Seaborn, Plotly |
| Machine Learning | scikit-learn, TensorFlow, XGBoost |
| Text & NLP | NLTK, regex |
| Utilities | datetime, collections, itertools, json |

### Data Analysis Example

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="""Here is sales data in CSV format:

    month,revenue,costs,units_sold
    Jan,50000,30000,500
    Feb,55000,31000,520
    Mar,48000,29000,480
    Apr,62000,33000,600
    May,70000,35000,680
    Jun,65000,34000,650

    Calculate month-over-month growth rates, profit margins,
    and create a visualization showing revenue vs costs over time.""",
    config=types.GenerateContentConfig(
        tools=[types.Tool(code_execution=types.ToolCodeExecution())],
    ),
)
```

### Gemini 3 Flash: Code Execution with Images

Gemini 3 Flash extends code execution capabilities to include image manipulation. The model can:

- Crop and resize images
- Add annotations, labels, and bounding boxes
- Perform visual math and geometry
- Generate charts and plots from data

### Pricing

Code execution incurs **no additional charges** beyond standard token billing. The generated code, its output, and any retry attempts are all billed at standard input/output token rates.

## Safety Settings

Gemini provides configurable safety filters that control how the model handles potentially sensitive content. You can adjust these filters to match your application's requirements.

### Filter Categories

There are four adjustable safety filter categories:

| Category | What It Filters |
|---|---|
| **Harassment** | Negative or harmful comments targeting identity or protected attributes |
| **Hate Speech** | Rude, disrespectful, or profane content |
| **Sexually Explicit** | References to sexual acts or lewd content |
| **Dangerous** | Content that promotes, facilitates, or encourages harmful acts |

### Threshold Options

Each category can be set to one of the following thresholds:

| Threshold | Behavior |
|---|---|
| `OFF` | No filtering applied |
| `BLOCK_NONE` | Show all content regardless of probability |
| `BLOCK_ONLY_HIGH` | Block only when high probability of unsafe content |
| `BLOCK_MEDIUM_AND_ABOVE` | Block medium and high probability content |
| `BLOCK_LOW_AND_ABOVE` | Most restrictive -- blocks even low probability content |

**Default for Gemini 2.5 and Gemini 3:** `OFF`

### Configuring Safety Settings

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Explain the chemistry behind common household cleaning products and their interactions",
    config=types.GenerateContentConfig(
        safety_settings=[
            types.SafetySetting(
                category="HARM_CATEGORY_DANGEROUS_CONTENT",
                threshold="BLOCK_MEDIUM_AND_ABOVE",
            ),
            types.SafetySetting(
                category="HARM_CATEGORY_HARASSMENT",
                threshold="BLOCK_MEDIUM_AND_ABOVE",
            ),
            types.SafetySetting(
                category="HARM_CATEGORY_HATE_SPEECH",
                threshold="BLOCK_MEDIUM_AND_ABOVE",
            ),
            types.SafetySetting(
                category="HARM_CATEGORY_SEXUALLY_EXPLICIT",
                threshold="BLOCK_MEDIUM_AND_ABOVE",
            ),
        ],
    ),
)
```

### Handling Blocked Responses

When content is blocked by safety filters, the response will indicate which category triggered the block:

```python
if response.candidates[0].finish_reason == "SAFETY":
    for rating in response.candidates[0].safety_ratings:
        if rating.blocked:
            print(f"Blocked by: {rating.category} "
                  f"(probability: {rating.probability})")
```

### Recommendations

- For production applications serving end users, consider using `BLOCK_MEDIUM_AND_ABOVE` as a starting point
- For research or internal tools, lower thresholds may be appropriate
- Always test your safety settings with representative prompts before deploying
- Monitor blocked response rates to calibrate your thresholds

## Sampling Parameters

Sampling parameters control how the model selects tokens during generation. These settings directly affect the creativity, consistency, and length of responses.

### Temperature

Temperature controls the randomness of token selection. Higher values produce more diverse and creative outputs; lower values produce more focused and deterministic responses.

| Value | Behavior |
|---|---|
| `0.0` | Fully deterministic -- always picks the highest-probability token |
| `0.5` | Balanced -- moderate diversity |
| `1.0` | Default for Gemini 3 -- standard diversity |
| `1.5 - 2.0` | Highly creative -- more surprising and varied outputs |

```python

# Deterministic output (e.g., for classification tasks)
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Classify this email as spam or not spam: ...",
    config=types.GenerateContentConfig(
        temperature=0.0,
    ),
)

# Creative output (e.g., for brainstorming)
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Write a creative short story about a time-traveling chef",
    config=types.GenerateContentConfig(
        temperature=1.5,
    ),
)
```

**Important note for Gemini 3:** The default temperature is `1.0`. Google recommends **not changing this value** unless you have a specific reason to do so. The model is tuned to perform optimally at this setting.

### Top-K

Top-K limits the model to choosing from only the K highest-probability tokens at each step.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Complete this sentence: The future of AI is...",
    config=types.GenerateContentConfig(
        top_k=40,  # Consider only the top 40 tokens at each step
    ),
)
```

- **Lower values (e.g., 10):** More focused, less diverse
- **Higher values (e.g., 100):** More diverse, potentially less coherent

### Top-P (Nucleus Sampling)

Top-P sets a cumulative probability threshold. The model considers the smallest set of tokens whose cumulative probability exceeds the threshold.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Explain quantum entanglement to a 10-year-old",
    config=types.GenerateContentConfig(
        top_p=0.95,  # Consider tokens until 95% cumulative probability
    ),
)
```

- **Lower values (e.g., 0.5):** Very focused, selects from a narrow set of likely tokens
- **Higher values (e.g., 0.95):** Broader selection, more variety

### Max Output Tokens

Controls the maximum length of the generated response. This is a hard cap -- the model stops generating once this limit is reached.

```python

# Short, concise answer
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="What is photosynthesis?",
    config=types.GenerateContentConfig(
        max_output_tokens=100,  # Keep it brief
    ),
)

# Long-form content
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Write a detailed analysis of renewable energy trends",
    config=types.GenerateContentConfig(
        max_output_tokens=8192,  # Allow a lengthy response
    ),
)
```

### Stop Sequences

Stop sequences are strings that cause the model to immediately stop generating when encountered. This is useful for controlling output boundaries.

```python
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Write a haiku about autumn:",
    config=types.GenerateContentConfig(
        stop_sequences=["\n\n", "---"],  # Stop at double newline or separator
    ),
)
```

### Combining Parameters

In practice, you often combine multiple sampling parameters:

```python
config = types.GenerateContentConfig(
    temperature=0.7,
    top_k=40,
    top_p=0.95,
    max_output_tokens=2048,
    stop_sequences=["END"],
)

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Generate a product description for a smart water bottle",
    config=config,
)
```

### Parameter Selection Guide

| Task Type | Temperature | Top-K | Top-P | Max Tokens |
|---|---|---|---|---|
| Classification / extraction | 0.0 | 1 | 1.0 | Varies |
| Summarization | 0.3 - 0.5 | 40 | 0.90 | Varies |
| General Q&A | 0.7 - 1.0 | 40 | 0.95 | Varies |
| Creative writing | 1.0 - 1.5 | 80+ | 0.95 | High |
| Brainstorming | 1.5 - 2.0 | 100 | 0.99 | High |

Keep in mind that these are starting points. The optimal settings for your specific application should be determined through experimentation and evaluation.

## Chapter Summary

The advanced features covered in this chapter dramatically expand what you can build with Gemini:

- **Grounding with Google Search** connects the model to real-time web information with citation support
- **Grounding with Google Maps** adds location-aware intelligence for geographic queries
- **Context Caching** (both implicit and explicit) reduces costs by up to 90% on input tokens for repeated contexts
- **Long Context** support handles up to 1M+ tokens, but requires careful query placement and single-needle retrieval strategies
- **URL Context** fetches and analyzes up to 20 web pages directly during inference
- **Structured Output** enforces JSON Schema compliance for reliable programmatic consumption
- **Code Execution** lets the model write, run, and iterate on Python code with 40+ libraries
- **Safety Settings** provide granular control over content filtering across four categories
- **Sampling Parameters** (temperature, top-k, top-p, max tokens, stop sequences) give you precise control over output characteristics

Mastering these features is what separates basic prompt engineering from building production-grade applications with Gemini. Each feature addresses a specific limitation of standard prompt-response interactions, and combining them unlocks capabilities far beyond what any single feature provides alone.

# Chapter 10: Agentic Patterns & Google ADK

As Gemini models become more capable at reasoning, tool use, and multi-turn interaction, a natural progression emerges: using these models not just for single-turn question answering, but as the core reasoning engine inside autonomous agents. Agentic systems combine LLM reasoning with tool execution, memory management, and goal-directed planning to accomplish complex tasks that span multiple steps and interactions. This chapter covers the design principles for agentic systems, the Google Agent Development Kit (ADK), communication protocols, and practical patterns for building production-grade agents with Gemini.

## Agent Design Dimensions

Effective agent design requires thinking across four key dimensions. Each dimension represents a design decision that shapes your agent's capabilities and limitations.

### Reasoning

The reasoning dimension determines how the agent thinks about problems. This includes the choice of model, thinking configuration, and prompting strategy.

- **Model selection**: Match model capability to task complexity. Use 2.5 Flash or 3 Flash for fast reasoning loops; escalate to 2.5 Pro or 3.1 Pro for complex planning.
- **Thinking level**: Agents benefit from higher thinking levels because each step involves deciding what to do next, which is inherently a reasoning task.
- **Planning vs reacting**: Some agents plan ahead (generating a full action plan before executing), while others react step-by-step (deciding the next action based on the latest observation). The right approach depends on task predictability.

### Execution

The execution dimension covers how the agent takes actions in the world. This typically involves function calling, code execution, or computer use.

- **Tool repertoire**: Define the set of tools the agent can use. Each tool should be well-described with clear parameter schemas.
- **Error handling**: Agents must handle tool failures gracefully -- retrying, using alternative tools, or asking the user for help.
- **Safety boundaries**: Define hard limits on what the agent can do. Some actions (deleting data, sending emails, making purchases) should require explicit confirmation.

### Interaction

The interaction dimension defines how the agent communicates with users and other agents.

- **Turn structure**: Single-turn (one request, one response) vs multi-turn (ongoing conversation with memory).
- **Proactivity**: Does the agent wait for instructions, or does it proactively suggest actions?
- **Clarification**: When faced with ambiguous requests, the agent should ask clarifying questions rather than making assumptions.

### Information

The information dimension covers how the agent accesses and manages knowledge.

- **Context window management**: For long-running agents, the context window can fill up. Strategies include summarization, RAG, and context caching.
- **Memory**: Short-term (within a conversation) vs long-term (across conversations) memory systems.
- **Grounding**: Connecting the agent to real-time data sources via Google Search, URL Context, or custom data pipelines.

## Agentic System Prompt Template

A well-structured system prompt is the foundation of an effective agent. Use XML structure to organize the agent's identity, constraints, context, and task.

```xml
<system_instruction>
  <role>
    You are a DevOps agent specialized in Kubernetes cluster management.
    You have access to kubectl commands, monitoring dashboards, and
    alerting systems. You make careful, reversible changes and always
    verify the cluster state before and after modifications.
  </role>

  <constraints>
    - NEVER delete namespaces without explicit user confirmation
    - NEVER modify production deployments without a rollback plan
    - Always check current resource utilization before scaling
    - Log every action taken for audit purposes
    - If uncertain about an action's impact, explain the risk and ask
      the user to decide
  </constraints>

  <context>
    You are managing a Kubernetes cluster running 15 microservices.
    The cluster uses Istio for service mesh, Prometheus for monitoring,
    and ArgoCD for GitOps deployments. Current environment: staging.
  </context>

  <task>
    Respond to operational requests by:
    1. Assessing the current state using available tools
    2. Planning the necessary changes
    3. Executing changes incrementally with verification at each step
    4. Reporting the final state and any follow-up actions needed
  </task>
</system_instruction>
```

## Google ADK (Agent Development Kit)

Google ADK is an open-source framework for building, deploying, and orchestrating AI agents. It provides structured abstractions for common agent patterns and integrates natively with Gemini models.

### Language Support

ADK is available in **Python**, **TypeScript**, **Go**, and **Java**, making it accessible to teams regardless of their primary language.

### ADK Agent Types

ADK provides several built-in agent types, each designed for a different orchestration pattern.

| Agent Type | Description | Use Case |
|-----------|-------------|----------|
| **LLM Agent** | A single agent backed by a Gemini model with tools. The most basic building block. | Single-purpose tasks, simple Q&A with tool access |
| **Sequential Workflow** | Executes a chain of agents in a fixed order. Output of each agent flows to the next. | Data pipelines, document processing, staged analysis |
| **Loop Workflow** | Repeatedly executes an agent until a termination condition is met. | Iterative refinement, retry logic, convergence tasks |
| **Parallel Workflow** | Executes multiple agents simultaneously and aggregates results. | Multi-source research, ensemble analysis, independent subtasks |
| **Custom Agent** | User-defined orchestration logic. Full control over agent coordination. | Complex business logic, conditional routing, hybrid patterns |
| **Multi-Agent** | Multiple specialized agents coordinated by a supervisor agent. | Complex workflows requiring diverse expertise |

### Basic ADK Agent Example

```python
from google.adk import Agent, Tool
from google import genai

# Define tools
def get_cluster_status(namespace: str = "default") -> dict:
    """Get the current status of pods in a Kubernetes namespace.

    Args:
        namespace: The Kubernetes namespace to check.

    Returns:
        A dictionary with pod names, statuses, and resource usage.
    """
    # Implementation here
    return {"pods": [...], "status": "healthy"}

def scale_deployment(name: str, replicas: int) -> dict:
    """Scale a Kubernetes deployment to a specified number of replicas.

    Args:
        name: The deployment name.
        replicas: The desired number of replicas.

    Returns:
        Confirmation of the scaling operation.
    """
    # Implementation here
    return {"deployment": name, "replicas": replicas, "status": "scaled"}

# Create an ADK agent
agent = Agent(
    model="gemini-2.5-flash",
    name="k8s-ops-agent",
    description="Manages Kubernetes cluster operations",
    tools=[get_cluster_status, scale_deployment],
    system_instruction="You are a Kubernetes operations agent...",
)

# Run the agent
response = agent.run("Check the status of the payments namespace "
                     "and scale it to 5 replicas if it's healthy.")
```

### Sequential Workflow Example

```python
from google.adk import Agent, SequentialWorkflow

# Stage 1: Research agent
researcher = Agent(
    model="gemini-2.5-flash",
    name="researcher",
    tools=[google_search_tool],
    system_instruction="Find and summarize relevant information.",
)

# Stage 2: Analysis agent
analyst = Agent(
    model="gemini-2.5-pro",
    name="analyst",
    system_instruction="Analyze the research findings and identify key insights.",
)

# Stage 3: Writer agent
writer = Agent(
    model="gemini-2.5-flash",
    name="writer",
    system_instruction="Write a clear, actionable report based on the analysis.",
)

# Chain them together
workflow = SequentialWorkflow(
    agents=[researcher, analyst, writer],
    name="research-report-pipeline",
)

report = workflow.run("Analyze the current state of edge computing in healthcare.")
```

## A2A Protocol

The **Agent-to-Agent (A2A) protocol** is an open standard developed by Google for inter-agent communication. It defines how agents discover each other's capabilities, exchange messages, and coordinate on tasks.

### Key Concepts

- **Agent Cards**: JSON documents that describe an agent's capabilities, accepted input formats, and available skills. Analogous to API documentation for agents.
- **Task lifecycle**: A2A defines a standard task lifecycle (submitted, in-progress, completed, failed) that enables agents to track and manage work delegated to other agents.
- **Transport-agnostic**: A2A works over HTTP, WebSockets, or other transports.

### Why A2A Matters

In multi-agent systems, agents built by different teams (or different organizations) need a common protocol to interact. A2A provides this standardization, preventing vendor lock-in and enabling interoperability.

## ReAct Pattern

The ReAct (Reasoning + Acting) pattern is the most widely used agentic loop. It alternates between reasoning about what to do and taking actions, using observations from each action to inform the next step.

### The Loop

```
Observe -> Think -> Act -> Observe -> Think -> Act -> ... -> Final Answer
```

1. **Observe**: The agent receives new information (user input, tool output, error message).
2. **Think**: The agent reasons about the observation and decides the next action.
3. **Act**: The agent executes a tool call or generates a response.
4. **Repeat**: The cycle continues until the task is complete.

### Implementation

```python
from google import genai
from google.genai import types

client = genai.Client()

def react_agent(user_query: str, tools: list, max_iterations: int = 10):
    """A simple ReAct agent loop."""
    messages = [
        types.Content(role="user", parts=[
            types.Part.from_text(user_query)
        ]),
    ]

    for i in range(max_iterations):
        # Think + Act: Let the model decide what to do
        response = client.models.generate_content(
            model="gemini-2.5-flash",
            contents=messages,
            config=types.GenerateContentConfig(
                tools=tools,
                thinking_config=types.ThinkingConfig(thinking_budget=4096),
            ),
        )

        candidate = response.candidates[0]

        # Check if the model produced a final text answer
        if candidate.finish_reason == "STOP":
            return response.text

        # Extract and execute function calls
        model_content = candidate.content
        messages.append(model_content)

        function_responses = []
        for part in model_content.parts:
            if part.function_call:
                result = execute_function(part.function_call)
                function_responses.append(
                    types.Part.from_function_response(
                        types.FunctionResponse(
                            name=part.function_call.name,
                            response=result,
                        )
                    )
                )

        # Observe: Add function results to the conversation
        messages.append(types.Content(
            role="user",
            parts=function_responses,
        ))

    return "Max iterations reached without a final answer."
```

## Computer Use for Agents

Computer Use enables agents to interact with graphical user interfaces through a screenshot-and-action loop. This is particularly powerful for automating tasks that do not have API equivalents.

### The 4-Step Loop

1. **Capture**: Take a screenshot of the current screen state.
2. **Observe**: Send the screenshot to the model for analysis.
3. **Decide**: The model returns a structured action (click, type, scroll, etc.).
4. **Execute**: Your infrastructure performs the action on the actual screen.

### Normalized Coordinates

Computer Use actions use **normalized coordinates in the 0-999 range** rather than pixel coordinates. This makes actions resolution-independent.

```python

# The model returns actions like:
action = {
    "type": "click",
    "x": 450,  # Normalized 0-999
    "y": 320,  # Normalized 0-999
    "button": "left",
}

# Convert to actual pixel coordinates
def to_pixels(normalized_x, normalized_y, screen_width, screen_height):
    pixel_x = int(normalized_x * screen_width / 999)
    pixel_y = int(normalized_y * screen_height / 999)
    return pixel_x, pixel_y
```

### Safety Decisions

Computer Use requires careful safety boundaries:

- **Never automate login flows** with real credentials exposed to the model.
- **Always confirm** before actions that modify data, submit forms, or make purchases.
- **Restrict the browsable domain** to prevent the agent from navigating to unintended sites.
- **Log all actions** for audit and debugging purposes.

## Multi-Context Window Strategies

Long-running agents often exceed the context window limit. Several strategies address this challenge.

### Split Context

Divide the task across multiple agents, each with its own context window. A coordinator agent manages the overall flow and passes relevant summaries between sub-agents.

### Caching

Use Gemini's context caching to store large reference documents and reuse them across multiple agent turns without re-processing.

```python

# Cache the agent's reference documentation
cache = client.caches.create(
    model="gemini-2.5-flash",
    config=types.CreateCachedContentConfig(
        display_name="agent-reference-docs",
        contents=[
            types.Content(role="user", parts=[
                types.Part(text=reference_documentation)
            ])
        ],
        ttl="3600s",
    ),
)

# Use the cache in each agent turn
response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents=current_turn_messages,
    config=types.GenerateContentConfig(
        cached_content=cache.name,
        tools=agent_tools,
    ),
)
```

### RAG (Retrieval-Augmented Generation)

Instead of keeping everything in context, store information externally (in a vector database) and retrieve only the relevant portions for each turn.

### Summarization Chains

Periodically summarize the conversation history and replace the full history with the summary. This compresses context while preserving essential information.

```python
def compress_history(messages: list, client, model: str) -> list:
    """Summarize old messages to free context space."""
    if len(messages) < 20:
        return messages

    # Keep the last 10 messages intact
    recent = messages[-10:]
    old = messages[:-10]

    # Summarize the old messages
    summary_response = client.models.generate_content(
        model=model,
        contents=[
            types.Content(role="user", parts=[
                types.Part.from_text(
                    "Summarize the following conversation history, preserving "
                    "all key decisions, action results, and pending tasks:\n\n"
                    + format_messages(old)
                )
            ]),
        ],
    )

    # Replace old messages with summary
    summary_message = types.Content(role="user", parts=[
        types.Part.from_text(f"[Conversation summary]: {summary_response.text}")
    ])

    return [summary_message] + recent
```

## Deployment Options

ADK agents can be deployed across multiple platforms depending on your scale and infrastructure requirements.

| Platform | Best For | Key Features |
|----------|----------|-------------|
| **Local development** | Testing and iteration | Hot reload, debug logging, minimal setup |
| **Vertex AI Agent Engine** | Managed production deployment | Auto-scaling, monitoring, Google Cloud integration |
| **Cloud Run** | Containerized serverless deployment | Pay-per-use, automatic scaling, custom runtime |
| **GKE (Google Kubernetes Engine)** | Large-scale, custom infrastructure | Full control, multi-agent orchestration, GPU access |

### Local Development

```bash

# Install ADK
pip install google-adk

# Run locally with debug logging
adk run --agent my_agent.py --debug
```

### Cloud Run Deployment

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["adk", "serve", "--agent", "my_agent.py", "--port", "8080"]
```

## Building Effective Agents: Summary of Best Practices

### 1. Precise Instructions

Write agent system prompts with surgical precision. Every ambiguity in the instructions is an opportunity for the agent to go off track. Specify exactly what the agent should do, in what order, and under what conditions.

### 2. Split-Step Verification

After each tool call, have the agent verify the result before proceeding. A simple "check your work" instruction in the system prompt significantly reduces cascading errors.

```
After each action, verify the result. If the outcome does not match
expectations, diagnose the issue before proceeding. Do not assume
success -- confirm it.
```

### 3. Constraint Organization

Organize constraints hierarchically: hard constraints (never violated), soft constraints (preferred but flexible), and guidelines (best effort).

```xml
<constraints>
  <hard>
    - Never delete production data
    - Never expose credentials in logs
    - Always require confirmation for destructive actions
  </hard>
  <soft>
    - Prefer the most recent API version
    - Prefer JSON over XML for structured output
  </soft>
  <guidelines>
    - Explain your reasoning when making non-obvious choices
    - Suggest improvements when you notice suboptimal patterns
  </guidelines>
</constraints>
```

### 4. Grounding

Connect agents to real-time data through Google Search, URL Context, or custom data sources. Agents that rely solely on training data become stale and unreliable for time-sensitive tasks.

### 5. Thinking Mode

Enable thinking mode for agents. Agent reasoning (what tool to call, what parameters to use, whether the task is complete) is inherently a multi-step cognitive task that benefits from internal reasoning.

For Gemini 3, use `thinkingLevel: medium` or `high`. For Gemini 2.5, use `thinking_budget: -1` (dynamic) or a budget of at least 4096.

### 6. Tool Integration

Follow all the tool design best practices from Chapter 7: descriptive names, detailed descriptions, strong typing with enums, and 10-20 tools maximum per request. For agents with larger tool sets, implement dynamic tool selection.

### 7. Safety

Implement defense in depth:
- **Model-level**: Use system instructions to define boundaries.
- **Application-level**: Validate tool call parameters before execution.
- **Infrastructure-level**: Use sandboxed environments, network restrictions, and audit logging.
- **Human-in-the-loop**: Require confirmation for high-impact actions.

Agents are powerful precisely because they can take autonomous action. This power demands proportional caution in design and deployment.