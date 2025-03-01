# Token  

**Saturday, January 11, 2025**  
**12:57 PM**  

Tokens are common sequences of characters found in a set of text.  

A token is the way the model splits a word and predicts the next possible token that will make more sense.  

Models learn to understand the statistical relationships between these tokens and excel at producing the next token in a sequence of tokens.  

---

## Model Uses Different Tokenizers  

For OpenAI: [OpenAI Tokenizer](https://platform.openai.com/tokenizer)  

---

## Tokens  

Text generation and embeddings models process text in chunks called **tokens**. Tokens represent commonly occurring sequences of characters.  

For example, the string **"tokenization"** is decomposed as **"token"** and **"ization"**, while a short and common word like **"the"** is represented as a single token.  

> **Note:** In a sentence, the first token of each word typically starts with a space character.  

As a rough rule of thumb:  

- **1 token ≈ 4 characters** or **0.75 words** for English text.  
- **100 tokens ≈ 75 words**  
- **1000 tokens ≈ 750 words**  

The token count is **higher** for **math and code**.  

### One Limitation  

For a **text generation model**, the **prompt and the generated output combined** must be no more than the model's **maximum context length**.  

For **embeddings models** (which do not output tokens), the input must be **shorter** than the model's maximum context length.  

The **maximum context lengths** for each text generation and embeddings model can be found in the **[model index](https://platform.openai.com/docs/concepts/tokens)**.  

_Source:_ [OpenAI Documentation](https://platform.openai.com/docs/concepts/tokens)  
