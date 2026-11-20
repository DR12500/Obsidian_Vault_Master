[paper](https://arxiv.org/pdf/2404.14082#cite.hubinger_chris_2019)

## Four ways in which transparency and interpretability could help
### Catching problems with auditing
- in the auditing game, one researcher takes a neural network and makes some modification to it - another researcher, given only the modified network has to diagnose the problem and figure out exactly what modification was made to the network using only interpretability tools without looking at error cases
## Deliberate design
- getting a better understanding of why a system works probably also helps in understanding their failure cases
## Giving feedback on process
- rather than just using interpretability as a mulligan at the end, you could also use it as part of your objective during training
- potentially a big boon for [informed oversight](https://ai-alignment.com/informed-oversight-18fcb5d3d1e1)
- model diffing:
	- a way of systematically comparing different models and determinig what's different from the point of view of high-level concepts and abstractions
	- track exactly how your model is evolving over the course of training in a way which is inspectable to the overseer
## Building microscopes not agents
- agentic AI introduces risks
- rather than training an RL agent, train a predictive model -> use interpretability tools to inspect it -> use knowledge to take and action in the real world (with human in the loop or in some automated way)  