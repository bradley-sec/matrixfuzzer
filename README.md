# Matrix Fuzzer

### Introduction

Current AI safety research often separates success rates by attack strategy. This makes sense, as large variances are often seen when doing this. I believe that success rates should be further broken up by tone, creating a 2D matrix. To do this, I created these Python scripts to assist in generating a large enough dataset for testing my theory. While the initial results do not show a statistically significant difference outside of the outlier, I believe that it has uncovered flaws with my methodology early on that can be corrected for future testing.

### Definitions

**Tone** - The vocabulary, experience, and assumed intent of the user.

**Strategy** - The attack vector utilized for achieving adversarial goals against AI models.

### Technical Breakdown

I utilized simple localized models for these scripts. For attack generation, the primary model utilized was Phi 3 (phi3:3.8b). This model was provided an attack tone, strategy, and objective to generate queries. Additionally, human created "seed" queries were provided to increase the quality of adversarial queries generated. The output was sent through standard regex formatting to ensure it does not break the JSON output with failed escape characters.

Once the initial generation was complete, I utilized LFM 2 (maternion/lfm2:8b) as an auditor. I found through testing that utilizing an MoE model provided higher quality auditing capabilities to standard models, and chose LFM 2 due to its size fitting my computational constraints at the time of creation. The auditor will force regeneration of queries it finds ineffective, greatly increasing quality.

### Results

| Strategy / Tone | Naive | Urgent | Professional |
| :--- | :---: | :---: | :---: |
| **Roleplaying** | 12% | 19% | 29% |
| **Constraint Conflict** | 22% | 22% | 28% |

As shown, there is a statistically significant difference between tones, but this is primarily due to the Professional-Roleplaying success rate sitting as an extreme outlier. Further investigation shows that this is probably just sampling bias and should not be taken as a definitive result.

As further evidence of sampling bias, there is no statistical difference between strategies. This strongly indicates that my testing methodology was flawed from the start, as differences between testing strategy success rates are expected based on most prior research.

### Limitations

Small localized models often lack the guardrails present in frontier models. For instance, frontier models are typically capable of differentiating between system instructions and user prompts. When testing Phi 3, I found that the successful adversarial prompts often resulted in the secret key being disclosed alongside a claim from the model that the key was provided by the user. This breakdown in instruction hierarchy showcases guardrail failures in small models that are not present in larger models.

Additionally, by attempting to create a generator for adversarial queries prior to fully proving my theory, my test was conducted using low quality adversarial prompts that in some cases fail to meet my own definitions. For example, the prompt ID 93 under professional roleplaying showcases a complete failure in the query generation and auditing that resulted in a pseudo-medical abstract being used for testing.

### Conclusions & Next Steps

The results of this are inconclusive. More research will be needed, and I believe that this will become more important as time goes on given that multiple frontier model organizations are moving towards intent based guardrails that are dependent on the model's analysis of the user over what is actually being requested.

For future tests, I will be creating a large number of queries manually, and hope to test against multiple larger models.
