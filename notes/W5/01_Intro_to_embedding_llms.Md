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
![0](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_traditionals_llms.png)

### What Are Embedding Models?
**Embedding models** are a type of machine learning model designed to transform complex data—like words, images, or user preferences—into numerical representations called **embeddings**.

- If **text** → words are split into **tokens**.
- If **image** → full image is split into **pixels**.
- If **audio** → split into **audio chunks**.
![1](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_input_split.png)

Each **token** is associated with a **vector** (a list of numbers) that encodes its meaning.
![2](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_embeddings.png)


### Vector Representations
These vectors have **several values or dimensions** that can be represented as coordinates.

- Tokens with similar meanings have vectors that are **close in magnitude and direction**.
- Example: **(Bound, Jump, Leap, Hop, Skip)** all have similar vector representations.
![3](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_embeddings_plot.png)

### The Attention Block
A key operation in transformers is the **Attention Block**, which allows vectors to communicate and pass information back and forth.
![4](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_attention_block.png)

- The **Attention Block** determines which words in a context are relevant to updating the meaning of other words.
- It updates vector values to refine meaning.

**Example:**  
For the word **"Model"**, the Attention Block determines the context:
- **"Machine Learning Model"** vs. **"Fashion Model"**  
- The meaning is updated accordingly.
![5](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_meaning.png)

### Feed-Forward Layer
After passing through the Attention Block, vectors go through the **Feed-Forward Layer**, which:

- Takes each word’s representation.
- Applies mathematical transformations (matrix multiplications).
- Introduces **non-linearity** (decides what information to emphasize).
- Produces an updated representation for each word.
![6](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_multilayer_perceptron.png)

Think of this as each word **"thinking privately"** about its meaning before interacting again.
![7](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_multilayer_perceptron_2.png)

### Final Processing
The updated vectors are passed back through multiple **Attention Blocks** and **Feed-Forward Layers** several times.
![8](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_embeddings_iterations.png)

Finally, the **last vector** in the sequence is used to compute a **probability distribution** over all possible next tokens or chunks.
![9](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W5/img_final_output.png)

