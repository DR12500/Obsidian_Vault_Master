Literature check for [[unsupervised manifold steering]], done on 2026-11-11.

**Result:** no paper was found that both discovers concept manifolds without labels _and_ steers along the discovered manifold. The gap "discovery from activations alone, then navigation, validated causally" still holds. Repeat the check before committing to the topic.

## Closest work

### Discovery without labels (no steering)
- [ ] Francel, _SMIXAE: Towards Unsupervised Manifold Discovery in Language Models_ (2026). [arXiv 2605.09224](https://arxiv.org/abs/2605.09224) (`francel2026smixae` in the exposé)
	- Sparse MIXture of AutoEncoders: learns multi-dimensional features directly, instead of regrouping independent SAE directions after training.
	- Recovers known manifolds and reports new ones in Gemma 2 2B and 9B.
	- Early-stage, single author, submitted to the Mechanistic Interpretability Workshop at ICML 2026.
	- Activation patching and causal steering are left to future work, so **closest competitor for the discovery half**.
- [ ] Bhalla et al., _Do Sparse Autoencoders Capture Concept Manifolds?_ (2026). [arXiv 2604.28119](https://arxiv.org/abs/2604.28119) (`bhalla2026saemanifolds`)
	- Finds manifolds without labels by grouping SAE features with Ising couplings or conditional co-activation (Llama-3.1-8B).
	- Steers only along _supervised_ manifolds (between labelled centroids), not along the discovered ones.

### Steering along manifolds (needs some supervision)
- [ ] Wurgaft et al., _Manifold Steering Reveals the Shared Geometry of Neural Network Representation and Behavior_ (2026). [arXiv 2605.05115](https://arxiv.org/abs/2605.05115) (`wurgaft2026manifoldsteering`)
	- The supervised precursor: fits a curve through activation centroids that come from ground-truth labels (weekdays, months, letters, ages, in-context grids and cylinders; Llama 3.1 8B).
	- Explicitly calls for an unsupervised protocol and "an operator that allows us to navigate the manifold".
- [ ] Oozeer et al., _Riemannian-Manifold Steering: Geometry-Aware Generative Autoencoders for Label-Free Steering_ (2026). [arXiv 2605.24942](https://arxiv.org/abs/2605.24942) (`oozeer2026riemannian`)
	- Treats steering as geodesic computation, using the output-space Hellinger distance pulled back to activations through a learned encoder.
	- No labelled centroids or per-prompt labels, but the encoder is trained on a small **concept-token schema** (schema-supervised).
	- **Closest competitor for the steering half.**
- [ ] Appapogu et al., _MAxBench_ (2026). [arXiv 2609.13072](https://arxiv.org/abs/2609.13072) (`appapogu2026maxbench`)
	- Its non-linear (spline) method only works for ordered concepts with all values known; extending it to unordered concepts is left to future work.

### Related, but a different goal
- [ ] _Sampling Reveals Style: Unsupervised, Training-Free Discovery of Prompt-Conditional Stylistic Axes in LLM Activations_ (2026). [arXiv 2609.19150](https://arxiv.org/abs/2609.19150)
	- Finds stylistic _axes_ (directions, not manifolds) without labels, using stochastic decoding and PCA.
	- Only seen in search results, not read yet.

## How the thesis would differ
| Work | Without labels | Curved manifold | Steers along discovered structure |
|---|---|---|---|
| Wurgaft et al. | no | yes | supervised manifolds only |
| Bhalla et al. | yes | yes | no |
| SMIXAE | yes | yes | no (future work) |
| Oozeer et al. | partly (schema) | yes | yes |
| MAxBench spline | no | yes (ordered concepts) | yes |
| **This thesis** | **yes** | **yes** | **yes** |
