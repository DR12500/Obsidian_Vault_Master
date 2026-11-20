also look at [[methodology_guidance]]
**Why it’s novel**

- Wurgaft et al. (2605.05115) fit manifolds with ground-truth labels and explicitly call for an unsupervised protocol, plus an operator to navigate the manifold.
- Bhalla et al. discover manifolds without labels (Ising-SAE clustering; on synthetic data the recovered groups reconstruct topology and geometry without supervision), but they only steer along supervised manifolds (between labelled centroids), not along the discovered ones.
- MAxBench’s nonlinear methods only handle ordered concepts with all values known.
- Closest competitor: Riemannian-manifold steering (Oozeer et al., 2605.24942). It needs no labelled centroids, but still needs a concept-token schema (which output tokens belong to the concept).
- Open gap: discovery from activations alone, then navigation, validated causally.
- Run a fresh literature check before committing. (Checked 2026-11-11, see [[Related Work]]: closest new work is SMIXAE, which discovers without labels but does not steer yet; the gap still holds.)

**Why it’s doable**

- Ground truth exists: supervised results for weekdays, months, letters, ages, and 2D in-context grids and cylinders.
- Public code: Goodfire causalab, manifold_steering branch.
- Llama 3.1 8B fits on one 24–48 GB GPU; develop on a smaller model.
- Reuses earlier ideas: persistent homology identifies the shape; Isomap or diffusion maps give intrinsic coordinates without labels.

**Research questions**

1. Can manifolds and their coordinates be recovered without labels, and how close are they to the supervised fits?
2. Does unsupervised steering match supervised steering on the paper’s naturalness score?
3. Does it work where supervised methods fail (unordered concepts, 2D grids)?
4. Does it replicate on a second model family?

**Pipeline:** activations → candidate group (Ising-SAE or PCA baseline) → topology via persistent homology → intrinsic coordinates via manifold learning → steering → compare with supervised and linear steering.