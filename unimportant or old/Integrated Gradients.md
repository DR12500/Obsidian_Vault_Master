[Paper](https://arxiv.org/pdf/2404.14082#cite.sundararajan_axiomatic_2017)
short explanation:
### The Core Problem: Why Standard Gradients Fail (Saturation)

Imagine you are training a neural network to recognize an image of a digit, and a specific pixel is already so bright that pushing its value even higher cannot possibly increase the model's prediction score any further.

If you take the gradient at that final state, the local derivative drops to **zero**. Standard gradient methods will look at that zero and falsely conclude, _"This pixel had zero importance to the model's decision,"_ completely ignoring the fact that the pixel had to change from dark to bright in the first place for the model to recognize it. This is known as the **gradient saturation problem**.

### How Integrated Gradients Solves It

Integrated Gradients bypasses saturation by looking at the _entire path_ of change from a neutral baseline (like a completely black image or a zero vector) up to your actual input image, rather than just looking at the local neighborhood of the final input.

1. **Establish a Baseline (x′x'x′):** Choose a neutral baseline input where the model's prediction should theoretically be neutral or baseline (e.g., a blank black screen).
    
2. **Define a Linear Path:** Draw a straight mathematical line in input space from your baseline (x′x'x′) to your actual input (xxx).
    
3. **Accumulate Gradients:** Calculate the gradients at multiple tiny, incremental steps along that entire straight-line path.
    
4. **Integrate (Average):** Take the average of all those gradients and multiply the result by the difference between your input and the baseline (x−x′x - x'x−x′).
    

### Why This Works (Axiomatic Guarantees)

Sundararajan et al. (2017) build the method on two axioms, **Sensitivity** and **Implementation Invariance**:

- **Sensitivity:** If an input feature differs from the baseline and affects the model's prediction, it receives a non-zero attribution score. This avoids the saturation problem at the input point, but the result now depends on the choice of baseline.
    
- **Implementation Invariance:** Two networks that compute the same function (same outputs for all inputs) get the same attributions, regardless of how they are implemented internally.

In addition, IG satisfies **Completeness**: the attributions sum to the difference between the model's output on the input and on the baseline. Completeness is a property, not one of the two axioms, and other methods (e.g., DeepLIFT, LRP) satisfy it too.

The uniqueness result is narrower than "the only attribution method": IG is the unique _path_ method that is symmetry-preserving.