---
layout: post
title: Large Language Models
categories:
    - Software Design
description: An primer on important concepts you should know when building and customizing AI assistants.
---

This page is intended as a simplified primer on important concepts for developing and customizing Large Language Models (LLM).


<details markdown="1">
<summary>Models</summary>

Training a completely new **base model** is **extremely expensive** (GPT-4: $100M) due to the required compute power to achieve good results, so this is only done by tech companies with enough capital:

- Meta: [Llama](https://huggingface.co/meta-llama) (public)
- Google: [Gemma](https://huggingface.co/collections/google/gemma-2-release-667d6600fd5220e7b967f315) (public), [Gemini](https://ai.google.dev/gemini-api) (API only)
- OpenAI: [GPT](https://platform.openai.com/docs/models/overview) (API only)
- Anthropic: [Claude](https://www.anthropic.com/claude) (API only)
- Mistral AI: [Mistral](https://huggingface.co/docs/transformers/main/en/model_doc/mistral) (public)
- (Many other models and remixes of larger models)

These models are trained on [billions of web pages](https://commoncrawl.org/), e.g. wikis, open source projects, etc.

These models often have different **variants**:
- Different sizes (e.g. 2B, 70B or 405B parameters). Larger models are [more powerful](https://lmarena.ai/?leaderboard), but require more resources.
- Fine tunings (e.g. "instruct models" optimized for chatbots)
- Multimodal models (understand images or speech)

Models have different **hardware requirements**: 
- Small models (≤ 70B): Can run **locally** (no internet access) on consumer hardware (gaming graphics cards, M1 MacBooks).
- Large models: Require expensive specialized hardware so **cloud access** is usually cheaper.
</details>


<details markdown="1">
<summary>Input/Output</summary>

LLMs are simple when viewed from the outside:
- **Input**: Text
- **Output**: Text

E.g. when the input is an entire chat history in a chat-bot, the LLM can predict what the next word(s) of the text would look like (more chat messages).

By cleverly choosing the input (**Prompt Engineering**), you can influence the output, e.g. with examples, instructions for language patterns, more context, etc.
</details>


<details markdown="1">
<summary>System Prompts</summary>

System prompts are predefined instructions given to an LLM in addition to user's input. They set the context and tone for the interaction.

Examples:
- **Role**: "You are a helpful assistant."
- **Behavior**: "Answer in English and provide detailed explanations."
- **Context**: "The time is {time} and the user's name is {name}. Here is a document relevant to the user's query: {document}"
</details>


<details markdown="1">
<summary>Vector Databases</summary>

Data can be stored in vector databases via mathematical "feature vectors". A collection of vectors is called an **embedding**.

These vectors lie in a multidimensional **vector space** (often plotted as a point cloud). Each dimension describes a **semantic meaning** (feature) of a data element. This has the advantage that related pieces of data are close to each other in the vector space.

**Semantic search**: A search query is converted to a feature vector and neighbors of this vector (related data) are retrieved.

**Embedding Models**: Text is converted to feature vectors with specialized embedding models, e.g. [nomic-embed-text](https://ollama.com/library/nomic-embed-text).

![](https://ds055uzetaobb.cloudfront.net/brioche/uploads/JERsKXkW4T-screen-shot-2016-05-05-at-123118-pm.png?width=2400)
*Source: Gutierrez-Osuna, R. Introduction to Pattern Analysis*

![](https://developers.google.com/static/machine-learning/crash-course/embeddings/images/linear_relationships.svg)
*Source: [Google Developer - Machine Learning Crash Course](https://developers.google.com/machine-learning/crash-course/embeddings/embedding-space)*
</details>


<details markdown="1">
<summary>Retrieval Augment Generation</summary>

Retrival augmented generation (RAG) is used to enrich a model with knowledge from documents (e.g. PDFs, videos, websites, …). 

These documents are split into chunks that are stored in a **vector database** as **embeddings**. Embeddings need to be regenerated if the documents change, so embeddings are **not real-time capable**.

A user request proceeds as follows:
- The request is converted into a vector in the embedding's vector space.
- Neighbors of this vector are searched in the vector database (**semantic search**).
- These vectors are **added to the system prompt** in their original text form.
- The LLM generates a response based on the additional context in the prompt.
</details>


<details markdown="1">
<summary>Tool Calling</summary>

Tool calling allows an LLM to call predefined **functions** ("tools") in the executing framework's code. With this it can call external APIs (search engines, databases, triggers, etc.). Therefore they are **real-time capable**.

LLMs still only handle text input and output:
- A **text description** for each tool is added to the LLMs prompt, so it knows how to use them correctly. 
- It can emit **JSON** formatted text during generation.
- The framework intercepts the output and calls the tool.
- The tools result is then **added to the system prompt** and the LLM can continue with the next tokens.

This works better if the model was fine tuned for tool calling.

Example:

```js
const getRecipesByMainIngedrientTool: RunnableToolFunction<any> = {
    type: 'function',
    function: {
        description: 'Search recipes by main ingredient. Ingredients should be in English and snakecase.',
        function: recipeApi.getRecipesByMainIngredient,
        parameters: {
            type: 'object',
            properties: { ingredient: { type: 'string' } },
        },
        parse: JSON.parse,
    },
};
```

</details>


<details markdown="1">
<summary>Fine Tuning</summary>

Fine-tuning adjusts the **general behavior** of an existing model through **further training**. For example to use it as a chatbot (question/answer), as a code generator, to write books, or to use certain vocabulary.

There are two options for this:
- **Full parameter fine-tuning**: An existing model is further trained, but with a specialized dataset. This adjusts **all parameters** over time, creating a new model. Requires a lot of computing power (expensive).
- **Low-rank adaptation (LoRA)**: Adds **additional parameters** to a model without affecting the original parameters. Requires less computing power.
</details>
