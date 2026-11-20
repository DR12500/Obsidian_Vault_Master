
also look at [[unsupervised manifold steering]]
# Methodology Guidance: Unsupervised Manifold Steering

*Based on: Exposé V2 (Nov 12, 2026), Wurgaft et al. 2026 ([arXiv:2605.05115](https://arxiv.org/abs/2605.05115)), and the local `causalab` checkout (branch `manifold_steering`, commit `1b6f43a`, May 6, 2026). Run `git pull` before starting, since newer commits may exist.*

---

## Assessment

The gap and the research questions are sound, and the codebase is a good base for this. But three issues will shape the methodology section, and the first one is the most important.

### 1. "Unsupervised" needs a precise definition

In causalab, labels are used in five places, not just one:

| Where | What the code does | Label used? |
|---|---|---|
| **Prompt selection** | The task template only produces weekday prompts | The task is known in advance |
| **PCA subspace** (`analyses/subspace/pca.py`) | Plain SVD over all prompt activations | No |
| **Centroids** (`methods/spline/builders.py: compute_centroids`) | Groups activations by causal-model variable values | **Yes** |
| **Intrinsic coordinate** (`methods/spline/train.py`) | `intrinsic_mode: pca` takes θ from the centroid PCs (atan2) and detects periodicity from eigenvalue degeneracy (`detect_periodic_dims`); `parameter` mode uses the ordinal index | Mostly no in `pca` mode, but it runs on labelled centroids |
| **Steering endpoints and evaluation** (`analyses/path_steering`, `analyses/output_manifold`) | Path pairs are centroid pairs; the behaviour manifold uses `score_token_ids` (the concept tokens) plus an "other" bin | **Yes** |

So "without labels" can mean several different things. Define a **supervision ladder** in the methodology and report every result at a named level:

- **L0 (reference):** the paper's setup, labelled centroids.
- **L1:** task prompts are known, but there are no per-prompt labels. Centroids come from clustering, and coordinates come from geometry alone.
- **L2:** no clustering either. Coordinates come directly from the point cloud (Isomap or diffusion maps), and endpoints are arbitrary points on the manifold.
- **L3:** a mixed or broad prompt pool, so you first have to discover *which* subspace holds a concept (this is where the SAE grouping, PCA or MFA step comes in).

The rule that holds this together: **labels are used for evaluation only, never for fitting.**

### 2. [27] is closer prior art than the exposé says

The exposé describes Oozeer et al. ([arXiv:2605.24942](https://arxiv.org/abs/2605.24942)) as "needs a concept-token schema." But the paper also has an unsupervised variant, **Mode 1 (GAGA-PHATE)**: the encoder is trained to match PHATE diffusion-potential distances computed on activations, with no class labels and no output-space information at training time. It runs on the same setup as Wurgaft et al. (Llama-3.1-8B-Instruct, layer 28, last prompt token).

This helps you rather than hurting you, because the result was weak:

- On behaviour fidelity (E_BC), Mode 1 is within 0.001 of linear steering on both tasks it was run on (weekdays, months).
- The authors call these PCA(64) encoders "inert".
- Mode 1 is not in the headline comparison tables; the geodesic figures are qualitative only.
- There is no discovery step: prompts come from each task's template, so the task is known in advance.
- The paper doesn't say how coordinates are named or oriented.

That makes it a natural **L1/L2 baseline**. Your contribution becomes three things:

1. unsupervised manifold steering that actually beats linear steering,
2. a discovery step (L3),
3. a naming and orientation operator.

Rewrite the gap paragraph around this.

### 3. Make the pipeline modular

The current pipeline is a single chain: SAE grouping → persistent homology → Isomap → steering. If the SAE step fails (and Bhalla et al. report that SAEs dilute manifolds), everything after it fails too. Make it two separable stages:

- **Stage A (coordinates):** given a prompt cloud, recover the manifold and its coordinates. This answers RQ1 and RQ3 at L1/L2.
- **Stage B (discovery):** find which subspace holds a concept. This is L3, with Ising-SAE grouping or PCA as alternatives.

Stage A is independently publishable and testable, and it doesn't depend on whether an SAE exists for layer 28. Check which layers the public Llama 3.1 8B SAEs actually cover before committing to that route.

### Smaller points

- **RQ4 wording:** the supervised method *does* handle 2D grids (thin-plate splines in the paper). Grids are a test of unsupervised 2D recovery against ground truth. Only the unordered concepts are a real extension, and MAxBench ([arXiv:2609.13072](https://arxiv.org/abs/2609.13072)) is the right metric there. It found manifold steering competitive with the best methods where applicable, while no method consistently beats prompting.
- **Persistent homology:** run it on the full prompt cloud, subsampled, not on 7 centroids, where it can't tell you anything.
- **Small samples:** weekdays has only 49 prompts. Use several seeds and paired tests on identical pairs. `path_steering` already writes `paired_ttest_*.json`.
- **A null control already exists:** `activation_manifold.embedding_shuffle_seed` permutes the coordinate assignment, which gives you a free label-permutation null.

---

## Step-by-step guidance

### Step 1: Reproduce L0 (weeks 1–3)

- Run `weekdays_8b_pipeline`, then months, alphabet and age (`causalab/configs/runners/`).
- The local venv is Windows-based and `scripts/run_exp.sh` is a bash script, so use WSL or call `causalab/runner/run_exp.py` directly.
- Debug on `llama32_1b_instruct` first; the model config already exists.
- Target the paper's numbers: E_BC 0.34 vs 0.93 on weekdays, and isometry r = 0.99.
- Freeze the cached activations and PCA(64) features. Every later step reuses them.

### Step 2: Build the evaluation harness (week 3)

Collect all metrics in one script so every method is scored the same way:

- **Coordinate recovery:** circular–circular correlation for cyclic concepts, Spearman for sequential ones. Both after the best rotation/reflection alignment (Isomap is only defined up to these).
- **Clustering:** ARI/NMI against the true labels.
- **Shape:** Procrustes or Hausdorff distance between the unsupervised and supervised curves.
- **Steering:** the existing `isometry`, `coherence` and `distance_from_behavior_manifold` (E_BC) scores in `causalab/methods/scores/`, plus intrinsic R².

### Step 3: L1, the minimal-change experiment (weeks 4–5)

- Replace the labels passed into `compute_centroids` with k-means or GMM cluster IDs on the PCA features. Choose k by eigengap or silhouette, and report how sensitive results are to k.
- In `pca` mode, cyclic tasks then work almost unchanged, because θ comes from atan2 and doesn't need an ordering.
- Sequential tasks (`_chord_length_parameterize`) need an ordering. Get it from a 1D Isomap coordinate or an MST path through the clusters.
- This is cheap, and it gives you a first RQ1 result early.

### Step 4: L2, coordinates without clusters (weeks 5–8)

- Fit Isomap and diffusion maps on the full prompt cloud. Check topology first with persistent homology (ripser or giotto-tda) to choose periodic vs. open.
- Fit the spline in those coordinates.
- Steering endpoints are arbitrary manifold points. For evaluation only, snap them to the nearest true label.
- Compare against GAGA-PHATE [27] (PHATE with k = 15 is easy to reimplement if they haven't released code), the L0 spline, and linear steering on E_BC (RQ3).

### Step 5: Naming operator (weeks 8–9)

Steer to a manifold point, then read the model's output. Use two variants:

- **(a)** an argmax over the concept vocabulary. This is weak supervision, so report it as such.
- **(b)** the top-k tokens over the full vocabulary, named by an LLM judge. This one is label-free.

This is what turns a recovered manifold into something you can navigate ("Monday → Wednesday").

### Step 6: Null tests, RQ2 (weeks 10–11)

- Run steps 3–4 on a randomly initialised Llama of the same architecture (or start with the 1B model).
- Add the shuffled-embedding control and a random-direction PCA control.
- Run the random-SAE control only if you get to Stage B.

### Step 7: Extensions, RQ4 and L3 (weeks 12–16, with a strict time limit)

- **2D:** run L2 on the `grid_5x5` and `cylinder_9x9` graph-walk tasks; the configs are already in the repo.
- **Unordered:** one concept (e.g. Animals) scored with MAxBench.
- **Discovery:** only if time remains, L3 on a mixed pool of weekday, month and age prompts. Can the method separate the three manifolds without being told which prompts belong to which concept?

### Step 8: Writing (month 6)

The original plan puts null tests and outlook in month 5. Moving null tests to step 6 protects RQ2, and treating step 7 as optional protects the writing month.

---

**Milestone check:** if step 3 already matches L0 on weekdays and months, the thesis is safe, and everything after it adds to the result.

## Sources

- Wurgaft, Rager, Kowal, et al. *Manifold Steering Reveals the Shared Geometry of Neural Network Representation and Behavior.* [arXiv:2605.05115](https://arxiv.org/abs/2605.05115)
- Oozeer, Raval, Quirke, Ravikiran. *Riemannian-Manifold Steering: Geometry-Aware Generative Autoencoders for Label-Free Steering.* [arXiv:2605.24942](https://arxiv.org/abs/2605.24942)
- Appapogu, Behrens, Belinkov, Mueller. *MAxBench: A Multinomial Concept Recovery Benchmark.* [arXiv:2609.13072](https://arxiv.org/abs/2609.13072)
- Bhalla, Fel, Rager, et al. *Do Sparse Autoencoders Capture Concept Manifolds?* [arXiv:2604.28119](https://arxiv.org/abs/2604.28119)
- Code: [goodfire-ai/causalab @ manifold_steering](https://github.com/goodfire-ai/causalab/tree/manifold_steering)
