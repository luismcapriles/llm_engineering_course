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


### More detailed info here
- [3Blue1Brown](https://www.youtube.com/watch?v=wjZofJX0v4M)
