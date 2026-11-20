## PROBABLY DELETE
[Paper](https://arxiv.org/pdf/2404.14082#cite.smilkov_smoothgrad_2017)
short explanation:
### The Core Problem: Why Raw Gradients Fail

When you compute a standard gradient (or saliency map) of a model's output with respect to an input, you are looking at local sensitivity—how a tiny change in a specific input feature shifts the output.

However, neural network decision surfaces are highly non-linear and riddled with microscopic, chaotic fluctuations. This causes raw gradients to contain extreme high-frequency noise. A single pixel might have a massive gradient simply because of a jagged local anomaly, rather than true semantic importance.

### How SmoothGrad Solves It ("Removing Noise by Adding Noise")

SmoothGrad bypasses this instability using a randomized averaging process:

1. **Perturbation:** It takes your original input (like an image) and creates multiple copies by adding random, zero-mean Gaussian noise (stochastic perturbation) to it.
    
2. **Gradient Calculation:** It calculates the standard gradient map for _each_ of these noisy copies independently.
    
3. **Averaging:** It averages all of those individual gradient maps together into a single final map.
    

### Why This Works

By averaging gradients across a local neighborhood of noisy variants, the random high-frequency fluctuations cancel each other out. Meanwhile, the true, stable features that _consistently_ influence the model across multiple variations are reinforced.

As Smilkov et al. demonstrated, this yields a much cleaner, visually sharper sensitivity map that highlights semantically meaningful structures rather than random background noise.