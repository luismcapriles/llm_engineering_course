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
