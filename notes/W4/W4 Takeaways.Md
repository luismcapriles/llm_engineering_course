# W4 Takeaway:

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
| **General LLM leaderboard** 📊 | [Hugging Face Open LLM Leaderboard](https://huggingface.co/spaces/open-llm-leaderboard/open_llm_leaderboard#/) |
| **Coding benchmarks** 💻 | [BigCode Models Leaderboard](https://huggingface.co/spaces/bigcode/bigcode-models-leaderboard) |
| **Performance benchmarks** ⚡ | [LLM Performance Leaderboard](https://huggingface.co/spaces/optimum/llm-perf-leaderboard) |
| **Model comparison & rankings** 🏆 | [Vellum AI LLM Leaderboard](https://www.vellum.ai/llm-leaderboard) |
| **Industry benchmark evaluations** 📈 | [Scale AI Leaderboard](https://scale.com/leaderboard) |
| **Software engineering benchmarks** 🛠️ | [SweBench](https://www.swebench.com/) |

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

## Interesting Notes:
Debates on an Apple paper stating that **LLMs have 0 evidence of human reasoning**. Instead, they rely on pattern recognition and memorization → next token prediction.  

[Apple AGI Research Claims There's ZERO Evidence LLMs Can Reason (+Tesla Optimus Robot Scandal)](https://chatgpt.com/share/6712d71e-add0-8012-9457-aba26302b31f)  

[François Chollet - How I Think About LLM Prompt Engineering](https://fchollet.substack.com/p/how-i-think-about-llm-prompt-engineering)  
