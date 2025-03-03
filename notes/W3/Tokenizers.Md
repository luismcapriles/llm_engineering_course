# Tokenizers

On the low level of the **Hugging Face (HF) API**, we have **Tokenizers** and **Models**:

- A **Tokenizer** is the method used by a model to encode and decode text/code into [Tokens](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W2/Tokens.Md)
- This tokenization works as a **mapping mechanism**, where each token represents approximately **4 letters**.
- Each **model** has its **own Tokenizer**.
- **AutoTokenizer** is a **Transformers library from HF** that **automatically selects the appropriate tokenizer** based on the model name.

## Example: Using AutoTokenizer
```python
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained('meta-llama/Meta-Llama-3.1-8B', trust_remote_code=True)
```

---

## Encoding Text into Tokens
```python
text = "I am excited to show Tokenizers in action to my LLM engineers"
tokens = tokenizer.encode(text)
tokens
```

**Output:**
```python
[128000, 40, 1097, 12304, 311, 1501, 9857, 12509, 304, 1957, 311, 856, 445, 11237, 25175]
```

---

## Decoding Tokens Back into Text
```python
tokenizer.decode(tokens)
```

**Output:**
```
<|begin_of_text|>I am excited to show Tokenizers in action to my LLM engineers
```

---

## Using `batch_decode()` to Map Tokens to Words
Using `batch_decode()`, we can see how the tokens are mapped back into words in a list.

**Note:** Some words are split into multiple tokens.

```python
tokenizer.batch_decode(tokens)
```

**Output:**
```python
['<|begin_of_text|>', 'I', ' am', ' excited', ' to', ' show', ' Token', 'izers', ' in', ' action', ' to', ' my', ' L', 'LM', ' engineers']
```

---

## Special Tokens in Tokenizers
- The model uses **special tokens** to determine when text starts/ends.
- You can use the **vocab** command to check the model's tokenizer dictionary.

```python
tokenizer.vocab  # Shows the tokens mapped during training
tokenizer.get_added_vocab()
```

**Example Output:**
```python
{
  '<|begin_of_text|>': 128000,
  '<|end_of_text|>': 128001,
  '<|reserved_special_token_0|>': 128002,
  '<|reserved_special_token_1|>': 128003,
  '<|finetune_right_pad_id|>': 128004,
  '<|reserved_special_token_2|>': 128005,
  '<|start_header_id|>': 128006,
  '<|end_header_id|>': 128007,
  '<|eom_id|>': 128008,
  '<|eot_id|>': 128009,
  '<|python_tag|>': 128010,
  '<|reserved_special_token_3|>': 128011,
  ...
}
```

---

## **Instruct Variants of Models**
- Many models have a variant that has been trained for use in **chat applications**.
- These are typically labeled with the word **"Instruct"** at the end.

![]()


- They are trained to expect **prompts with a structured format**, including:
  - **System prompts**
  - **User messages**
  - **Assistant responses**

### **Using `apply_chat_template`**
There is a utility method **`apply_chat_template`** that converts messages from a list format into the correct input prompt for the model.

> **Note:**  
> Some models expect **text**, while others expect **code**. The message structure may differ accordingly.


# Comparing Different Model Tokenizers

We can check the Model Tokenizer structute by printing the **`apply_chat_template`** passing the message, **`tokenize=False`** as we don’t want to show all tokens, the **`add_generation_prompt=True`** tells the template to add special tokens that indicate the start of a bot response instead of doing something unexpected.

```python
messages = [
    {"role": "system", "content": "You are a helpful assistant"},
    {"role": "user", "content": "Tell a light-hearted joke for a room of Data Scientists"}
]
```

## Llama

```python
Llama_tokenizer = AutoTokenizer.from_pretrained('meta-llama/Meta-Llama-3.1-8B-Instruct', trust_remote_code=True)
print(tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True))
```

**Output:**
```
<|begin_of_text|><|start_header_id|>system<|end_header_id|>
Cutting Knowledge Date: December 2023
Today Date: 26 Jul 2024
You are a helpful assistant<|eot_id|><|start_header_id|>user<|end_header_id|>
Tell a light-hearted joke for a room of Data Scientists<|eot_id|><|start_header_id|>assistant<|end_header_id|>
```

---

## Phi-3

```python
print(phi3_tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True))
```

**Output:**
```
<|system|>
You are a helpful assistant<|end|>
<|user|>
Tell a light-hearted joke for a room of Data Scientists<|end|>
<|assistant|>
```

---

## Qwen2

```python
print(qwen2_tokenizer.apply_chat_template(messages, tokenize=False, add_generation_prompt=True))
```

**Output:**
```
<|im_start|>system
You are a helpful assistant<|im_end|>
<|im_start|>user
Tell a light-hearted joke for a room of Data Scientists<|im_end|>
<|im_start|>assistant
```

<details><summary><strong>More Details About Tokenizers</strong></summary>

# More Details About Tokenizers

```python
tokenizer = AutoTokenizer.from_pretrained(LLAMA) 
tokenizer.pad_token = tokenizer.eos_token 
inputs = tokenizer.apply_chat_template(messages, return_tensors="pt").to("cuda")
```

### Padding Token:

```python
tokenizer.pad_token = tokenizer.eos_token
```

The `.pad_token` is used to make all sequences in a batch the same length. For example:

## Example:

Let's say you have two sequences:

```python
seq1 = "Hello world"          # 2 tokens
seq2 = "Hi there everyone!"   # 3 tokens
```

To process these together efficiently, they need to be the same length. The shorter sequence gets padded:

```
seq1: [Hello] [world] [PAD]
seq2: [Hi] [there] [everyone!]
```

**NOTE:** Different models handle padding differently.
</details>

For more information in Tokenizer visit: https://huggingface.co/docs/transformers/main_classes/tokenizer






