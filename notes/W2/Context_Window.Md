# Context Window  
**Saturday, January 11, 2025**  
**1:35 PM**  

## OpenAI: Concept  

Refers to the maximum number of tokens that can be used in a single request, inclusive of both input, output, and reasoning tokens.  

For example, when making an API request to chat completions with the o1 model, the following token counts will apply toward the context window total:  

- **Input tokens** (inputs you include in the messages array with chat completions)  
- **Output tokens** (tokens generated in response to your prompt)  
- **Reasoning tokens** (used by the model to plan a response)  

Tokens generated in excess of the context window limit may be truncated in API responses.  

![Context window](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W2/context-window.png)

Source: [OpenAI Documentation](https://platform.openai.com/docs/models#context-window)  

---

![Context windows max token](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W2/context-window2.png)

When making a request, the tokens from the system prompt and user prompt appear to be stored in a "memory." Then, the model generates the output.  

Then when we do Follow-up Questions  

What really happens is that a new request is embedded with the history of the chat. This results in a larger prompt so the model can predict the most probable next token.  

For example, the **entire works of Shakespeare** are approximately:  

- **900,000 words**  
- **1.2 million tokens**  

To extract something from this data, you would need a model that supports a **context window of at least 1.2M tokens**.  

---

### Understanding Model Memory  

The model **does not** have real memory of past interactions. It simply predicts the most likely next token given a sequence of tokens.  

- If those tokens contain multiple interactions, the prediction considers what came before.  
- Token count increases over time as more context is included.  
- The APIs **do not optimize** token usage automatically.  

### Cost Considerations  

There are advanced techniques to reduce costs, but they are mostly useful when sending the same content repeatedly (e.g., a codebase for multiple conversations).  

Example:  
- **Prompt Caching:** [Anthropic - Prompt Caching](https://www.anthropic.com/news/prompt-caching)  

However, for most use cases, costs are **low**.  

- **GPT-4o-mini cost:**  
  - **15 cents per million input tokens**  
  - Storing the **complete works of Shakespeare** (~1.2M tokens) would cost **~15 cents**  

For typical conversations, even long ones, **costs remain minimal**.  

This matters more for chatbots handling **tens of thousands of conversations daily**, but for **most users, the expense is negligible**.  

Source: [Udemy - LLM Engineering Course](https://www.udemy.com/course/llm-engineering-master-ai-and-large-language-models/learn/lecture/46871427#questions/22706365)  

