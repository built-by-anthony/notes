# MSO Foundations

**MSO:**

- **m**odel hevaior and technical substrates 
- **S**ystem/Model reasoning
- **O**perational/Prompting modes

## Orientation 

### What you will be able to do by the end 

This module introduces the model fundamental and technical foundations that the rest of the Developer course assumes you already have. 

#### By the end of this module, you will ne able to: 

1. Explain what a token is, how the context window works as a fixed budget, why sampling makes outputs vary, and what non-determinism means for testing and evals. 

2. Describe the Claude model family and its capability tiers, and distunguishing choosing a model from enabling a reasoning model such as extended thinking. 

3. Choose between zero-shot, one-shot, and multiple-shot prompting, and weight hecost and quality trade-off of adding examples. 

4. Describe how a developer accesses Claude: SDK versus raw REST, synchronous versus s treaming responses, and asynchronous pattersn for high-volume work. 

## How LLMs Behave 

### How LLMs behave: tokens, context, sampling, non-determinism 

#### Tokens: the unit of input, output, and cost

- Claude reads **tokens**, and the characteristics-per-token depends on the tokenizer of hte model at hand and differs between model generations. 

- Treat any chars-per-token rule of thumb ad model-dependent and confirm current tokenizer behavior at build time. 

- Everything the model processes is counted in tokens. 

- Tokens are the unit of both pricing and budget, so when you estimate what a feature costs or whether an input fits, you are counting tokens, not words. 

- A useful habit is to think in tokens, since that is the unit the API bills in and the context window measures. 

#### The context window: a fixed budget 

The **Context window** - is the total number of tokens the model can take in for a single request. 

- It holds everything at once: 
    - The system prompt
    - The full conversation so far 
    - any documents you inject 
    - every tool result
    - and the model output

- It is a fixed budget with two distinct edge behaviors: 
    - A request whose input is already larger than the window is rejected with a validation error before generation begins 
    - A request that fits on input can still reach the ceiling during generation. Current models then stop and return the output generated so far with a `model_context_window_exceeded` stop reason rather than raising an error. 

Either way, keeping a long session running rquires the application to trim or summarize history befoer each call. 

In development, the window rarely fills becuase test inputs are short. In production, on the other hand, longer inputs and more turns fill the window faster. 

#### Sampling: why the same prompt can give different answers

A language model does not pick one fixed next token. At each step it produces a probability distribution over possible next tokens and then **samples** from it. 

Settings such as *temperature*, shape that distribution: a lowerw temperature concentrates probablility on the most likely tokens and makes output more repetable, while a higher temperature spreads it out and makes output more varied. 

**Temperature** - A setting that shapes the probability distribution the model samples from. 
    - A lower temperature concentrates the probability on the most likely tokens and makes output more repeatable; a higher temperature spreads it out and makes output more varied. 

> **Note**: sampling controls are model dependent. The newest Claude models do not accept non-default samlpling parameters. Setting temperature, `top_p`, or `top_k` returns a `400` error, and behavior on those models is steered through prompting instead. Even where temperature is accepted, temperature 0 makes outputs more repeatable but does not guarantee identical output across calls. Confirm current parameter support in the API reference at build time. 

#### Non-determinism: what it means for testing and evals 

**Non-determinism** is the primary consequence of sampling: ***identical inputs do not guaranteed identical outputs***.

That changes how you test a Claude feature. A test that asserts the exact text of a response will be inconsistent, because the model can express the same correct answer many ways. Instead, assert on hte property that must hold: a required field is present, a value is in range, the structure parses. 

When you need to judge meaning rather than structure, use an ***eval with a model-graded judge***. This is why the course treats evals as the standard for for knowing a feature is correct, and thats why Module 3 builds that capability. 

**eval with a model-graded judge**: An evaluation that uses a model to judge whether a response's meaning meets a rubric, used when you need to judge meaning rather than structure.

## Models & Reasoning 

### Model options and reasoning mode

#### The Claude model family 

Claude is a family ofm odels that current spans four tiers, each model represents a different tradeoffs across cost, latency, and capability 

- **Fable** - the most capable tier, for the most demanding reasomning, coding, and agentic work where maximum intelligence is the priority. 
- **Opus** - handles demanding work above the Sonnet envelope.
- **Sonnet** - the balanced default for most production workloads. 
- **Haiku** - built for speedand cost efficiency on tasks that fit its capacity envelope.

The practical default is to start with Sonnet, move up a tier only when an eval shows the current tier missing your quality bar, and move down to Haiku only when an eval shows the quality drop is acceptable for the task. 

#### Reasoning modes are a separate setting from model choice 

Choosing which model to run is one decision. WHtehr the model reasons before answering is a separate decision you make per call. 

On current models the reasoning mode is adaptive thinking: the model decides when and how much to think, and you tune depth with an effort setting rather than a fixed token budget. Thinking content is omitted from responses be default on the newest models.

Request summarized display when you need to show it. Reasoning earns its cost on hard, multi-step problems and is wasted on lookups and classification. 

They key point for this module is that the two levers compose: model choice picks the family member, while the reasoning mod e is onc figured per request. 

Per-model deafults differ (some of the newest models think adaptively by default or always), so confirm the current thinking defaults for your model at build time. 

#### How the two work together 

Becuase model choice and reasoning mode are independent, each can be set separately. A capable model with reasoning off is fast and direct, while a smarter model with reasoning on spends more tokens to think. 

The most demanding tasks pair a capable model with a higher effort setting. Module 2 teaches the mechanics of enabling reasoning and handling the thinking blocks it returns. 

The decision of which model to run, weighed against cost, latency, and quality, is taken up in Module 4.

## Prompting modes 

### Prompting modes: zero-shot, one-shot, multi-shot 

#### The three modes 

Seperate form how you word a prompt, is how many worked examples you give the model inside it. 

- **Zero-shot** gives the instructions and no examples: you decsribe the task and ask for thr result. 
- **One-shot** adds one example of the input paired with the desired output. 
- **Multi-shot** also called **few-shot**, includes several such examples. 

The examples are not training data; they sit in the prompt and show the model the exact shape of the answer you want, which a description alone often fails to pin down. 

#### The cost and quality trade-off 

Each example you add costs tokens on every call and consumes context budget, so the choice trades quality against cost. 

Reach for zero-shot when the task is simple and the output shape is obvious. 

Move to one-shot or multi-shot when the output has a specific structure, casing, or edge case the a description keeps missing. Often one or two correct examples usually fix the issue faster than another paragraph of instructions. 

The general discipline, which Module 2 reinforces, is to adsd the smallest amount of prompt that produces a reliable result. 

#### Mode choice interacts with model choice

Prompting mode nad model choice are related levers. A more capable model often succeeds zero-shot on task where a smaller model needs a few examples to match the structure, so adding examples can let a cheaper model do the job. 

The two decisions are worth making together: try the simplest model and teh fewest examples that meet your eval, and add capability or examples only where the eval says you need them. 

## Techincal Substrate 

### The technical substrate: SDKs, REST, streaming, async

#### How a developer reaches Claude: SDK versus raw REST

At its core, Claude is reached over an HTTP REST API: your code sends a request to an endpoint with your API key and a JSON body, and reads a JSON response back. You can call that endpoint directly with any HTTP client.

More commonly you use an official SDK available for Python and TypeScript among others, which is a thin convenience layer over the same REST API. It handles authentication, request construction, retries, and response parsing so you write less boilerplate. 

The SDK saves you from assembling requests by hand,. Module 2 builds against the SDK and the Messages API, which sits on this same foundation. 

#### Synchronous, streaming, and real-time responses 

A **synchronous** request is the simplest pattern: you send the request and wait for the complete response to come back in once piece, then act on it. That is fine for short responses and backend jobs where no one is waiting.

WHen a response is long or a user is watching, **streaming** sends the response in pieces as the model generates it. Output appears immediately rather than after a blank-screen wait, and your code reassembles the pieces into the final message. 

Claude exposes streaming over the same HTTP connection using server-sent events. Module 2 teaches how to consume a stream safely and recover when it is interrupted. 

#### Asynchronous patterns for high-volume work 

Two apatterns address high-volume work, and they solve different problems. 

The Python SDK exposes an async client (AsyncAnthropic) that uses non-blocking async/await to make API calls without tying up your application thread. in the TypeScript SDK the standard Anthropic client is Promise-based, so you await calls directly. THere is nosearate async client class. Either way the request still returns in real time, but your application can handle other work while it waits. This is the right pattern when you need concurrency without blocking.

**AsyncAnthropic** - The async client exposed by the Python SDK. Uses non-blocking async/await to make API calls without tying up your application thread while the request returns in real time.

The **Message Batches API** is a separate pattern for bulk offline workloads. You submit a large set of request in one call, receive an identifier, and poll for completion. Batch jobs can take up to 24 hours to complete nad run at a lower per-token cost in exchange for that latency. This suits offline pipelines, evaluation runs, and bulk jobs where nou ser is waiting on each result and cost matters more than turnaround time. 

**Message Batches API** - A separate pattern for bulk offline workloads: submit a large set of requests in one call, receive an identifier, and poll for completion. Can t ake up to 24 hours and runs a lower per-token cost. 

## Recap: five takeaways 

1. **Tokens are the uynit of input, output, and cost** - Think and buget in tokens rather than words, since that is what the API meters and the context window measures. 

3. **The context window is a fixed token budget that holds the whole request at once** - An oversidzed input errors before generation, while hitting the ceiling mid-generation returns truncated output with a `model_context_window_exceeded` stop reason, so managing history is the application's job.

4. **Model choice and reasoning mode are separate composable layers** - Pick the smallest model and the simplest reasoning and prompting that meet your eval and add capability only where the eval says you need it. 

5. **A developer reaches Claude over a REST API, usually through an SDK.** - Choose bewteen synchronous, streaming, async/await, or batch based on whether a use is waiting and whether the workload is real-time of bulk offline.

