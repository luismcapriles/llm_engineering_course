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
### Padding Token:

A Padding Token (PAD token) is a special token used to ensure that all input sequences in a batch have the same length. This is crucial when training or using NLP models that require fixed-size inputs.

```python
tokenizer = AutoTokenizer.from_pretrained(LLAMA) 
tokenizer.pad_token = tokenizer.eos_token 
inputs = tokenizer.apply_chat_template(messages, return_tensors="pt").to("cuda")
```

### A common approach is to set it to **`eos_token`**:

```python
tokenizer.pad_token = tokenizer.eos_token
```

  <details><summary><strong>What is eos_token ?</strong></summary>
  # Setting EOS as PAD  
  
  ```python
  # Some models (like LLaMA) don't have pad_token  
  # Common practice:
  if tokenizer.pad_token is None: 
      tokenizer.pad_token = tokenizer.eos_token  # EOS token serves double duty as padding
  ```
  
  ---
  
  ## Understanding `tokenizer.eos_token`  
  
  The **End of Sequence (EOS) token** is a special token that signals the end of a text sequence. It's like a **"full stop"** or **"period"** that tells the model, *"this is where the text ends."*  
  
  ### 1. Basic Usage  
  
  Different models might use different EOS tokens. Here's how you can retrieve the EOS token for various models:  
  
  ```python
  from transformers import AutoTokenizer 
  
  # GPT-2 EOS token  
  gpt2_tokenizer = AutoTokenizer.from_pretrained('gpt2')  
  print(gpt2_tokenizer.eos_token)  # '<|endoftext|>'  
  
  # T5 EOS token  
  t5_tokenizer = AutoTokenizer.from_pretrained('t5-base')  
  print(t5_tokenizer.eos_token)  # '</s>'
  ```
  
  ---
  
  ### 2. Why EOS is Important  
  
  Without an EOS token, the model **doesn't know when to stop** generating text.  
  
  ```python
  # Without EOS token  
  text = "How are you"  
  # Model doesn't know when to stop generating  
  
  # With EOS token  
  text = "How are you" + tokenizer.eos_token  
  # Model knows exactly where to stop  
  ```
  
  ---
  
  ### 3. Common Use Cases  
  
  #### **Text Generation**  
  
  When generating text, the EOS token helps the model determine when to stop.  
  
  ```python
  def generate_with_eos(model, tokenizer, prompt):  
      # Add EOS token to training data  
      input_ids = tokenizer(prompt + tokenizer.eos_token, return_tensors='pt').input_ids  
      
      # Model learns to generate text until EOS  
      generated = model.generate(
          input_ids, 
          max_length=100, 
          pad_token_id=tokenizer.eos_token_id  # Stop when EOS is generated  
      )  
  
      return tokenizer.decode(generated[0])
  ```
  
  #### **Sequence Classification**  
  
  For tasks like **sentiment analysis**, the EOS token helps define the **end of meaningful content**.  
  
  ```python
  text = "This movie is great" + tokenizer.eos_token  
  # EOS helps the model understand where the actual content ends  
  ```
  
  ---
  
  ### 4. Handling Multiple Sequences  
  
  When working with multiple text sequences, EOS tokens help **clearly separate** them.  
  
  ```python
  # Handling multiple sequences  
  sequences = [
      "First sequence" + tokenizer.eos_token,  
      "Second sequence" + tokenizer.eos_token  
  ]
  
  # During tokenization:  
  encoded = tokenizer(
      sequences, 
      padding=True,
      truncation=True
  )  
  # EOS tokens ensure proper separation  
  ```
  
  ---
  
  ### 5. Common Gotchas and Solutions  
  
  #### **Problem:** The model keeps generating text indefinitely.  
  
  ```python
  output = "This is a never-ending sequence..."  
  ```
  
  #### **Solution:** Always add an EOS token to mark the end.  
  
  ```python
  text = "This is complete" + tokenizer.eos_token  
  # The model now knows when to stop  
  ```
  </details>

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

### Different models handle padding differently:

# For example:
# BERT:
```python
from transformers import BertTokenizer

tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
# BERT has a default pad_token = '[PAD]'
print(tokenizer.pad_token) # outputs: '[PAD]'
```

# GPT-2:
```python
from transformers import GPT2Tokenizer

tokenizer = GPT2Tokenizer.from_pretrained('gpt2') # GPT-2 doesn't have a default pad_token
# Common solution:
tokenizer.pad_token = tokenizer.eos_token # Uses </s> as pad token
```

---

## When working with different models, you should:

1. **Check if the model has a default pad token:**
```python
if tokenizer.pad_token is None:
    # Need to set a pad token
    tokenizer.pad_token = tokenizer.eos_token # Common approach
```

2. **Be aware of padding side:**
```python
# For encoder-decoder models (like T5), usually pad on the left
encoded = tokenizer.batch_encode_plus(
    ["short text", "longer example text"],
    padding=True, truncation=True,
    padding_side='left' # or 'right'
)
```

3. **Consider attention masks:**
```python
# Attention mask will be 0 for pad tokens and 1 for regular tokens
encoded = tokenizer(["Hello", "Hi there"], padding=True, return_attention_mask=True)

# attention_mask might look like:
# [[1, 1, 0], # "Hello" + pad
#  [1, 1, 1]] # "Hi there"
```

---

# **Padding tokens are crucial and what happens if you don't use them:**

## **Why Padding is Important:**

### 1. Batch Processing
```python
# Without padding - this won't work:
sequences = [
    [1, 2],           # length 2
    [1, 2, 3, 4],     # length 4
    [1, 2, 3]         # length 3
]
# PyTorch/TensorFlow expect rectangular tensors
# ERROR: Cannot create tensor with irregular shape
```

### 2. Memory Efficiency
```python
# Bad approach - wasteful memory usage
max_length = 512
# Every sequence padded to maximum possible length
# Even a 3-token sequence uses 512 tokens of memory

# Good approach - efficient padding
sequences = ["short", "longer text", "medium"]
encoded = tokenizer(sequences, padding=True)
# Only pads to length of longest sequence in batch
```

---
  <details><summary><strong>Consequences of Not Using Padding</strong></summary>
  
  ### 1. Training Issues:
  ```python
  # Without padding - must process one sequence at a time
  for sequence in sequences:
      output = model(sequence)  # Inefficient
  
  # With padding - process multiple sequences at once
  batched_output = model(padded_sequences)  # Much faster
  ```
  
  ### 2. Computational Inefficiency:
  ```python
  # Example of processing cost
  batch_size = 32
  sequences_no_padding = [process_one_by_one(seq) for seq in sequences]  # 32 forward passes
  sequences_with_padding = process_batch(padded_sequences)               # 1 forward pass
  ```
  
  ### 3. Memory Problems:
  ```python
  # Without padding - must handle variable sizes
  for sequence in sequences:
      # Need separate memory allocation for each sequence
      output_buffer = torch.zeros(sequence.size())  # Inefficient memory management
  ```
  
  ### 4. Model Instability:
  ```python
  # Without padding or attention masks
  # Model might attend to random values or garbage data
  attention_scores = calculate_attention(sequences)  # Unreliable results
  
  # With padding and attention masks
  attention_scores = calculate_attention(
      padded_sequences, 
      attention_mask=mask  # Zeros out attention to pad tokens
  )
  ```
  
  ### 5. Poor Performance:
  ```python
  # Example showing loss calculation
  # Without padding - must handle each sequence separately
  loss = 0
  for seq in sequences:
      output = model(seq)
      loss += calculate_loss(output)  # Sequential processing
  
  # With padding - efficient parallel processing
  outputs = model(padded_sequences)
  loss = calculate_loss(outputs)  # One calculation
  ```
  </details>


# **Real-world impacts:**
- Training time could increase by **2-10x** without proper padding.
- Memory usage becomes **unpredictable and inefficient**.
- Model convergence might be **slower or less stable**.
- Batch processing becomes **impossible or highly inefficient**.
- Risk of **introducing bugs** in attention mechanisms.

In modern deep learning frameworks, padding is not just a convenience - it's a **fundamental requirement** for efficient and stable model training and inference. Without it, you'd face significant performance penalties and potential quality issues in your model's outputs.



</details>

For more information in Tokenizer visit: https://huggingface.co/docs/transformers/main_classes/tokenizer






