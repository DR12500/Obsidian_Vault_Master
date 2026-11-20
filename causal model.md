TODO
https://arxiv.org/pdf/2605.05115
- **Counterfactuals** are input pairs that differ only in one variable (e.g. "Monday + 2" vs. "Thursday + 2").
- An **interchange intervention** runs the model on input A, copies the activations at one spot (layer, token position) from a run on input B into it, and checks whether the output changes the way the causal model predicts. If it does, that spot carries the variable.