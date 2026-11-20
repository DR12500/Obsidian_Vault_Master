[Do Sparse Autoencoders Capture Concept Manifolds?](https://arxiv.org/pdf/2604.28119)
https://www.goodfire.com/research/neural-geometry
https://www.goodfire.com/research/the-world-inside-neural-networks

[**Fourier decomposition**](https://arxiv.org/abs/2605.01148) a mathematical method that breaks down a complex or irregular signal, function, or time series into a sum of simpler, harmonically related sine and cosine waves (or complex exponentials).

If you want to read the primary sources that deeply connect our brainstorming session to the mechanics of this review paper, you should focus on these four foundational texts:

**IMPORTANT:** Finding a loop in a point cloud only shows correlation. The thesis needs a causal intervention showing the model actually uses the structure.
**Additionally**: Highlighting whether underlying geometric patterns are inherent to the data the model was trained on or due to architectural design choices. (**TODO**) Further research needs to be done to investigate if and how well these two cases can be distinguished. 


[Can we use lines to capture curved neural geometry?](https://www.goodfire.com/research/can-saes-capture-neural-geometry)
	Yes—but it's complicated. We are reminded of the old proverb in which blind men encounter an elephant for the first time. Each touches a different part—the trunk, the tusk, the leg—and each comes to a different conclusion about the elephant: the man touching the trunk says an elephant is like a snake, the one touching the leg says it's like a tree, and so on. Similarly, a single line can only give us a partial view of curved geometric structure, and consequently an incomplete understanding of the bigger picture. As with the elephant, the full meaning emerges only when the parts are understood as a whole.

##################
**OPUS:**
**Make it three sources.**

1. **Data:** the shape reflects structure in the training text, such as days following each other cyclically.
2. **Architecture and training:** the shape comes from design choices. Rotary positional embeddings, for example, build rotations into the model. A loop you find could be a positional artifact rather than a concept. That’s a real confound for any circle you discover.
3. **The analysis method:** the shape comes from your tool. The SAE sanity-check results show this matters, and it’s what your random-SAE null in RQ2 is designed to catch.
4. Hypothetically a 4th source might be the if it reveals underlying structure or fabrics of reality itself. Frankly, this is hard to prove/disprove and a bit too complex for a master thesis 

**Cheap tests that fit a master’s thesis:**

- **Untrained model:** run the pipeline on a randomly initialized model. Shapes that appear there come from the architecture or the method, not the data.
- **Data-statistics comparison:** compute co-occurrence statistics (e.g., a PMI matrix) for the concept’s tokens and check whether that already contains the loop. If it does, the data explanation gets stronger.
	- _Claude's idea (moved here from the exposé, not covered there yet):_ this test is cheap and can be made precise with the theory of [Karkada et al. (2026)](https://arxiv.org/abs/2602.15029): translation symmetry in co-occurrence predicts Fourier modes (sinusoids along the principal components). If these predicted modes show up in the PMI matrix of the concept tokens, the data explanation is strong. [Prieto et al. (2026)](https://arxiv.org/abs/2603.09972) support the same check: the month circle is already visible in a PCA of the raw bag-of-words data.
- **Training checkpoints:** the Pythia suite releases checkpoints of models trained on the same data at different sizes. You can see when a shape emerges and whether it survives across scales.
- **Different architecture, same data:** compare a transformer with a state-space model such as Mamba trained on comparable data. If the shape persists, it’s probably data-driven.

##################
-----------------
TODO: Check if the following AI-generated output are still relevant. OPUS'es output might help here...


- **The Core Superposition Text: _Toy Models of Superposition_ (Elhage et al., 2022)**
    - _Connection:_ Heavily cited in the paper. This is the empirical toy-model study (small ReLU models, not a proof about real networks) of how networks exploit high-dimensional geometry to store more features than they have physical dimensions, creating the complex landscapes we talked about.
- **The Cognitive Geometry Text: _The Platonic Representation Hypothesis_ (Huh et al., 2024)**
    - _Connection:_ This directly validates our discussion on convergent evolution. It is a position paper: it hypothesizes that as vision and language models scale, their representations converge toward a shared statistical model of reality, and gives empirical evidence that representations become more similar with scale. It does not prove that they reach the exact same shape.
- **The Concept Geometry Text: _The Geometry of Categorical and Hierarchical Concepts in Large Language Models_ (Park, Choe, Jiang & Veitch, ICLR 2025)**
    - _Connection:_ Cited in Section 3.2 of the paper. It moves past theoretical vectors and maps the actual physical shapes (simplices and polytopes) that concepts like "animals" take inside modern LLMs.
        
- **The Non-Linear Geometry Text: _Not All Language Model Features Are One-Dimensionally Linear_ (Engels et al., ICLR 2025)**
    - _Connection:_ Cited as a vital counter-example in the paper. While most features are linear vectors, these researchers discovered that neural networks sometimes build **approximately circular, multi-dimensional representations** to compute cyclical concepts like time, days of the week, and months.
## available Tools
- [TransformerLens](https://www.google.com/url?sa=i&source=web&rct=j&url=https://github.com/TransformerLensOrg/TransformerLens&ved=2ahUKEwiJpNLu7dyXAxXVRfEDHcgMMQMQy_kOegoIAggACAAIGhAD&opi=89978449&cd&psig=AOvVaw3K9z3ohcUZSwnrsTebq1PD&ust=1793281917449000)
- [Logit Lens](https://www.google.com/url?sa=i&source=web&rct=j&url=https://mbrenndoerfer.com/writing/logit-lens&ved=2ahUKEwiJpNLu7dyXAxXVRfEDHcgMMQMQr8wVegoIAggACAAIGhAK&opi=89978449&cd&psig=AOvVaw3K9z3ohcUZSwnrsTebq1PD&ust=1793281917449000) variants (e.g., Tuned Lens)
- Sparse Autoencoders
	- [SAELens](https://www.google.com/url?sa=i&source=web&rct=j&url=https://github.com/jbloomAus/SAELens&ved=2ahUKEwiJpNLu7dyXAxXVRfEDHcgMMQMQy_kOegoIAggACAAILBAD&opi=89978449&cd&psig=AOvVaw3K9z3ohcUZSwnrsTebq1PD&ust=1793281917449000)
	- [Neuronpedia](https://www.google.com/url?sa=i&source=web&rct=j&url=https://www.neuronpedia.org/&ved=2ahUKEwiJpNLu7dyXAxXVRfEDHcgMMQMQy_kOegoIAggACAAILBAE&opi=89978449&cd&psig=AOvVaw3K9z3ohcUZSwnrsTebq1PD&ust=1793281917449000)
	- Transcoders / Crosscoders: Specialized variants of SAEs designed to decompose features across layers, allowing you to trace how a geometric feature in layer N structurally translates into a feature in layer N + 1 ([source](https://www.google.com/url?sa=t&source=web&rct=j&url=https%3A%2F%2Ftransformer-circuits.pub%2F2025%2Fattribution-graphs%2Fmethods.html&ved=0CAEQnM4VahcKEwjo3fXx7dyXAxUAAAAAHQAAAAAQSg&opi=89978449))
- **Topological Data Analysis** Tools
	- [giotto-tda](https://www.google.com/url?sa=i&source=web&rct=j&url=https://arxiv.org/abs/2004.02551&ved=2ahUKEwiJpNLu7dyXAxXVRfEDHcgMMQMQy_kOegoIAggACAAIOhAD&opi=89978449&cd&psig=AOvVaw3K9z3ohcUZSwnrsTebq1PD&ust=1793281917449000): 
		- tracks persistent homology
		- construct grpahs, build dendrograms, generate persistence barcodes etc.
		- Dimensionality Reduction Visualizers (like UMAP and PCA)
- Circuit Trancing Engines (e.g., Goodfire)
- Attribution Graph Libraries