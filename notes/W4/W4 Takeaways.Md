# W4 Takeaway:

## Evaluating Models 
- Start with the basics:
-- Numbers of Parameters
-- Context Lenght
-- Pricing

- Then look at the results
-- Benchmarks
-- Leaderboards 
-- Arenas

There are multiple Benchmarks in the markets. Some more popular than others.  

The relevance to one Benchmark over another relies on the Business Use Case Metrics.  

**You'll need to pick some outcome metrics, decide a subset of models, and measure your business metric against those models.**
or 
**You could use a model to help you pick one**  

Benchmarks can give you a starting point to have some candidate models.  

**What matters most is how closely a Benchmark is measuring what's relevant for your use case.**  

The "popular" metrics tend to be the ones that are most closely related to typical user goals.  

## Examples:  

- **IFEval** - Measures how good models are at following certain kinds of prompt instructions. If that is important to your task at hand, then that would be a good benchmark.  
- **ELO on [lmarena.ai](https://lmarena.ai)** - Mostly popular because it's fun!  

---

# COMPARING LLMs
## Limitations of Benchmarks

- Not consistently applied
- Too narrow in scope
- Hard to measure nuanced reasoning
- Training data leakage
- Overfitting

And a new concern, not yet proven

- Frontier LLMs may be aware that they are being evaluated

---

# 🚀 Benchmarks List  

| Why is it useful? 🔍 | Link 🔗 |
|----------------------|--------|
| **General LLM leaderboard** 🤗 | [Hugging Face Open LLM Leaderboard](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard#/) |
| **Coding benchmarks** 🤗 | [BigCode Models Leaderboard](https://huggingface.co/spaces/bigcode/bigcode-models-leaderboard) |
| **Performance benchmarks** 🤗 | [LLM Performance Leaderboard](https://huggingface.co/spaces/optimum/llm-perf-leaderboard) |
| **All Huggingface Leaderboard in Spaces** 🤗 | [HF LLM Leaderboard](https://huggingface.co/spaces?search=leaderboard) |
| **Model comparison & rankings** 🏆 | [Vellum AI LLM Leaderboard](https://www.vellum.ai/llm-leaderboard) |
| **Industry benchmark evaluations** 📈 | [Scale AI Leaderboard](https://scale.com/leaderboard) |
| **Software engineering benchmarks** 🛠️ | [SweBench](https://www.swebench.com/) |
| **Chatbot Arena voted from users** 🏆 | [LLM Arena](https://https://lmarena.ai/?leaderboard/) |

---

- [7 Commons Benchmarks](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W4/img_7_common_benchmark.png)
- [3 Specific Benchmark](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W4/img_3_specific_benchmark.png)
- [6 Hard  Next Level Benchmark](https://github.com/luismcapriles/llm_engineering_course/blob/main/notes/W4/img_6_hard_next_level_benchmark.png)

---
# 🏢 Official Models vs 🏗️ Non-Official Providers  

Typically, **unofficial providers** are either smaller labs or individual researchers who fine-tune models to squeeze more performance out of them—either using their own data or by generating data from other models.  

### ⚠️ Risks to Consider:
- **Overfitting to benchmarks** – Performance might look great in benchmarks but not generalize well in real-world tasks.
- **Training data opacity** – Less insight into their dataset and training methodology.
- **Reliability concerns** – No guarantees on long-term support or stability.  

### 🔒 Security Considerations:
- Generally, **low risk** from a data security perspective since you’re just taking their weights.
- Exception: **Tokenizers** could have security implications in rare cases.
- **Treat it like an open-source dependency** – Perform **due diligence** before using unofficial models in production.  

### 🏢 Enterprise Recommendation:
- Start with a **base model from an official provider** (e.g., Meta’s Llama, Google’s Gemma, Microsoft’s Phi).  
- Use **non-official models** as reference implementations for fine-tuning insights.  

---
# 📈 Evaluate Gen AI Performance 

## 🧠 Model-centric or Technical Metrics (**Easiest to optimize with**)

<details><summary><strong>Loss (eg cross-entropy loss)</strong></summary>
# Loss (e.g. cross-entropy loss)
Cross-entropy loss is a way of measuring how well a Generative AI (GenAI) model predicts probabilities for different possible outputs. It is commonly used when the model is generating text, images, or other structured outputs where multiple possible answers exist.

## Simple Analogy
Imagine you're playing a game where you have to guess the next word in a sentence. Your AI model predicts probabilities for several possible words. The closer the model’s predicted probability is to the correct answer, the better it performs.

## Mathematical Idea
Cross-entropy loss compares the predicted probabilities with the actual (ground truth) values. It gives a low loss when the model assigns a high probability to the correct answer and a high loss when the model assigns a low probability to the correct answer.

\[
\text{Loss} = - \sum_{i} y_i \log(\hat{y}_i)
\]

Where:

- \( y_i \) is the actual (true) label (1 for the correct class, 0 for others).
- \( \hat{y}_i \) is the predicted probability of class \( i \).
- The logarithm makes wrong predictions heavily penalized.

## In GenAI Context
For a text generation model, suppose we ask the model to complete:

**"The capital of France is ____."**  
If the model assigns:

- **"Paris"** → 80% probability  
- **"London"** → 15% probability  
- **"Berlin"** → 5% probability  

Since **"Paris"** is the correct answer, the cross-entropy loss would be **low** because the model assigned a high probability to the right answer. If it had predicted **"Berlin"** with a high probability instead, the loss would be **high**, signaling poor performance.

## Why It Matters?
Cross-entropy loss helps fine-tune the model by adjusting its weights so that it assigns higher probabilities to correct outputs over time, making the AI more accurate.

</details>

<details><summary><strong>Perplexity</strong></summary>
# Perplexity (PPL) Explained Simply

Perplexity is a way to measure how **confused** a language model is when making predictions.  

A lower perplexity means the model is **more confident** and better at predicting the next word, while a higher perplexity means the model is struggling.

## How Perplexity is Related to Cross-Entropy Loss

Perplexity (\( PPL \)) is directly derived from **cross-entropy loss**:

\[
PPL = e^{\text{Cross-Entropy Loss}}
\]

Since cross-entropy measures how well the predicted probabilities match the true ones, exponentiating it converts that loss into an interpretable score: **how many choices the model is effectively juggling at each step.**

## Intuitive Example

Imagine you're playing a "guess the next word" game, and the model has to complete:

**"The capital of France is ____."**

- **Good model (low perplexity):** Assigns **high probability** to **"Paris"** → Confident prediction.  
- **Bad model (high perplexity):** Assigns **similar probabilities** to **"Paris", "Berlin", and "London"** → More uncertain.  

- **If PPL = 1**, the model is perfect (it always predicts correctly).  
- **If PPL = 10**, the model is as uncertain as if it had 10 equally likely choices.  
- **If PPL = 100**, the model is struggling a lot.  

## Why Does Perplexity Matter in GenAI?

Perplexity is commonly used to evaluate **language models** like GPT, LLaMA, or T5. It helps answer:

- **How well does the model understand language?**  
- **Is it improving during training?** (Lower PPL = better)  
- **Which model version is better?** (Compare PPL scores)  

</details>

- Accuracy
- Precision, Recall, F1
- AUC-ROC



## 📊 Business-centric or Outcome Metrics (**Most tangible impact**)
- KPIs tied to business objectives
- ROI
- Improvements in time, cost or resources
- Customer satisfaction
- Benchmark comparisons



---

## Interesting Notes:
[Chinchilla Law](https://www.analyticsvidhya.com/blog/2024/09/chinchilla-scaling-law/)


Debates on an Apple paper stating that **LLMs have 0 evidence of human reasoning**. Instead, they rely on pattern recognition and memorization → next token prediction.  

[Apple AGI Research Claims There's ZERO Evidence LLMs Can Reason (+Tesla Optimus Robot Scandal)](https://chatgpt.com/share/6712d71e-add0-8012-9457-aba26302b31f)  

[François Chollet - How I Think About LLM Prompt Engineering](https://fchollet.substack.com/p/how-i-think-about-llm-prompt-engineering)  
