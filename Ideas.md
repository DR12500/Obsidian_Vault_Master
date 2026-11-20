How are words like "and" and "or" represented in the internals of a neural network and how do they differ to the logical "and" and the logical "or" in programming?

Designing a new benchmark, a thesis-sized version could have two tiers:

1. **Synthetic tier:** activations built from known manifolds mixed in superposition (extending Bhalla’s zoo, or BOWS-style data). Ground truth comes by construction, and it’s cheap to generate.
2. **Real-model tier:** a small catalogue of known manifolds in one or two open models (days, months, years, colors, geography). The metric is recall: does a method find them without labels?

**Intrinsic Interpretability:** 
most of the research has been done on how activation space is arranged in geometric patterns. What if we reverse it and force the model to arrange its activation space in organized geometric patterns/polytopes. Does this have an effect on interpretability (or performance)? 