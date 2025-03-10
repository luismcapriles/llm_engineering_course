# Pipelines vs. Models for Inference

**Pipelines** is a higher-level API that carries out the most common inference tasks, but is less configurable and can't be used for training.

## Using Pipeline:

### Pros:
- Simpler API, handles all preprocessing and postprocessing automatically
- Great for quick prototyping and simple use cases
- Built-in safety features and error handling
- Handles batching automatically
- Easier to switch between models with minimal code changes

### Cons:
- Less control over the generation parameters
- Can be slower for batch processing of multiple texts
- Limited flexibility for custom preprocessing
- Harder to optimize for specific use cases

---

## Using Models:

### Pros:
- Full control over generation parameters
- Better performance optimization possibilities
- Can implement custom preprocessing/postprocessing
- More efficient for batch processing
- Easier to integrate with other custom components

### Cons:
- More complex implementation
- Requires better understanding of the model's architecture
- Need to handle preprocessing manually
- More code to maintain

---

## Example: Summarizing Meeting Minutes

1. If you're just doing **basic summarization without special requirements**, Pipeline is perfectly fine.
2. If you need to **customize the summary length based on input length, control the summarization style, or process multiple meetings efficiently**, direct model usage would be better.
