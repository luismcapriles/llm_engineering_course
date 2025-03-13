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
We have previously worked with **Traditional LLMs** or **Auto-regressive LLMs**, which predict a future token based on previous information (text, image, audio, etc.). These models select the next token based on the highest probability and repeat this process iteratively.

### What Are Embedding Models?
**Embedding models** are a type of machine learning model designed to transform complex data—like words, images, or user preferences—into numerical representations called **embeddings**.

- If **text** → words are split into **tokens**.
- If **image** → full image is split into **pixels**.
- If **audio** → split into **audio chunks**.

Each **token** is associated with a **vector** (a list of numbers) that encodes its meaning.

### Vector Representations
These vectors have **several values or dimensions** that can be represented as coordinates.

- Tokens with similar meanings have vectors that are **close in magnitude and direction**.
- Example: **(Bound, Jump, Leap, Hop, Skip)** all have similar vector representations.

### The Attention Block
A key operation in transformers is the **Attention Block**, which allows vectors to communicate and pass information back and forth.

- The **Attention Block** determines which words in a context are relevant to updating the meaning of other words.
- It updates vector values to refine meaning.

**Example:**  
For the word **"Model"**, the Attention Block determines the context:
- **"Machine Learning Model"** vs. **"Fashion Model"**  
- The meaning is updated accordingly.

### Feed-Forward Layer
After passing through the Attention Block, vectors go through the **Feed-Forward Layer**, which:

- Takes each word’s representation.
- Applies mathematical transformations (matrix multiplications).
- Introduces **non-linearity** (decides what information to emphasize).
- Produces an updated representation for each word.

Think of this as each word **"thinking privately"** about its meaning before interacting again.

### Final Processing
The updated vectors are passed back through multiple **Attention Blocks** and **Feed-Forward Layers** several times.

Finally, the **last vector** in the sequence is used to compute a **probability distribution** over all possible next tokens or chunks.

