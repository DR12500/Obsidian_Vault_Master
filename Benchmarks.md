- [ ] AxBench (Wu et al., ICML 2025) _(skim)_. Read before MAxBench, which builds on it. [arXiv 2501.17148](https://arxiv.org/abs/2501.17148)
	- valuate model-control methods, including prompting and finetuning baselines, along two utility axes: concept detection C and model steering
	- For the former, we measure classification performance on a held-out set of labelled data. For the latter, we use an LLM judge to rate steered outputs on three relevant axes (see §3.3).
	- **Concept detection** (also important for MAxBench)
		- Formally, given a Transformer-based LM with a hidden dimension size of d, we define a concept classifier as a parameterized function Ψ Detect that maps a model representation h ∈ Rd into a binary label y indicating the relative presence of a concept
		- To evaluate a concept classifier, we measure how accurately it can predict ground-truth labals on the labelled evaluation set from D_concept
	- **Model steering** (also important for MAxBench)
		- Representation-based steering as an alternative to finetuning and prompting
		- Given a prompt x, the model’s original generation can be written as ˆy = LM(x). We produce the model’s counterfactual generation conditioned on the concept-based intervention ΦSteer(h): ˆySteer = LMx, h ← ΦSteer(h) where h ← ΦSteer(h) is an in-place representation modification
	- Both, Concept detection and Model steering come with their own evaluation dataset and metric as well as a variety of different evaluation methods 
- [ ] MAxBench (Appapogu et al., 2026). How geometries are recovered and scored; note its limitation to ordered concepts. [arXiv 2609.13072](https://arxiv.org/abs/2609.13072)
	-  For binary concepts (AxBench) such as refusal, a single direction in activation space often suffices for steering, but many concepts are not binary
	- compare 10 localization methods (covering 5 geometry types) across 6 concepts and 4 models
	- find that 
		1. affine subspaces steer more reliably and have greater recall than rank-one or linear subspaces
		2. much of this advantage is due to better non-zero offsets rather than the choice of bases 
		3. manifold steering is competitive with the best methods when applicable
		4. no method consistently outperforms prompting, in alignment with prior findings on binary concepts.
	- moving from onedimensional/binary to multidimensional/multinomial concepts therefore introduces several challenges.
		- space of possible geometries becomes much larger, and may depend on the concept
		- he dimensionality (or rank) of a concept representation is generally not known in advance and likely varies significantly across concepts.
	- contributions:
		 1. We propose a sampling-based steering framework for evaluating how well a given representation captures a target concept. This framework is geometry-agnostic, and can be applied to linear and non-linear representations.
		 2. We introduce MAXBENCH, a benchmark that leverages this sampling-based framework to enable comparisons of concept representations across multiple concepts and geometric structures.
		 3. We use MAXBENCH to compare 10 concept representation discovery methods across 6 concepts, 5 geometries, and 4 models. We find that 
			 1. affine subspaces steer more reliably and have greater recall than rank-one or linear subspaces
			 2. much of this advantage is due to better non-zero offsets rather than the choice of bases 
			 3. manifold steering is competitive with the best methods when applicable
			 4. no method consistently outperforms prompting, in alignment with prior findings on binary concepts. 
		4. We introduce affine generalizations of existing steering methods for recovering multinomial concept representations.
	- Concept localization methods
		- linear: One-dim methods (DiffMean-r1, ReFT-r1), affine methods (PCA, SchattenProbe, DiffMean, Linear Probe, Factor Analysis), Collection of Directions (SAEs with MCC for scoring)
		- piecewise / local: Gaussian mixture (MFA) – local affine regions, not globally linear
		- non-linear: Manifold Methods from [Wurgaft et al. (2026)](https://openreview.net/pdf?id=QklBm5nnR9) (spline fitting)
			- limitation: only works for ordered concepts and, in its standard form, needs the complete sequence of values; in the main evaluation it is only applied to _Days_
	- **Sampling-Based Evaluation** if a geometry faithfully captures a semantic concept, then points sampled from the geometry should decode only to valid instances of that concept (exclusivity) while covering the full range of values and categories contained by the concept (completeness).
		- the next two paragraphs from the paper describe how sampling from affine subspace and sampling from a non-linear manifold is accomplished
	- **Scores**
		- Concept Score (_taken from AxBench_): measure whether the generated output contains text relevant to the target concept
		- Diversity Score: 
			1. Extract entities mentioned in each generated sentence (e.g., cat, sparrow) together with their associated category labels (e.g., mammal, bird for the ANIMALS concept)
			2. aggregate the extracted entities and categories across all generated samples and compute the normalized Shannon entropy at two levels:
				1. **instance-level**, which measures the diversity of distinct concept instances in any category (e.g., dog, lion, tiger)
				2. **category-level**, which measures how broadly the generations span higher-level semantic categories within the concept (e.g., mammal, bird, reptile)
			3. sum of the normalized entity- and category-level entropies
		- MAxBench Score (_inspired by AxBench_):
			- measure fluency and instruction
			- Harmonic mean of concept, fluency, instruction-following _and additionally diversity_ score
	- Results:
		- **Offset-Shifted Subspaces Work Best:** Using a group of directions with a shifted starting point (affine subspaces) steers models more reliably and captures concepts better than using just a single straight line.
		- **Steering Still Can't Beat Simple Prompting:** Non-linear shapes work well for ordered topics like dates, but plain text instructions (prompting) still outperform all vector-steering techniques.
	-  **Future work** should extend MAXBENCH with a wider range of non-linear geometric representations.


**TODO**
- [ ] SAEBench (Karvonen et al., ICML 2025). Which of its 8 metrics fit; keep in mind it scores features one at a time. [arXiv 2503.09532](https://arxiv.org/abs/2503.09532)
	- spanning interpretability, feature disentanglement and practical applications like unlearning. 
	- over 200 SAEs across eight recently proposed SAE architectures and training algorithms (abstract: "eight")
	- gains on proxy metrics do not reliably translate to better practical performance
	- [github](https://github.com/adamkarvonen/SAEBench)
	- five key requirements for a comprehensive SAE evaluation framework:
		- **Diversity.** Metrics should capture a broad range of SAE behavior, from basic reconstruction to downstream task performance, since relying on a single metric can overlook important tradeoffs.
		- **Extensibility.** The framework should offer a standardized structure, making it easy to add new evaluation methods as the field evolves. 
		- **Speed.** Evaluations must complete within one to two hours per SAE, facilitating rapid research iteration. 
		- **Automation.** A single script should run all evaluations, eliminating the need to integrate separate codebases manually. 
		- **Reproducibility.** All metrics must be deterministic and reproducible to ensure fair comparisons between architectures.
	- four fundamental capabilities that together characterize effective SAEs
		- **Concept Detection:** Measures how precisely individual latents correspond to meaningful concepts through Sparse Probing and Feature Absorption metrics
		- **Interpretability:** Evaluates human-understandability of learned latents using an LLM as a judge
		- **Reconstruction:** Quantifies how faithfully the SAE preserves the model’s original behavior via Loss Recovered metrics
		- **Feature Disentanglement:** Assesses proper separation of independent concepts through Unlearning, Spurious Correlation Removal, and Targeted Probe Perturbation
	- **Existing Metrics**
		- **sparsity-fidelity trade-off** has dominated recent SAE development
			- While advances in this trade-off are valuable, small gains do not necessarily translate into qualitatively better representations or superior performance on downstream interpretability tasks.
		- **Automated Interpretability**
		- **k-Sparse Probing**
		- **RAVEL**
	- **Adapted Metrics**
		- Feature Absorption
		- Unlearning Capability
	- **Novel Metrics**
		- Spurious Correlation Removal - extends the SHIFT method
		- Targeted Probe Pertubation



# The following Benchmarks have not yet been covered
- [ ] SynthSAEBench (Chanin & Garriga-Alonso, 2026) _(skim)_. A contrast case: linear by construction. [arXiv 2602.14687](https://arxiv.org/abs/2602.14687)
- [ ] MIB (Mueller et al., ICML 2025) and RAVEL (Huang et al., ACL 2024) _(skim)_. The only benchmarks the Goodfire post cites, both for computation and causal localization. [MIB](https://arxiv.org/abs/2504.13151), [RAVEL](https://arxiv.org/abs/2402.17700)