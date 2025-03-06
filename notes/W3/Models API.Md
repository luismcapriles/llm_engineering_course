# Models

When looking for optimizing inference or training (fine-tuning) pre-trained models, we will be talking about **Model API**. These libraries allow you to access parameters and modify them to get the most out of the model.  

### Analogy:  
- **Pipeline API** → automatic car (simpler and quick to use)  
- **Model API** → manual car (you have more control over the drive, changes, etc.)  

When working with Model API, you load/save the model either from a local file or directory or from a pretrained model configuration provided by the library (downloaded from Hugging Face).

### Base Classes of Model Libraries  
The base classes of Model libraries:  
- `PreTrainedModel`  
- `TFPreTrainedModel`  
- `FlaxPreTrainedModel`  
>[HF PreTrainedModel](https://huggingface.co/docs/transformers/v4.49.0/en/main_classes/model#transformers.PreTrainedModel)

These classes implement common methods for loading/saving a model.  

`PreTrainedModel` and `TFPreTrainedModel` also provide methods for:  
- Resizing the input token embeddings when new tokens are added to the vocabulary.  
- Pruning the attention heads of the model.  

### Focus in This Course  
In this course, we looked at `PreTrainedModel`.  

`PreTrainedModel` takes care of storing the configuration of models and handles methods for loading, downloading, and saving models, as well as a few common methods to:  
- Resize the input embeddings.  
- Prune heads in the self-attention heads.  

---

## Where Tokenizers Fit 🧩  

Tokenizers are a crucial preprocessing step before working with models. The **Model API** expects numerical input (tensors), but text needs to be converted into tokens first.

>Note:
>tensors (multi-dimensional arrays)

### How Tokenizers Relate to Model API  
- A model doesn't process raw text; it processes tokenized input.  
- Hugging Face provides `AutoTokenizer` to easily load the right tokenizer for a given model.  
- Tokenization involves:  
  - Splitting text into tokens.  
  - Converting tokens to numerical IDs (vocab indices).  
  - Adding special tokens (e.g., `[CLS]`, `[SEP]`).  
  - Padding/truncation to fit model input sizes.  

Thus, before loading a model using the **Model API**, we must tokenize the input.  

Example:
```python
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
tokens = tokenizer("Hello, how are you?", return_tensors="pt")
```
>Note:
>return_tensors="pt" ->> pt refers to pytorch this way we get pytorch tensor at the output 
---

## Where Quantization & `bitsandbytes` Fit 🚀 

Quantization and `bitsandbytes` help with model optimization for efficient inference.  

### Quantization  
- Reduces model size and memory usage by lowering the precision of model weights (e.g., from **32-bit floating point** to **8-bit** or **4-bit integers**).  
- Helps deploy models on lower-resource environments (e.g., consumer GPUs, edge devices).  
- Hugging Face supports quantization via libraries like `torch.quantization` and `bitsandbytes`.  

>Note:
>Visit here for [Quantization](https://huggingface.co/docs/transformers/quantization/overview)

### `bitsandbytes`  
- A specialized library for **4-bit** and **8-bit** quantization using `LLM.int8()` and `LLM.int4()` techniques.  
- Works with transformers to **reduce memory footprint** while keeping model accuracy high.  
- Used when loading models like:  
>Note:
>Visit here for [bitsandbytes](https://huggingface.co/docs/transformers/quantization/bitsandbytes)

```python
from transformers import AutoModelForCausalLM

model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-2-7b-chat-hf",
    load_in_4bit=True,
    device_map="auto"
)
```
## **Example of Quantization from FP36 to BF16**

1. Check current memory allocated

```python
!pip install -q requests torch bitsandbytes transformers sentencepiece accelerate
from transformers import AutoTokenizer, AutoModelForCausalLM, TextStreamer, BitsAndBytesConfig
import torch

#to know memory allocation before loading the model to memory
print(torch.cuda.memory_allocated()/1e9, "GB allocated")
print(torch.cuda.memory_reserved()/1e9, "GB reserved")
```
Output:
```python
0.0 GB allocated
0.0 GB reserved
```
2. Load the model and check the memory usage
By default the model without Quantization is loaded with its Weight at full-precision at FP32 (32 floating points) that takes a lot of memory
```python
#load the model without quantization and forced to GPU
model = AutoModelForCausalLM.from_pretrained(LLAMA, device_map={"":0}).to("cuda")
```
Output:
```python
32.12104550 GB allocated
32.12207717 GB reserved
```
3. Free the memory space
>Note:
>once the model is loaded, and I tried to clean the cache.  CUDA memory *is not released* with: 
```python
torch.cuda.empty_cache()
torch.cuda.ipc_collect()
```
>I had to restart the kernel, so the resources get free. 
![Cuda Free](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W3/cuda_free.png)

4. Quantization in action
Quantization Config - this allows us to load the model into memory and use less memory
There are different Quantization Techniques
Transformers supports many quantization methods, each with their pros and cons, so you can pick the best one for your specific use case.
During the course we used **`bitsandbytes`** ->> This uses [bitsandbytesConfig](https://huggingface.co/docs/transformers/main/en/main_classes/quantization#quantization)

```python
quant_config = BitsAndBytesConfig(
    bnb_4bit_compute_dtype=torch.bfloat16, #here we are downcasting from FP32 to BF16
)
```
```python
#Verifying the model loaded into memory with quantization BF16
model = AutoModelForCausalLM.from_pretrained(LLAMA, device_map={"":0},quantization_config=quant_config).to("cuda")

#to know memory allocation after loading the model with quantization BF16
print(torch.cuda.memory_allocated()/1e9, "GB allocated")
print(torch.cuda.memory_reserved()/1e9, "GB reserved")
```
Output:
```python
6.06089267 GB allocated
6.15933542 GB reserved
```
 *Conclusion PyTorch Downcasting*

 *Advantages:*
- **Reduced memory footprint**
  - More efficient use of GPU memory.
  - Enables the training of larger models.
  - Enables larger batch sizes.
- **Increased compute and speed**
  - Computation using low precision (fp16, bf16) can be faster than fp32 since it requires less memory.
  - Depends on the hardware (e.g., Google TPU, NVIDIA A100).

 *Disadvantages:*
- **Less precise**: We are using less memory, hence the computation is less precise.

---

## **Order of Steps in the Workflow**
1. **Load the Tokenizer**  
   - Tokenizers convert raw text into numerical format (tokens) the model can understand.  
   - This step **always comes first** because models do not process raw text.

2. **Tokenize the Input Text**  
   - The tokenizer outputs token IDs and necessary structures like attention masks.  
   - This processed input is then **passed into the model**.

3. **Load the Model (with or without Quantization)**  
   - Here, we load a pre-trained model from Hugging Face.  
   - If you want to **optimize inference**, you can **enable quantization at this step**.  
   - If fine-tuning, you may skip quantization initially and later optimize it for deployment.

4. **Run Inference or Fine-Tuning**  
   - If using the model for predictions (inference), pass tokenized input into the model and process its output.  
   - If fine-tuning, load data, train the model, and save the new fine-tuned weights.

---

## **Detailed Workflow with Code**

### **1️⃣ Load the Tokenizer**  
We need a tokenizer that matches the model's architecture.

```python
from transformers import AutoTokenizer

# Load a tokenizer for a specific model
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
```

---

### **2️⃣ Tokenize the Input Text**  
Convert raw text into token IDs, attention masks, etc.

```python
text = "Hello, how are you?"
tokens = tokenizer(text, return_tensors="pt")  # Output: token IDs ready for model input
```

---

### **3️⃣ Load the Model (with or without Quantization)**  

- **Without Quantization (default model loading):**  

  ```python
  from transformers import AutoModelForSequenceClassification
  
  model = AutoModelForSequenceClassification.from_pretrained("bert-base-uncased")
  ```

- **With Quantization (memory-optimized loading using `bitsandbytes`):**  

  ```python
  from transformers import AutoModelForCausalLM

  model = AutoModelForCausalLM.from_pretrained(
      "meta-llama/Llama-2-7b-chat-hf",
      load_in_4bit=True,  # Enable quantization
      device_map="auto"   # Auto-assign to available GPU
  )
  ```
  🔹 *Here, quantization reduces memory usage, making large models more efficient.*

---

### **4️⃣ Run Inference**  
Now, pass the tokenized input into the model to generate predictions.

```python
outputs = model(**tokens)
logits = outputs.logits
```

---

## **Summary of the Step Order**

| **Step**              | **Purpose**                                 | **Code Example** |
|----------------------|-------------------------------------------|------------------|
| **1. Load Tokenizer** | Prepare tokenizer for text processing      | `AutoTokenizer.from_pretrained(...)` |
| **2. Tokenize Input** | Convert text to numerical form for model   | `tokenizer(text, return_tensors="pt")` |
| **3. Load Model**     | Load pre-trained model (quantized or not)  | `AutoModel.from_pretrained(...)` |
| **4. Run Inference**  | Feed tokenized input into model            | `outputs = model(**tokens)` |

---

## **Where Does Fine-Tuning Fit?**  
If you’re **fine-tuning a model**, you would:  
1. Tokenize your dataset (same as above).  
2. Load the model **without quantization** (since training requires full precision).  
3. Train the model on new data.  
4. Optionally, **apply quantization later** for efficient inference.  

---

## **Final Thoughts**
- **Tokenization always happens first** because models don't understand raw text.  
- **Quantization is optional but must be applied when loading the model**, not after.  
- **Fine-tuning is done on a full-precision model**, and then **quantization can be applied** if needed for deployment.  
