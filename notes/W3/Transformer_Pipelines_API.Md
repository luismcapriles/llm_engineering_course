# Huggingface Transformers

When working with Data Science models, you could be carrying out two very different activities: **training** and **inference**.

## 1. Training
Training is when you provide a model with data for it to adapt and get better at a task in the future. It does this by updating its internal settings—the **parameters or weights** of the model. 

If you're training a model that has already been trained, the activity is called **"fine-tuning"**.

## 2. Inference
Inference is when you are working with a model that has **already been trained**. You are using that model to produce new outputs on new inputs, taking advantage of everything it learned while it was being trained. 

Inference is also sometimes referred to as **"Execution"** or **"Running a model"**.

All of our use of APIs for **GPT, Claude, and Gemini** in the last weeks are examples of inference. The **"P" in GPT stands for "Pre-trained"**, meaning that it has already been trained with data (lots of it!). In **week 6**, we will try fine-tuning GPT ourselves.

The **pipelines API** in HuggingFace is only for **inference**—running a model that has already been trained.

---

## HuggingFace Transformers Library

The HuggingFace transformers library provides APIs at two different levels:

- **High Level (Transformers Pipelines and Diffusers)**: For quickly using inference.
- **Low Level (Tokenizers and Models)**: More advanced, allowing access to the parameters (used for **fine-tuning**).

The **High-Level API** for using open-source models for typical inference tasks is called **"pipelines"**. It's incredibly easy to use.

## There two types of Pipelines: *Transformers*🤖 and *Diffusers* 🧠

- 🤖**Transformers pipelines** are used for processing and understanding **existing content** (text, speech, images...)
   - **You would use transformers pipelines when you want to:**
      	-  Convert speech to text (speech recognition)
      	-  Summarize text
      	-  Translate languages
      	-  Classify text or images
      	-  Extract information from text
      	-  Answer questions
      	-  Analyze sentiment

- 🧠**Diffuser pipeline**s are specifically designed for **generating new content**, particularly images..
   - **You would use diffusers when you want to:**
      	-  Generate images from text descriptions (like Stable Diffusion)
      	-  Create image variations
      	-  Edit or modify existing images
      	-  Generate video frames
         -  Perform image-to-image translation
<details>
<summary><strong>**Note on Audio generation**</strong></summary>
# Audio Generation: Transformers vs. Diffusers

Audio generation can actually be found in both libraries, but they handle different types of audio generation approaches:

## 1. Transformers:
- Handles audio generation through models like **MusicGen, Bark, and AudioLDM**.
- Typically uses **transformer-based architectures**.
- Good for tasks like **text-to-speech, music generation from text descriptions**.

### Example:
```python
from transformers import pipeline

# Using MusicGen
music_generator = pipeline("text-to-audio", "facebook/musicgen-small")
music = music_generator("An electronic dance song with a strong beat")

# Using Bark for text-to-speech
speech_generator = pipeline("text-to-speech", "suno/bark-small")
speech = speech_generator("Hello, how are you?")
```

---

## 2. Diffusers:
- Handles audio generation through **diffusion-based models**.
- Specializes in models that use the **diffusion process** (gradually denoising random noise).
- Examples include **AudioLDM2, Dance Diffusion**.

### Example:
```python
from diffusers import AudioLDM2Pipeline
import torch

pipe = AudioLDM2Pipeline.from_pretrained("cvssp/audioldm2", torch_dtype=torch.float16)
audio = pipe("a dog barking in a park").audio[0]
```

---

## Key Differences:
- **Transformers** use **sequence modeling and attention mechanisms**.
- **Diffusers** use the **diffusion process** (gradually removing noise).

For most general audio generation tasks, the **Transformers library** might be more commonly used since it includes popular models like **MusicGen and Bark**. However, if you're specifically interested in **diffusion-based audio generation**, you'd want to use **Diffusers**.
<details>

You create a pipeline using:

```python
my_pipeline = pipeline("the_task_I_want_to_do")
result = my_pipeline(my_input)
```

*Note:* Each pipeline has a different **default model** if you don't specify:

![Pipelines](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W3/HF_pipelines.png)

---

## Example of Sentiment Analysis

```python
classifier = pipeline("sentiment-analysis", device="cuda")
result = classifier("I feel that tool is completely inadequate to help us. ")
print(result)
```
<details>
<summary><strong>What does device="cuda" do?</strong></summary>

In this code, `device="cuda"` specifies that the text generation pipeline should run on an **NVIDIA GPU (Graphics Processing Unit)** rather than the CPU. **CUDA (Compute Unified Device Architecture)** is NVIDIA's parallel computing platform and programming model.

### Benefits of using CUDA:
1. The model and its computations will be performed on the **GPU** rather than the CPU.
2. This typically results in **much faster processing**, especially for large language models.
3. **Requirements:**
   - An **NVIDIA GPU** installed in your system.
   - **CUDA toolkit** installed.
   - **PyTorch installed with CUDA support**.
     - *Note:* Your PyTorch version must match your CUDA version. For example, if you have CUDA 11.8: Download and Install Pytorch as this
     ![Pytorch](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W3/pytorch.PNG)

### If you don't have a CUDA-capable GPU:
- **Remove** the `device` parameter entirely (it will default to **CPU**).
- Or explicitly use `device="cpu"`.

### Checking if CUDA is available:
To check if your system has CUDA support, run:

```python
import torch
print(torch.cuda.is_available())
```
</details>


