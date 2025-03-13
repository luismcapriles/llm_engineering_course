# Introduction to Embedding in LLMs

## What is GPT?
GPT refers to **Generative Pre-Trained Transformer** models that generate new text.

- **Pre-Trained**: Indicates that the model was trained with some data and can be fine-tuned further with more specific data.
- **Transformer**: A key component of neural network architectures in machine learning.

## Training vs. Inference
We can interact with a model in two main ways:

- **Training**: The process of providing a model with data so it adapts and improves at a task over time. This happens by updating its settings (parameters or weights).
- **Inference**: Using a pre-trained model to generate new outputs.
---

# Auto-Encoding vs. Auto-Regressive Models and How They Relate to Embeddings  


## 1. Auto-Encoding Models (e.g., BERT)  
These models encode input into a dense representation (**embedding**) and try to reconstruct the original input.  

### 🔹 How They Work:  
- They take an input text, **mask some words**, and train the model to **predict those missing words**.  
- The goal is to **learn contextual embeddings**, meaning each word is represented based on its **sentence context**.  

### 🔹 Example:  
- **Input**: `"The cat sat on the MASK."`  
- **Model Predicts**: `"mat"`  
- The model learns that `"cat"` and `"mat"` are often related, improving its embeddings.  

### 🔹 Relation to Embeddings:  
- **Auto-encoding models produce deep contextual embeddings**, meaning the word `"bank"` in `"financial bank"` gets a different embedding than in `"river bank"`.  
- This is why **BERT-based embeddings** are more powerful than static ones like **Word2Vec**.  

---

## 2. Auto-Regressive Models (e.g., GPT)  
These models predict the **next word** based on **previous words**.  

### 🔹 How They Work:  
- Given an **input sequence**, they **generate the next word/token step by step**.  
- Instead of masking words like in **BERT**, they use **causal attention**, meaning the model **only looks at past words**.  

### 🔹 Example:  
- **Input**: `"The cat sat on the"`  
- **Model Predicts**: `"mat."`  

### 🔹 Relation to Embeddings:  
- **Auto-regressive models generate embeddings dynamically** while predicting text.  
- Each **token is encoded into a hidden representation** before making the next prediction.  
- Unlike auto-encoding models, which **learn bidirectional context** (both left and right), **auto-regressive models only learn from past words**.  

---

## 3. How This Relates to Embeddings  
Both models generate embeddings but in **different ways**:  

### 🔹 1. Auto-Encoding Models (**BERT**)  
✔ Learn **bidirectional embeddings** that capture **deep meaning**.  
✔ Used for **semantic understanding** (*e.g., search engines, question answering*).  

### 🔹 2. Auto-Regressive Models (**GPT**)  
✔ Learn **contextual embeddings** based on **left-to-right training**.  
✔ Used for **text generation** (*e.g., chatbots, story writing*).  

---

## 🎭 Analogy: Compression vs. Storytelling  

### 📂 **Auto-Encoding (BERT) → Zip File**  
- Imagine you have a big document and you **compress it into a meaningful summary** (**embeddings**).  
- You can then use that **compressed information** to **reconstruct missing parts** of the document.  

### 📖 **Auto-Regressive (GPT) → Writing a Novel**  
- You **start with the first sentence** and keep **adding words based on the previous ones**.  
- You **never go back to edit**, only move forward.  


## 4. Summary table: Auto-Encoding vs. Auto-Regressive Models  

| Feature | Auto-Encoding | Auto-Regressive |
|---------|---------------|-----------------|
| Goal | Compress and reconstruct input efficiently | Predict the next token/word in a sequence |
| How It Works | Learns a compressed latent representation (embedding) | Generates text one token at a time based on past context |
| Example Models | BERT, ALBERT, Variational Autoencoders (VAEs) | GPT, Transformer-XL, LLaMA |
| Training Task | Masked Language Modeling (fill missing words) | Causal Language Modeling (predict the next word) |

>NOTE:
Auto-regressive are models that are designed to be really good a predicting the next token in a sequence. The output is a vector that represents the probabilities of the next token. At training time, the loss is calculated based on it's accuracy in predicting the next token. So with an input like, "We are living in a material", the auto-regressive model might contain within its layers a representation of the context, but the output from the model will be the probabilities of the next token, hopefully with "world" getting a high probability!
>

>
Auto-encoding are trained using various techniques so that the output is a vector that reflects the entire meaning of the input sequence, not the probability of the next token.
>

---

## So What is Vector Embedding?
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

---
<details><summary><strong>Personal question on Embeddings</strong></summary>
# 1. How Is the High-Dimensional Vector Defined for a Word?  

The vector (embedding) for a word is **not manually assigned**—it is **learned** during training using mathematical optimization. The number of dimensions (e.g., 100, 300, 768, 1024) is chosen based on the model design.  

### How is the embedding learned?  
1. **Training on Large Text Data**: The model processes millions/billions of words and learns patterns in how words appear together.  
2. **Adjusting Embeddings Based on Context**: The model starts with random vectors and updates them using techniques like **gradient descent** and **backpropagation** to make similar words closer in vector space.  
3. **Result**: Each word gets a unique vector that captures meaning based on its **context and relationships** with other words.  

---

# 2. Does a Dictionary of Predefined Vectors Exist for Each Word?  

It depends on the model type:  

## (A) Static Embeddings (Predefined Vectors)  
- In older models like **Word2Vec, GloVe, or FastText**, a **fixed dictionary** of word embeddings exists.  
- **Example**: Word2Vec might have a lookup table like:  
  - `"king"`  → `[0.5, 0.3, -0.7, ...]` *(fixed 300D vector)*  
  - `"queen"` → `[0.52, 0.28, -0.69, ...]`  
- Every time you see `"king"`, it has the **same vector**, no matter the context.  

## (B) Dynamic Contextual Embeddings  
- In **modern models** (*BERT, GPT, T5, etc.*), embeddings are **dynamic**.  
- The **same word** can have **different vectors** depending on the sentence.  
- **Example with BERT**:  
  - `"I will book a flight."` → `"book"` embedding → `[1.2, -0.3, 0.8, ...]`  
  - `"I read a book."` → `"book"` embedding → `[0.5, 1.1, -0.4, ...]`  
- The model **computes embeddings on the fly** instead of using a static lookup table.  

---

# 3. Can the Values of an Embedding Vector Be Dynamic?  

Yes! In **contextual embeddings** (*e.g., BERT, GPT, T5, etc.*), the values change dynamically based on:  

1. **Sentence Context** → The same word in different sentences has different vectors.  
2. **Fine-Tuning** → If you **fine-tune** a model on a specific task (*e.g., medical documents*), the embeddings can shift to represent medical meanings better.  
3. **Different Model Layers** → In deep models, embeddings evolve **across layers**. The deeper layers typically contain more task-specific information.  

💡 **Example**:  
- A **lawyer** searching for `"bank"` might get a vector closer to **finance-related** terms.  
- A **biologist** searching for `"bank"` might get a vector closer to **ecosystem-related** terms.  

---

# 4. Is There a Universal Embedding, or Does Each Model Use Different Embeddings?  

There is **no single universal embedding** because different models use different training data, architectures, and objectives.  

| Model    | Embedding Type          | Fixed or Dynamic? |
|----------|-------------------------|-------------------|
| **Word2Vec** | Single word embeddings | Fixed |
| **GloVe**    | Single word embeddings | Fixed |
| **FastText** | Word + Subword embeddings | Fixed |
| **BERT**     | Contextual embeddings | Dynamic |
| **GPT**      | Contextual embeddings | Dynamic |
| **T5**       | Contextual embeddings | Dynamic |

### Why does each model use different embeddings?  
- **Word2Vec and GloVe** were trained on Wikipedia/news, so they represent **general meanings**.  
- **BERT** is trained to understand **sentence context**, so it assigns different embeddings to the same word in different cases.  
- **GPT** learns embeddings **while generating text**, making them **optimized for completion tasks**.  
- **Fine-tuned models** (*e.g., BioBERT for medical texts, LegalBERT for legal documents*) have embeddings that reflect **domain-specific meanings**.  

---

# 🎵 Analogy: Music Playlists vs. Live Music Performance  

1. **Static Embeddings (Word2Vec, GloVe) → Pre-recorded Playlist**  
   - Imagine a **music playlist** where each song is **fixed**.  
   - You always get the **same song (embedding) for a given word**, regardless of the situation.  

2. **Dynamic Embeddings (BERT, GPT) → Live Music Performance**  
   - A **live musician** adjusts the song based on the audience, mood, and context.  
   - The **word embedding changes dynamically** based on the sentence.  

---

# ✅ Final Summary  
✔ **The embedding vector is learned** from large text data, not predefined.  
✔ **Static embeddings** (*Word2Vec, GloVe*) use a **fixed dictionary of word vectors**.  
✔ **Dynamic embeddings** (*BERT, GPT*) **change based on context**.  
✔ **There is no universal embedding**—each model has its own way of representing words.  
</details>

### More detailed info here
- [3Blue1Brown](https://www.youtube.com/watch?v=wjZofJX0v4M)
