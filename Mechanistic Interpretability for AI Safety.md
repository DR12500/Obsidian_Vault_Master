#### to be solved:
- are there alternatives to RL(HF), that make achieving the goal better? -> [[Representation Engineering]] is related, but Zou et al. present it as "a top-down approach to AI transparency" (reading and steering representations), not as a replacement for RLHF

## [Mechanistic Interpretability for AI Safety A Review](https://arxiv.org/pdf/2404.14082)
### spontaneous definition:
reverse engineering the computational
mechanisms and representations learned by neural networks into human-understandable
algorithms and concepts to provide a granular, causal understanding
#### Interpretability Paradigms from the Outside In
###### Behavioral
- analyzing input-output relations
- **Techniques**:
	- [minimal pair analysis](https://arxiv.org/pdf/2404.14082#cite.warstadt_blimp_2020)
	- [sensitivity and pertubation analysis](https://arxiv.org/pdf/2404.14082#cite.casalicchio_visualizing_2018) 
	- assessing model robustness and variable dependencies ([1](https://arxiv.org/pdf/2404.14082#cite.shapley_value_1988), [2](https://arxiv.org/pdf/2404.14082#cite.ribeiro_why_2016), [3](https://arxiv.org/pdf/2404.14082#cite.covert_explaining_2021))
- [practical for complex or proprietary models](https://arxiv.org/pdf/2404.14082#cite.jumelet_evaluating_2023)
- **but:** [lacks insight into internal decision processes and causal depth](https://arxiv.org/pdf/2404.14082#cite.jumelet_evaluating_2023)
###### Attributional
- aims to explain outputs by tracing predictions to individual input contributions using gradients
- **technique**: raw gradients can be discontinuous or sensitive to slight pertubations -> techniques such as [[SmoothGrad]] and [[Integrated Gradients]] average across gradients
	- other popular techniques:
		- [layer-wise relevance propagation](https://arxiv.org/pdf/2404.14082#cite.bach_pixelwise_2015)
		- [DeepLIFT](https://arxiv.org/pdf/2404.14082#cite.shrikumar_learning_2017)
		- [GradCAM](https://arxiv.org/pdf/2404.14082#cite.selvaraju_gradcam_2016) 
- enhances transparancy by showing input feature influence without requiring an understanding of internal structure, enabling decision validation, compliance and trust while serving as a bias detection tool, **but** have fundamental limitations ([source](https://arxiv.org/pdf/2404.14082#cite.bilodeau_impossibility_2024))
###### Concept-based
- adopts a top-down approach to unraveling a model's decision-making processes by probing its learned representations for high-level concepts and patterns governing behavior
- **techniques**:
	- [training supervised auxiliary classifiers](https://arxiv.org/pdf/2404.14082#cite.belinkov_probing_2021)
	- [employing unsupervised contrastive and structured probes to explore latent knowledge](https://arxiv.org/pdf/2404.14082#cite.burns_discovering_2023)
	- using neural representation analysis to quantify the representational similarities between the internal representations learned by different neural network ([1](https://arxiv.org/pdf/2404.14082#cite.kornblith_similarity_2019), [2](https://arxiv.org/pdf/2404.14082#cite.kornblith_similarity_2019))
	- Can also enable manipulation of these representations ([[Representation Engineering]]) -> potentially enhancing safety by upregulationg concepts such as honesty harmlessness, and morality
###### Mechanistic
- bottom-up approach that studies the fundamental components of models through granular analysis of features, neurons, layers, and connections, offering an intimate view of operational mechanics. It aims to uncover causal relationships and precise computations transforming inputs into outputs, often identifying specific neural circuits driving behavior 
- draws from interdisciplinary fields like physics, neuroscience and systems biology to guide developments of transparent, value-aligned AI systems.

#### The following is not inherently part of the paper (at least so far): 
##### perhaps combining different kinds of Interpretability with Mechanistic Interpretability is a directions worth investigating in. Gemini mainly provided the following examples:
- **Concept-Based + Attribution** 
- **Mechanistic + Behavioral (Validation Loops)**
- **Representation Engineering + Attribution** 
- _Not missing in the paper (correction):_ both are covered by Bereska & Gavves, see "Dissecting Models into Interpretable Circuits" and "Developmental interpretability" further down in this note
	- Mechanistic Interpretability (Reverse-Engineering Circuits)
	- Developmental Interpretability
#### additional information:
- **Sparse Autoencoders (SAEs)** have become the primary workhorse for scaling mechanistic interpretability. They act like a specialized microscope designed to solve one of the biggest bottlenecks in studying large neural networks: **polysemanticity**.

#### Some ideas/papers regarding the link between [[Mechanistic Interpretability and Geometry]]
### Back to the paper...
#### features
- A key property of features is their irreducibility, meaning they cannot be decomposed into or expressed as a combination of simpler, independent factors
- in 3.1 to 3.2 features get defined as directions in activation space and this to to be the fundamental units of nn representation
#### Exploring Polysemanticity: Hypotheses and Implications.
##### To understand the widespread occurrence of polysemanticity in neural networks, several hypotheses have been proposed
- Hypothesis 1: **Superposition** -> Neural networks represent more features than they have neurons by encoding features in overlapping combinations of neurons
- Hypothesis 2: **Linear Representation** -> Features are directions in activation space, i.e., linear combinations of neurons
- Hypothesis 3: **Weak Universality** -> There are underlying principles governing how neural networks learn to solve certain tasks. Models will generally converge on analogous solutions that adhere to the common underlying principles. However, the specific features and circuits that implement these principles can vary across different models based on factors like hyperparameters, random seeds, and architectural choices
- Hypothesis 4: **Strong Universality** -> The same core features and circuits will universally and consistently arise across all neural network models trained on similar tasks and data distributions and using similar techniques, reflecting a set of fundamental computational motifs that neural networks inherently gravitate towards when learning.
- Hypothesis 5: **Simulation** -> A model whose objective is text prediction will simulate the causal processes underlying the text creation if optimized sufficiently strongly
- Hypothesis 6: **Prediction Orthogonality** -> A model whose objective is prediction can simulate agents who optimize toward any ojectives with any defree of optimality

#### Feature Disentanglement via Sparse Dictionary Learning
- this process, known as sparse dictionary learning, reconstructs activation vectors as sparse linear combinations of directional vectors within the activation space

### Intervention
#### Activation Patching
**two directions**:
- clean to corrupted (causal tracing)
- corrupted to clean (resample ablation)
#### Causal Abstraction
- mathematical framework for mechanistic interpretability
- methods: 
	- [LIME](https://arxiv.org/pdf/2404.14082#cite.ribeiro_why_2016)
	- [causal effect estimation](https://arxiv.org/pdf/2404.14082#cite.feder_causalm_2021)
	- [causal mediation analysis](https://arxiv.org/pdf/2404.14082#cite.vig_investigating_2020)
	- [iterated nullspace projection](https://arxiv.org/pdf/2404.14082#cite.ravfogel_null_2020)
	- [circuit-based explanations](https://arxiv.org/pdf/2404.14082#cite.geiger_causal_2023)
- [distributed alignment search](https://arxiv.org/pdf/2404.14082#cite.geiger_finding_2023) introduced gradient-based distributed interchange interventions, [extending causal abstraction to larger models](https://arxiv.org/pdf/2404.14082#cite.wu_interpretability_2023)
- [Causal proxy models](https://arxiv.org/pdf/2404.14082#cite.wu_causal_2023), which address the challenge of counterfactual observations
- Applications of causal abstraction:
	- linguistic phenomena analysis([1](https://arxiv.org/pdf/2404.14082#cite.arora_causalgym_2024), [2](https://arxiv.org/pdf/2404.14082#cite.wu_causal_2022))
	- [evaluation of interpretability methods](https://arxiv.org/pdf/2404.14082#cite.huang_ravel_2024)
	- [improving performance through representation finetuning](https://arxiv.org/pdf/2404.14082#cite.wu_reft_2024) 
	- [improving efficiency via model distillation](https://arxiv.org/pdf/2404.14082#cite.wu_causal_2022)
#### Hypothesis Testing
- [causal scrubbing](https://arxiv.org/pdf/2404.14082#cite.chan_causal_2022)
- [Locally consistent abstractions](https://arxiv.org/pdf/2404.14082#cite.jenner_comparison_2023)
### Integrating Observation and Intervention
- to understand internal neural network mechanisms, combining observational and interventional methods is crucial
- Sparse Autoencoders can be used to "disentangle superposed features"
- integrating sparse autoencoders with automated circuits discovery ([1](https://arxiv.org/pdf/2404.14082#cite.conmy_automated_2023), [2](https://arxiv.org/pdf/2404.14082#cite.syed_attribution_2023))
- 
# Current Research
-> Three domains within Mechanistic Interpretability
## Intrinsic Interpretability
- encourages sparsity, modularity and monosemanticity, to make the reverse enginering process more tractable
- methods:
	- engineer models to be more monosemantic ([1](https://arxiv.org/pdf/2404.14082#cite.jermyn_engineering_2022), [2](https://arxiv.org/pdf/2404.14082#cite.elhage_softmax_2022))
	- regularization with spatial structure, akin to the organization of information in the human brain ([brain-inspired modular training](https://arxiv.org/pdf/2404.14082#cite.liu_growing_2023))
	- [sparse attention](https://arxiv.org/pdf/2404.14082#cite.zhang_sparse_2021)
	- adding L1 penalties ([1](https://arxiv.org/pdf/2404.14082#cite.kasioumis_elite_2021), [2](https://arxiv.org/pdf/2404.14082#cite.georgiadis_accelerating_2019))
	- [pruning neurons](https://arxiv.org/pdf/2404.14082#cite.frankle_lottery_2019)
## Developmental interpretability
- [distinct field, but intersects with mechanistic interpretability](https://arxiv.org/pdf/2404.14082#cite.olsson_incontext_2022)
- key motivation: investigate the universality of safety-critical ptterns, aiming to understand how deeply ingrained and thereby resistant to safety fine-tuning capabilities like deception are
- _Grokking might be interesting to investigate in connection to this_ 

## Post-Hoc Interpretability
- key distinctions between:
	- global vs local interpretability
	- comprehensive vs partial interpretability
#### global
- aims to uncover general patterns and behaviors of a model, providing insights that apply broadly accross many instances ([1](https://arxiv.org/pdf/2404.14082#cite.doshi-velez_rigorous_2017), [2](https://arxiv.org/pdf/2404.14082#cite.nanda_how_2023))
#### local
- explains the reasons behind a model's decisions for particular instances, offering insights into individual predictions or behaviors
#### comprehensive 
- [involves achieving a deep understanding of a model's behavior, providing a holistic view of its inner working](https://arxiv.org/pdf/2404.14082#cite.nanda_how_2023)
#### partial
- often applied to larger and more complex models
- concentrates on interpreting specific aspects or subsets of the model's behavior, focusing on the application's most relevant or critical ares

- Circuit-style mechanistic interpretability aims to explain neural networks by reverse engineering the underlying mechanisms at the level of individual neurons or subgraphs
- for LLMs this often involves investigations into behaviors like 
	- [multiple choice reasoning ](https://arxiv.org/pdf/2404.14082#cite.lieberum_does_2023)
	- [indirect object identification](https://arxiv.org/pdf/2404.14082#cite.wang_interpretability_2023)
	- [computing operations](https://arxiv.org/pdf/2404.14082#cite.hanna_how_2023)
	- [analyzing circuits for python docstrings](https://arxiv.org/pdf/2404.14082#cite.hanna_how_2023)
	- ["an" vs "a"](https://arxiv.org/pdf/2404.14082#cite.miller_we_2023)
	- [price tagging](https://arxiv.org/pdf/2404.14082#cite.wu_interpretability_2023)

- Even simple arithmetic operations can involve complex strategies and multiple algorithmic solution. Characterizing these algorithms helps test hypotheses around generalizable mechanisms like
	- [variable binding](https://arxiv.org/pdf/2404.14082#cite.feng_how_2023)
	- [arithmetic resoning](https://arxiv.org/pdf/2404.14082#cite.stolfo_mechanistic_2023)
#### Automation: Scaling Post-Hoc Interpretability
- As model become more complex, automating key aspects of the interpretability workflow becomes increasingly crucial.
##### Dissecting Models into Interpretable Circuits
- The first major automation challenge is identifying the critical computational sub-circuits or components underpinning a model's behavior for a given task
- _Methods for iterative removal of network components:_
	- [automated circuit discovery](https://arxiv.org/pdf/2404.14082#cite.conmy_automated_2023)
	- attribution patching ([1](https://arxiv.org/pdf/2404.14082#cite.syed_attribution_2023), [2](https://arxiv.org/pdf/2404.14082#cite.kramar_atp_2024))
  - _Top-down approach methods:_
	  - [high-level causal properties that components solving a target subtask should satisfy and then learn binary masks to expose those component subsets](https://arxiv.org/pdf/2404.14082#cite.davies_discovering_2023)
	  - [information flow graphs](https://arxiv.org/pdf/2404.14082#cite.ferrando_information_2024)
##### Interpreting Extracted Circuits
- A Novel Paradigm uses LLMs themselves as an interpretive tool ([1](https://arxiv.org/pdf/2404.14082#cite.bills_language_2023), [2](https://arxiv.org/pdf/2404.14082#cite.mousi_can_2023), [3](https://arxiv.org/pdf/2404.14082#cite.bai_describeanddissect_2024))
- [graph-based approach](https://arxiv.org/pdf/2404.14082#cite.foote_neuron_2023)
- [auto-distilling the algorithm learned by a trained model into human-readable Python code](https://arxiv.org/pdf/2404.14082#cite.michaud_opening_2024)

# Relevance to AI Safety
- inner misalignment (model's behavior deviates from its intended goals) and mesa-optimization (the emergence of unintended subagents within the model) _([1](https://arxiv.org/pdf/2404.14082#cite.hubinger_risks_2019), [2](https://arxiv.org/pdf/2404.14082#cite.vonoswald_uncovering_2023))_
- [[may trigger normative shifts within AI community toward rigorous safety protocols]]
- locating and erasing sensitive information stored in the model ([1](https://arxiv.org/pdf/2404.14082#cite.meng_locating_2022), [2](https://arxiv.org/pdf/2404.14082#cite.nguyen_survey_2022))
- Interpretability can provide safety filters for every stage of training
	- [before](https://arxiv.org/pdf/2404.14082#cite.hubinger_chris_2019)
	- during ([1](https://arxiv.org/pdf/2404.14082#cite.hubinger_transparency_2022), [2](https://arxiv.org/pdf/2404.14082#cite.sharkey_circumventing_2022)
	- after 
		- [evaluation of artificial cognition for honesty](https://arxiv.org/pdf/2404.14082#cite.burns_discovering_2023), [2](https://arxiv.org/pdf/2404.14082#cite.zou_representation_2023)
		- [screening for deceptive behaviors](https://arxiv.org/pdf/2404.14082#cite.park_ai_2023)
- [deceptive alignment](https://arxiv.org/pdf/2404.14082#cite.park_ai_2023)
- [eliciting latent knowledge](https://arxiv.org/pdf/2404.14082#cite.christiano_eliciting_2021)
- [iterative distillation and amplification](https://arxiv.org/pdf/2404.14082#cite.chan_what_2023)
- [well-founded AI](https://arxiv.org/pdf/2404.14082#cite.tegmark_provably_2023)
- [microscope AI](https://arxiv.org/pdf/2404.14082#cite.hubinger_chris_2019)
- [retargeting an internal search process](https://arxiv.org/pdf/2404.14082#cite.wentworth_how_2022)
## RISKS
- potential misalignment risks, ranging from acute, model-centric issues to gradual systemic concerns ([source](https://arxiv.org/pdf/2404.14082#cite.kulveit_risks_2024))

# Challenges
##### Need for Comprehensive, Multi-Pronged Approaches
1. Coordinating observational (e.g. probing, logit lens) and interventional methods (e.g. activation patching) to establish causal relationships
2. Combining feature-level analysis (e.g. sparse autoencoders) with circuit-level interventions (e.g. path patching) to uncover representation-mechanism interplay
3. Integrating intrinsic interpretability approaches with post-hoc analysis for robust understanding
##### Cherry-Picking and Streetlight Interpretability
- Examining AI systems under only ideal conditions of maximal interpretability, risking missing critical phenomena that only emerge in more realistic and diverse contexts

##### Obstacles to Bottom-Up Interpretability
- model representations might naturally converge to more human-interpretable forms as capability increases ([1](https://arxiv.org/pdf/2404.14082#cite.hubinger_chris_2019), [2](https://arxiv.org/pdf/2404.14082#cite.feng_how_2023))

##### Analyzing Models Embedded in Environments
- Real-world AI systems embedded in rich, interactive environments exhibit two forms of in-context behavior that pose significant interpretability challenges beyond understanding models in isolation
	- [externally](https://arxiv.org/pdf/2404.14082#cite.leahy_barriers_2023)
	- [internally (the hydra effect)](https://arxiv.org/pdf/2404.14082#cite.mcgrath_hydra_2023)
##### Adversarial Pressure Against Interpretability
- deceptive behaviors ([1](https://arxiv.org/pdf/2404.14082#cite.sharkey_circumventing_2022), [2](https://arxiv.org/pdf/2404.14082#cite.hubinger_transparency_2022))

# Future Directions
### Clarifying Concepts
- Corroborate or refutre core assumptions
- Integrate existing literature and terminology
##### Neurosymbolic Reasoning and Program Synthesis
- [source](https://arxiv.org/pdf/2404.14082#cite.riegel_logical_2020)
- creating programs in domain-specific languages ([1](https://arxiv.org/pdf/2404.14082#cite.verma_programmatically_2019), [2](https://arxiv.org/pdf/2404.14082#cite.trivedi_learning_2021))
- [extracting decision trees](https://arxiv.org/pdf/2404.14082#cite.zhang_interpreting_2019)
- [extracting causal graphs](https://arxiv.org/pdf/2404.14082#cite.ren_defining_2023)
##### Causal Representation Learning 
- aims to discover and disentangle underlying causal factors in data ([source](https://arxiv.org/pdf/2404.14082#cite.scholkopf_causal_2021))
- could enhance interpretability by identifying causal relationships between neurons or layers ([1](https://arxiv.org/pdf/2404.14082#cite.bengio_metatransfer_2019), [2](https://arxiv.org/pdf/2404.14082#cite.ke_systematic_2021))
- its focus on interventions and counterfactuals ([1](https://arxiv.org/pdf/2404.14082#cite.bengio_metatransfer_2019), [2](https://arxiv.org/pdf/2404.14082#cite.ke_systematic_2021)) could inspire new methods for probing model internals ([1](https://arxiv.org/pdf/2404.14082#cite.goyal_recurrent_2020), [2](https://arxiv.org/pdf/2404.14082#cite.besserve_counterfactuals_2019))
- CRL's emphasis on learning invariant representations ([1](https://arxiv.org/pdf/2404.14082#cite.peters_causal_2015), [2](https://arxiv.org/pdf/2404.14082#cite.kugelgen_semisupervised_2019)) could guide the search for robust features, while its approach to transfer learning ([1](https://arxiv.org/pdf/2404.14082#cite.rojas-carulla_invariant_2018), [2](https://arxiv.org/pdf/2404.14082#cite.magliacane_domain_2018)) could inform studies into model generalization.
##### Trojan Detection
- Quantifying progress is challenging due to the lack of evidence for deception as an emergent capability in current models ([source](https://arxiv.org/pdf/2404.14082#cite.jacob_emergent_2023)), apart from sycophancy ([1](https://arxiv.org/pdf/2404.14082#cite.lang_when_2024), [2](https://arxiv.org/pdf/2404.14082#cite.denison_sycophancy_2024)) and theoretical evidence for [deceptive inflation behavior](https://arxiv.org/pdf/2404.14082#cite.lang_when_2024).
- [detecting trojans](https://arxiv.org/pdf/2404.14082#cite.hubinger_sleeper_2024) implanted via data poisoning could be a proxy goal and proof-of-concept
- trojans simulate **outer misalignment** (or reward hacking, occurs when the specified reward function or utility function fails to capture the desired objectives correctly. This leads the AI to optimize for behaviors that achieve high reward scores but are misaligned with the intended outcomes) rather than **inner misalignment** (or goal misgeneralization; occurs when an AI system develops goals or behaviors during training that are misaligned with the intended objectives despite a correctly specified reward signal)
##### Adversarial Robustness
- duality between interpretability and adversarial robustness: [More interpretable models tend to be more robust against adversarial attacks](https://arxiv.org/pdf/2404.14082#cite.jyoti_robustness_2022) and [vice versa](https://arxiv.org/pdf/2404.14082#cite.engstrom_adversarial_2019)
- input gradient regularization have been shown to simultaneously improve interpretability of saliency maps AND enhance adversarial robustness ([1](https://arxiv.org/pdf/2404.14082#cite.ross_improving_2017), [2](https://arxiv.org/pdf/2404.14082#cite.du_fighting_2021))
- interpretability tools can help create more sophisticated adversaries ([1](https://arxiv.org/pdf/2404.14082#cite.carter_activation_2019), [2](https://arxiv.org/pdf/2404.14082#cite.casper_robust_2021))
- **linear representation hypothesis** treats activation directions as features, but the emergence and necessity of linearity is unclear - is it architectural bias or inherent? Stronger theory justifying linearity’s necessity or counter-examples like autoencoders on uncorrelated data without [intermediate linear layers](https://arxiv.org/pdf/2404.14082#cite.elhage_toy_2022) are needed -> An alternative lens views features as polytopes from piecewise linear activations ([source](https://arxiv.org/pdf/2404.14082#cite.black_interpreting_2022))
- **superposition hypothesis** suggets that polysemantic neurons arise from the network compressing and representing many features within its limited set of neurons, but polysemanticity can also occur incidentally due to redundancy. It also raises open questions: 
	- operationalizing computation in superposition?
	- attention head superposition? 
	- representing feature clusters
	- connections to adversarial robustness
	- anti-correlated feature organization
	- architectural effects
### Setting Standards
- Prioritize robustness over capability advancement (focusing on adversarial tasks such as backdoor detection and removal ([1](https://arxiv.org/pdf/2404.14082#cite.lamparth_analyzing_2023), [2](https://arxiv.org/pdf/2404.14082#cite.hubinger_sleeper_2024), [3](https://arxiv.org/pdf/2404.14082#cite.wu_backdoorbench_2022)) and evaluating the accuracy of explanations in producing adversarial examples ([source](https://arxiv.org/pdf/2404.14082#cite.goldowsky-dill_localizing_2023)))
- Establish metrics, benchmarks, and algorithmic testbeds 
	- goodhart's law: When a measure becomes a target, it ceases to be a good measure
	- to advance the field, rigorous evaluation methods are needed. These should include:
		1. assessing out-of-distribution inputs
		2. controlling systems through edits, such as implanting or removing trojans or targeted editing
		3. replacing components with simpler reverse-engineered alternatives
		4. comprehensive evaluation through replacing components with hypothesized circuits
	- tools like [Tracr](https://arxiv.org/pdf/2404.14082#cite.lindner_tracr_2023) can provide ground truth labels for benchmarking search methods ([source](https://arxiv.org/pdf/2404.14082#cite.goldowsky-dill_localizing_2023)) while toy models [studying superposition in computation](https://arxiv.org/pdf/2404.14082#cite.vaintrob_mathematical_2024) and transformers on algorithmic tasks can quantify sparsity and test intrinsic methods.
	- Introduction([1](https://arxiv.org/pdf/2404.14082#cite.thurnherr_tracrbench_2024), [2](https://arxiv.org/pdf/2404.14082#cite.gupta_interpbench_2024)) of datasets of transformer weights with known circuits for evaluating mechanistic interpretability techniques
### Scaling up
- Coverage and complexity
	- pushing the pareto frontier between model and task complexity ([source](https://arxiv.org/pdf/2404.14082#cite.chan_what_2023))
		- future work should aim for thorough [reverse engineering](https://arxiv.org/pdf/2404.14082#cite.quirke_understanding_2023), [integrating proven modules into larger networks](https://arxiv.org/pdf/2404.14082#cite.nanda_progress_2023), and [capturing sequences encoded in hidden states beyond immediate predictions](https://arxiv.org/pdf/2404.14082#cite.pal_future_2023)
		- Deepening analysis complexity is also key, [validating the realism of toy models](https://arxiv.org/pdf/2404.14082#cite.elhage_toy_2022) and extending techniques like path patching ([1](https://arxiv.org/pdf/2404.14082#cite.goldowsky-dill_localizing_2023), [2](https://arxiv.org/pdf/2404.14082#cite.liu_seeing_2023)) to LLMs
- Universality and overarching theories
	- transitioning from isolated empirical findings to developing overarching theories and universal reasoning primitives beyond specific circuits, aiming for a comprehensive understanding of AI capabilities.
	- Identifying universal reasoning patterns and unifying theories is key to advancing interpretability
- Automation techniques
	- manual circuit identification is labor-intensive, so automated techniques like circuit discovery and sparse autoencoders can enhance the process ([1](https://arxiv.org/pdf/2404.14082#cite.foote_neuron_2023), [2](https://arxiv.org/pdf/2404.14082#cite.nanda_200tool_2023)). Future work should automatically create varying datasets for understanding circuit functionality, develop automated hypothesis search, and investigate atten head/MLP interplay
### Expanding Scope
- During and before training
	- by studying learning dynamics ([1](https://arxiv.org/pdf/2404.14082#cite.nanda_200dynamics_2022), [2](https://arxiv.org/pdf/2404.14082#cite.elhage_toy_2022), [3](https://arxiv.org/pdf/2404.14082#cite.hubinger_transparency_2022))
	- [tracking neuron development](https://arxiv.org/pdf/2404.14082#cite.liu_probing_2021)
	- [analyzing neuron set changes with scale](https://arxiv.org/pdf/2404.14082#cite.michaud_quantization_2023)
	- [investigating emergent computations](https://arxiv.org/pdf/2404.14082#cite.quirke_understanding_2023)
	- [studying phase transitions could yield safety insights for reward hacking risks](https://arxiv.org/pdf/2404.14082#cite.olsson_incontext_2022)
- Top-down and Hybrid
	- complementing the predominant bottom-up methods, mechanistic interpretability should explore top-down and hybrid approaches
	- top-down offers a tractable way to study large models and guide [microscopic research with macroscopic observations](https://arxiv.org/pdf/2404.14082#cite.variengien_look_2023)
	- could help understand internal modifications from techniques like instruction fine-tuning and RLHF
- Vision, multimodal, and RL models
	- regarding mechanistic interpretability, little work has been done on vision transformers
	- Interpretability of RL should investigate reward/goal representations, study circuitry changes from alignment algorithms and explore emergent subgoals (or proxies such as internal reward models)
