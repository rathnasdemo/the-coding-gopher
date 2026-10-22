---
schemaId: urn:gala:schema:content-frontmatter:2.0.0
schemaVersion: 2.0.0
id: 01a16494-323f-752e-9e5b-ed11919a2408
kind: article
title: Model Context Protocol (MCP) Explained
description: I walk through how LLMs went from static text predictors to in-context learning, RAG and tool-using agents, and why Anthropic's Model Context Protocol, with its host, client and server architecture, standardizes how models reach tools and data.
language: en-US
authors:
- 01a16498-1128-725f-af97-840ef616dfdd
tags:
- model-context-protocol
- llms
- ai-agents
- retrieval-augmented-generation
- anthropic
status: published
createdAt: '2025-05-29T12:00:00.000Z'
publishedAt: '2025-05-29T12:00:00.000Z'
slug: model-context-protocol-mcp-explained
hero:
  path: assets/content-model-context-protocol-mcp-explained-hero.jpg
  alt: Thumbnail of the video "Model Context Protocol (MCP) Explained"
  role: informative
redirects: []
extensions: {}
---
*Adapted from the video [Model Context Protocol (MCP) Explained](https://www.youtube.com/watch?v=rCBSQxQr9Xg)*

99% of developers don't get MCP. Every time I hit up my favorite cafe to get some serious coding done, all I see are screens filled with people vibe coding or interrogating ChatGPT. If you haven't used ChatGPT yet, you're probably either senile or living under a digital rock.

But I'm telling you right now that there is a huge leap between your run-of-the-mill vanilla ChatGPT and what I'm about to show you. Today, my friends, I'm going to take you on an insane journey from that no-context ChatGPT to ChatGPT on pure, unadulterated steroids, using Model Context Protocol, or MCP.

## From static text predictors to in-context learning

*[Watch from 0:34](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=34s)*

Large language models have undergone a profound transformation in recent years. They were initially conceived as text prediction engines trained on large corpora. Models such as GPT-2 and early GPT-3 were primarily used for generating humanlike text responses from a given prompt.

These models were inherently static. They could not update their knowledge in real time or interact with external environments. Their utility was bound by their training data and limited to the content that could be embedded in a fixed-length prompt. Although they did impress users with coherent output, they were fundamentally constrained in terms of up-to-date knowledge and dynamic adaptability.

To partially mitigate these limitations, developers introduced in-context learning, which involved stuffing the model's context window with task-specific information, examples and instructions. This let the model perform zero-shot or few-shot tasks better than before, essentially by tricking it into thinking the input prompt was part of its training distribution.

While this improved accuracy for many tasks, it still lacked scalability and modularity. These models could not autonomously seek additional context beyond what was hard-coded into the prompt, nor could they execute tasks or retrieve external knowledge dynamically.

## Retrieval augmented generation, and why it was read-only

*[Watch from 1:41](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=101s)*

This led to the rise of retrieval augmented generation, or RAG, a framework that combined LLMs with information retrieval systems. RAG allowed the model to retrieve relevant documents from external knowledge bases, typically via semantic search using vector databases, and then incorporate that information into the generation process.

This architecture helped bridge the knowledge gap between model pre-training and current information. It effectively became a staple for building AI applications that needed domain-specific or up-to-date information.

However, the big issue here is that RAG was fundamentally read-only. The model could observe data but could not interact with it. It could not perform transactions, query APIs or affect stateful systems in the world.

## Tool-augmented agents and the arrival of MCP

*[Watch from 2:21](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=141s)*

The next leap came with the emergence of tool-augmented agents, which allowed LLMs to go beyond passive retrieval and into action execution. This approach lets an LLM invoke external APIs, databases or other software tools dynamically during a conversation. However, this capability required new infrastructure and design patterns to ensure consistency, security and developer ergonomics.

And that is where the Model Context Protocol, or MCP, comes in. It's a groundbreaking architecture developed by Anthropic, the company that built Claude, and it's increasingly adopted in the AI community to formalize the interface between LLMs and tools.

What MCP does is introduce a standardized JSON-RPC-based protocol that allows LLMs, or clients, to communicate with external functions or systems, like servers, through a host application. Each tool exposes a manifest describing its available methods, parameters, expected input and output, and optional authentication schemes like OAuth 2.0.

When the LLM determines a need for an action, it can compose and send a structured request to an external tool, await the response, and incorporate it into its ongoing task. This fundamentally turns the LLM into a reasoning engine and orchestrator capable of performing multi-step operations like database queries, code execution or even user-specific recommendations.

The client LLM doesn't access raw secrets or internal infra. It merely uses defined methods via the protocol. Models no longer rely solely on pre-training or retrieval, but can dynamically extend their abilities through secure, pluggable interfaces. This solves a couple of critical pain points:

- Knowledge staleness
- Limited context
- The inability to act

## Context as simulated memory

*[Watch from 5:14](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=314s)*

There's this very core idea of context as simulated memory. LLMs like GPT-4 are stateless at their core. They have no memory between runs unless we explicitly give them one. Every time you send a prompt, the model processes it from scratch, without any inherent awareness of past interactions, unless those are included again in the prompt itself.

This means that any form of intelligence we simulate over multiple interactions, whether it's keeping track of a conversation, responding consistently to preferences, or synthesizing knowledge from long documents, depends entirely on what we include in the model's input window at inference time. This input is referred to as the context.

## The M×N integration problem and the USB-C analogy

*[Watch from 5:47](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=347s)*

Before MCP, integrating LLMs with external systems was a complex endeavor. Each new tool or data source required custom connectors, leading to a proliferation of bespoke solutions that were difficult to maintain and scale. This M×N integration problem meant that for every combination of model and tool, a unique integration had to be developed, resulting in significant engineering overhead and potential for errors.

MCP is an open protocol designed by Anthropic that standardizes how applications provide context to LLMs. Think of MCP like a USB-C port for AI applications. Just as USB-C provides a standardized way to connect your devices to various peripherals and accessories, MCP provides a standardized way to connect AI models to different data sources and tools.

So it enables these LLMs to interact with external systems like tools, data and APIs in a structured and declarative manner. MCP abstracts the interface between the LLM and its tool-use context. It allows models to request capabilities or data they don't have internally, while enabling tool providers to expose their capabilities safely and scalably.

## The MCP host

*[Watch from 6:53](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=413s)*

Now let's look at the high-level architecture. MCP defines a three-part architecture: the host, the client and the server.

The first is the MCP host. This is the container environment running the LLM or integrating it into a larger software system. Think of it as the orchestrator. It manages the life cycle of clients, routes requests between the LLM and the outside world, enforces permission scopes and security policies, and so on. In technical terms, it is often tightly coupled to the model runtime and responsible for launching and coordinating MCP clients.

## MCP clients and their two transports

*[Watch from 7:25](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=445s)*

Each MCP client is a translation layer that enables the LLM, via the host, to interact with an external system in a normalized, schema-driven way. It translates LLM intents, often JSON requests, into structured calls to the underlying MCP server. It handles session and connection management, interprets the model's requests into actions, and returns server responses in a usable format.

Clients maintain one-to-one connections with MCP servers and use either standard IO for local tool access, or HTTP and server-sent events (SSE) for remote or cloud-based resources.

- **Standard input/output (standard IO).** This is used for local tools or CLI-style utilities. The client and server run as subprocesses on the same machine, and data is piped in and out via standard input and standard output. Think of a client that wraps grep, or a local Python script. It's very fast, with no network latency, it's simple to implement, and it's great for local dev tools or sandboxed plugins.
- **HTTP and server-sent events (SSE).** This is used for remote or network tools, often in cloud or containerized environments. The client sends HTTP POST requests to invoke actions or fetch resources, and the server streams responses back using SSE.

So why SSE? It allows incremental or streaming results, for example a log tail, a partial response or an async job status. It also enables long-lived interactions without blocking, like raw HTTP would. For example, the model asks the GitHub tool, via its client, to fetch PR comments. The client sends an HTTP POST, and the server sends an SSE stream back with the comments.

## The MCP server

*[Watch from 8:56](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=536s)*

Next we have the MCP server. This is the external tool or service the model is trying to access. The server implements the MCP spec. It exposes a set of capabilities using structured JSON schemas for:

- **Resources**, which are retrievable objects like files or documents
- **Tools**, which perform actions or make changes
- **Prompts**, which are reusable LLM call templates

Each server provides an introspect endpoint that describes its interface, allowing the host or model to query and reason about what it can do.

## Declarative and self-describing by design

*[Watch from 9:26](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=566s)*

There are some important protocol design principles at play here. First, it's declarative and self-describing. MCP servers are introspectable: they declare what resources, tools and prompts they expose, and this enables dynamic discovery and adaptive reasoning by the model. This introspection model is similar to OpenAPI for APIs or GraphQL's introspection system.

In practice, when the model uses MCP, it:

1. Discovers the tools dynamically via introspection.
2. Requests operations in structured formats.
3. Processes the responses from tools as part of its reasoning loop.

For example, the model might spit out some JSON describing the operation it wants. The host parses this, routes it through the client, and returns the structured result to the model. The model then incorporates it into its next thought or message.

## Extensible and modular

*[Watch from 10:12](https://www.youtube.com/watch?v=rCBSQxQr9Xg&t=612s)*

It's also very extensible and modular. Anyone can write a conforming MCP server for any tool or API. The model can interact with dozens of such tools simultaneously, and new tools can be hot-plugged without retraining or reconfiguring the model itself.

If you learned something new, I would greatly appreciate a like and a subscribe. As always, thank you very much, and happy coding.

*[Watch the full video on YouTube](https://www.youtube.com/watch?v=rCBSQxQr9Xg)*

This article is adapted from the video "Model Context Protocol (MCP) Explained" by The Coding Gopher ([https://www.youtube.com/watch?v=rCBSQxQr9Xg](https://www.youtube.com/watch?v=rCBSQxQr9Xg)), licensed under [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/). It was transcribed and edited into an article by Galascribe.
