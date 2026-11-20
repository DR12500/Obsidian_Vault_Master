Summary of the current exposé (`expose/expose_written.tex`, as of 2026-11-13). Hand-in PDF: `expose_Dominik_Rittner.pdf`.

## Topic
**Unsupervised Manifold Steering: Discovering and Navigating Concept Manifolds in LLMs without Labels**
- Supervisors: Prof. Dr. Sandipan Sikdar, Tobias Kalmbach (LUH, KBS)
- Details in [[unsupervised manifold steering]] and [[Related Work]]

## Core idea in one paragraph
Many concepts in LLMs are not single directions but curved, low-dimensional shapes (manifolds), e.g. weekdays and months form circles. Steering along a straight line cuts through such a shape and produces unnatural outputs, while steering along the manifold moves smoothly (Monday → Tuesday → Wednesday). Current manifold steering needs ground-truth labels. The thesis aims to **find concept manifolds without labels and steer along them**, and to check causally that the model actually uses them.

**In one sentence:** turn manifold steering from a supervised into an unsupervised method, so that it also works for concepts nobody has labelled (e.g. unordered ones like animals or countries).

**Where the idea comes from:** Wurgaft et al. (2026), Future Work, "Fitting the geometry". Their spline fit needs ground-truth coordinates, and they write that "an unsupervised protocol would however significantly broaden the applicability of our methods". As a starting point they cite the manifold-learning literature: diffusion maps (Coifman & Lafon 2006), *Diffusion Geometry* (Jones 2024), *Computing Diffusion Geometry* (Jones & Lanners 2026) and Meilă & Zhang (2023).
- The exposé uses diffusion maps, but it does **not** mention diffusion geometry yet.
- From the abstracts only, diffusion geometry claims to be more robust to noise than persistent homology. It can compute geodesic distances, curvature and circular coordinates directly from data, so it could serve both the topology step and the coordinate step.

## Background (Section 3)
- **Linear representation hypothesis:** a concept = a direction. SAEs are built on this view (Park et al. 2024; Bhalla et al. 2026).
- **Not all features are linear:** weekday/month circles in GPT-2 and Mistral 7B, used for modular arithmetic (Engels et al., ICLR 2025). Curved counting manifolds also appear in Claude 3.5 Haiku (Gurnee et al. 2025).
- **Theory:** features as metric spaces; on-manifold distances match feature distances up to a constant (Modell et al. 2025).
- **Where shapes come from:**
	- Data: translation symmetry in word co-occurrence produces circles (Karkada et al. 2026).
	- Superposition: correlated features interfere constructively (BOWS, Prieto et al., ICLR 2026).
	- Training choices (bottlenecks, weight decay) also matter.
- **Three possible sources of a shape:** data, architecture/training, or the analysis method itself. Cheap tests:
	- a random model
	- PMI of the concept tokens
	- Pythia checkpoints
	- transformer vs. Mamba

## Methods (Section 4)
- **SAEs:**
	- They fragment ("dilute") manifolds over many features.
	- Low reconstruction error does not mean the manifold was recovered.
	- Manifolds can be found by grouping features via Ising couplings or co-activation (Bhalla et al. 2026).
	- Random SAEs nearly match trained ones (Korznikov et al., ICML 2026 MI workshop).
- **MFA** (Shafran et al., ICML 2026): local Gaussian regions (centroid + small local subspace), unsupervised, steers better than SAEs. This makes it a strong baseline and the closest overlap with the thesis.
	- **How it steers:** it pulls the activation toward a region's centroid, x → (1−α)·x + α·μ ("jump into the Wednesday region"). A second option adds a shift along the region's local directions, but the steering experiments use only the centroid.
	- **What it does not do:**
		- no steering *along* a curved path between regions (no geodesics, no manifold steering)
		- no global shape (it doesn't find that the weekday regions form a closed loop)
		- no coordinates along a concept
	- **Partial grouping:** it links nearby centroids in a nearest-neighbour graph and finds local "constellations" of regions with the same theme (61% of regions have at least one such neighbour). These are local clusters, not a global manifold.
	- **Labels:** regions are named afterwards by GPT-5-mini from example texts. The fitting is unsupervised, but the interpretation is not. The thesis faces the same issue when matching coordinates to concept values.
	- **Consequence for the thesis:** if the evaluation only asks "can I set the concept to value X?", MFA may already be enough. The contribution has to show where geometry matters:
		- smooth paths between values (naturalness along the route)
		- global topology
		- unordered concepts
	- MFA regions could serve as the building blocks for the grouping step.
- **SMIXAE:** finds manifolds without labels but does not steer yet (Francel 2026). Closest competitor for discovery.
	- implemented within SAELens
- **Topology:** persistent homology tests whether a loop is real (giotto-tda).
- **Manifold steering** (Wurgaft et al., ICML 2026 MI workshop):
	- Activation and behaviour manifolds match (scaled isometry).
	- Linear steering "teleports"; manifold steering is more natural (e.g. 0.34 vs. 0.93 on weekdays).
	- It is supervised, and the authors ask for an unsupervised protocol.

## Evaluation (Section 5)
- **AxBench:** binary concepts. Prompting steers best, and SAEs are not competitive.
- **MAxBench:** multinomial concepts, geometry-agnostic scoring (exclusivity + completeness). Its manifold method only works for ordered concepts, which is the gap for RQ4.
- **SAEBench:** eight metrics, but it scores features one at a time (linear view).

## Research gap (Section 6)
Discovery from activations alone, then navigation, then validation. No paper does all three yet (literature check 2026-11-11).

## Research questions
1. Can concept manifolds and their coordinates be recovered without labels, and how do they compare to supervised fits?
2. Do they survive null tests (random model, random SAE), i.e. do they reflect the data rather than the architecture or method?
3. How does steering along unsupervised manifolds compare to supervised manifold steering and linear steering?
4. To what extent does the approach extend to concepts where supervised methods do not apply (unordered concepts, 2D grids)?

## Pipeline
Activations → candidate group (Ising-SAE grouping or PCA) → topology (persistent homology) → intrinsic coordinates (Isomap / diffusion maps) → steering → comparison with supervised and linear steering

1. **Grouping step:** decide which features or regions belong to the *same* concept. Activations mix thousands of concepts, so this has to come first. Options:
	- SAE features grouped by Ising couplings / co-activation (Bhalla et al.)
	- PCA as a baseline
	- MFA regions and their local constellations
2. **Topology:** is there really a loop, sphere, …? Tools: persistent homology, or possibly diffusion geometry.
3. **Coordinates:** intrinsic coordinates without labels (Isomap, diffusion maps, possibly diffusion geometry with circular coordinates).
4. **Steering** along these coordinates, compared with supervised manifold steering, linear steering and MFA. Null tests (random model, random SAE) check that the shape is not an artifact.

The hardest part is probably the step from 3 to 4: coordinates found without labels must first be matched to concept values (which point is "Monday"?).

- **Challenge:** label-free coordinates have no names or orientation, so each point must be matched to a concept value (e.g. by decoding through the model's output).
- **Risk:** Isomap's nearest-neighbour graph can short-circuit on noisy data, so diffusion maps serve as the comparison.
- **Feasibility:**
	- Ground truth for evaluation comes from the supervised results and from Bhalla's synthetic manifold zoo.
	- Code exists (Goodfire `causalab`, branch `manifold_steering`).
	- Llama 3.1 8B fits on one 24–48 GB GPU.

## Work plan (6 months)
- Months 1–2: literature, reproduce supervised manifold steering
- Months 2–3: discovery on synthetic data, then Llama
- Months 3–4: unsupervised coordinates and steering, comparison
- Month 5: null tests, outlook experiments
- Month 6: writing

## Outlook (Section 8)
- **Safety:**
	- Graded behaviours (refusal, sycophancy, persuasion) could be steered smoothly.
	- Backdoor triggers leave a topological signature (Fay et al., ICLR 2026).
	- SAEs lost to linear probes on a safety task.
- **Emotions:** valence–arousal circle as an unordered/2D concept (Sun et al. 2026).
- **Other model families:** Gemma, Qwen, or Mamba, to test whether the results generalise.

## Still open
- [ ] Work plan: "Month 5: Outlook, null tests" wording, and no slot for RQ4 yet
- [ ] SAEBench: decide which metrics to use (RAVEL missing from the capability list)
- [ ] Optional: short summary at the top of the exposé; one opening sentence per section
- [ ] Read Jones 2024 / Jones & Lanners 2026 (diffusion geometry) and decide whether to add them to the exposé (pipeline + where the idea comes from)
- [ ] Exposé: say more clearly how the thesis differs from MFA (local regions + jumping vs. global shape + moving along it)
- [ ] Check whether the MFA paper cites Wurgaft et al. (the reference list was cut off when I read it)
