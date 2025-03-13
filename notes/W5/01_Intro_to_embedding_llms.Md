# Introduction to Embedding in LLMs

## What is GPT?
GPT refers to **Generative Pre-Trained Transformer** models that generate new text.

- **Pre-Trained**: Indicates that the model was trained with some data and can be fine-tuned further with more specific data.
- **Transformer**: A key component of neural network architectures in machine learning.

## Training vs. Inference
We can interact with a model in two main ways:

- **Training**: The process of providing a model with data so it adapts and improves at a task over time. This happens by updating its settings (parameters or weights).
- **Inference**: Using a pre-trained model to generate new outputs.

## Traditional LLMs vs. Embedding Models
We have previously worked with **Traditional LLMs** or **Auto-regressive LLMs**, which predict a future token based on previous information (text, image, audio, etc.) These model select the next token based on the highest probability distribution. Once the next token is selected, the model can generate completely new output by repeating the process over and over again. 

![0](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_traditionals_llms.png)

There is a variant in Transforming model called Embedding Models: focus on creating vector representations (embeddings) of text that capture semantic meaning

### What Are Embedding Models?
**Embedding models** are a type of machine learning model designed to transform complex data—like words, images, or user preferences—into numerical representations called **embeddings**.

![Embeddings](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_embeddings2.png)

### The process goes like this: 

- If input data is in the form of **text** → words are split into **tokens**.
- If input data is in the form of **image** → full image is split into **pixels**.
- If input data is in the form of **audio** → split into **audio chunks**.

![1](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_input_split.png)

Each of these Token is associated with a **Vector** (a list of numbers which **encodes** “the meaning” of that peace)

![2](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_embeddings.png)


### Vector Representations
These vectors have **several values or dimensions** that can be represented as coordinates.

- Tokens with similar meanings have vectors that are **close in magnitude and direction**.
- Example: **(Bound, Jump, Leap, Hop, Skip)** all have similar vector representations.

![3](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_embeddings_plot.png)

As you noticed, Tokens with similar **“meaning”** have Vectors that are close in **magnitude** and **direction**. This is how model can capture relationships between words.

### The Attention Block
Then the list of vectors is passed to an operation called “Attention block” that allows the vectors to talk each other and pass information back and forth to update their values.

![4](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_attention_block.png)


This **Attention block** is responsible to figure out **which words** in the context are **relevant** to updating the meaning of which **other words**. By updating the meaning (refers to updating the encoded vector values).

**Example:**  
For example, the word: **“Model”** the Attention block identify the context (by **machine learning**) or (**fashion**) and update the vector meaning accordingly.  

![5](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_meaning.png)

### Feed-Forward Layer
After passing through the Attention Block, vectors go through the **Feed-Forward Layer**, which:

1. Takes each word’s representation.
2. Applies mathematical transformations (matrix multiplications).
3. Introduces **non-linearity** (decides what information to emphasize).
4. Produces an updated representation for each word.

![6](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_multilayer_perceptron.png)

Think of it as giving each word a chance to **"think privately"** about what it means in the current context, without looking at other words. 
- This layer typically consists of two main transformations with a non-linear activation function in between.

The Feed-Forward Layer is crucial because it adds computational depth and allows the model to capture more complex patterns than the attention mechanism alone could handle

![7](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_multilayer_perceptron_2.png)

### Final Processing
The updated vectors are passed back through multiple **Attention Blocks** and **Feed-Forward Layers** several times.

![8](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_embeddings_iterations.png)

After that, the hope is the **entire meaning** of the input is set into the **last vector of the sequence**. Where finally that last vector is computed to have probability distribution over all possible tokens or chunk that might come next

![9](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_final_output.png)

---
<details><summary><strong>Embeddings vs. Tokens: What’s the Difference?</strong></summary>

# Embeddings vs. Tokens: What’s the Difference?

**Tokens** and **embeddings** are both essential in natural language processing (NLP), but they serve different purposes.

1. Tokenized Representations (Numerical Tokens)
	• A tokenizer converts text into a sequence of numbers, but these numbers are just indexes that refer to words or subwords in a predefined vocabulary.
	• The numbers themselves do not capture meaning—they are simply unique IDs.
	• Example (using a vocabulary with index mapping):
		○ Text: "I love AI."
		○ Tokenized Output (IDs from vocab): [1, 345, 678, 2]
		○ Here, 1 = "I", 345 = "love", 678 = "AI", 2 = "."
	• If we were to compare "love" and "hate", their tokenized representations would be completely different (e.g., 345 vs. 921), even though they are opposites in meaning.

2. Embedding Vectors (Dense Representations of Meaning)
	• An embedding is a dense vector (a list of floating-point numbers) that captures the semantic meaning of a token.
	• Words with similar meanings have similar embeddings.
	• Example (simplified embedding representation for "love"):
		○ "love" → [0.75, -0.23, 0.89, ...]
		○ "hate" → [0.72, -0.21, 0.87, ...]
		○ Here, "love" and "hate" have embeddings that are somewhat similar, showing they belong to the same concept group (emotions) but are also distinct.

## Key Differences

| Feature | Tokenized IDs (Indexes) | Embedding Vectors |
|---------|-------------------------|-------------------|
| What It Represents | Just a unique ID for a word/subword | A vector capturing the meaning of a word |
| Type of Data | Integer (e.g., `345`) | Floating-point vector (e.g., `[0.75, -0.23, 0.89, ...]`) |
| Meaning Awareness | No semantic meaning | Captures relationships between words |
| Example Output | "love" → `345` | "love" → `[0.75, -0.23, 0.89, ...]` |
| Similarity Awareness | "love" and "hate" have unrelated token IDs | "love" and "hate" have somewhat similar embeddings |

## Analogy: A Dictionary vs. a Meaning Map
- **Tokens** are like **dictionary entries**: they tell you what words exist but don’t explain their relationships.
- **Embeddings** are like a **"meaning map"** where related words are placed near each other.
</details>



### More detailed info here
- [3Blue1Brown](https://www.youtube.com/watch?v=wjZofJX0v4M)
