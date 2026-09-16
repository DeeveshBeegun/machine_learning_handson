# Teaching guide: Spam or Not Spam?

## Files and setup

- **Spam_or_Not_Spam_Lab.ipynb**: learner notebook with working defaults, experiments, questions, and collapsible suggested answers.
- **Spam_or_Not_Spam_Lab_Executed.ipynb**: the same notebook with locally generated outputs, useful for lesson preparation or as a fallback.

Upload the learner notebook to Google Colab, connect to a CPU runtime, and run the first cell. No external data, API keys, or GPU are required. Run in order. If learners change earlier definitions, restart the session and run all cells to restore consistent state. Upload instructions: https://support.google.com/qwiklabs/answer/16671106?hl=en

Designed for beginners familiar with basic Python. Allow 75–90 minutes including discussion. For a 45-minute lesson, cover sections 1–5, demonstrate training, and finish with the new-email activity. Learners do not need to derive gradients; the training code is supplied.

## Lesson plan

| Section | Minutes | Teaching prompt |
|---|---:|---|
| Setup | 3 | What does the network receive: words or numbers? |
| Features | 8 | Predict the four numbers before running the extractor. |
| One neuron | 12 | Change only one input, weight, or bias at a time. |
| Activations and bias | 8 | Locate the point where ReLU becomes positive. |
| Full network and threshold | 10 | Read each layer's values and separate prediction from classification. |
| Training | 15 | Compare weights before and after; identify the four training stages. |
| Evaluation | 8 | Which error is worse for an email inbox, and why? |
| New email | 5 | Explain a prediction using only the extracted features. |
| Explain it back | 5 | Ask learners to explain a complete prediction to a partner. |

## Alignment with the slides

Feature order is spam-like words, suspicious-link cue, aggressive formatting, trusted sender. The architecture has 4 inputs, 3 hidden neurons, 2 hidden neurons, and 1 output: 20 weights plus 6 biases. Hidden layers use ReLU and the output uses sigmoid. Tanh is plotted as an alternative.

Propagation is explicitly taught as the weighted sum plus bias, separate from activation. The hand-chosen demonstration reproduces [1,1,1,0] → [2.1,1.4,0] → [2.6,0.2] → sigmoid(2.75) ≈ 0.94. Not all demonstration weights appeared in the slides; the notebook labels the completed matrices as illustrative. Training starts from different, seeded initial parameters.

The brain connection can be introduced verbally: inputs resemble dendritic signals, weights resemble connection strengths, and training changes weights. This is a simplified analogy, not a model of biological learning.

## Expected answers and results

- Original example features: [1,1,1,0]. Input contributions: 1.5, 0.3, 0.8, 0. Weighted sum 2.6; bias −0.5; z=2.1; ReLU output 2.1.
- Change only trusted sender to 1: z=1.1. Set only the first weight to zero: z=0.6. Increase only bias by 1: z=3.1.
- A zero input contributes zero regardless of the weight. A zero weight ignores that input in this neuron's current weighted sum.
- With weight 1, ReLU(x+b) becomes positive when x>−b. For bias −2, the boundary is x=2.
- Demonstration output: 0.9399. Threshold 0.5 gives Spam; threshold 0.95 gives Not Spam. The probability itself is unchanged by the threshold.
- Default training: seed 42, learning rate 0.05, 2,000 updates. Training loss falls from approximately 0.7345 to 0.1909. Small numerical differences are acceptable.
- Default eight-email test set: 8/8 correct at threshold 0.5. At 0.2, the appointment email becomes a false positive. At 0.8, the free-cash email is missed. Discuss the trade-off, not just accuracy.
- “Free museum entry this weekend” and “Free cash reward today” from unknown senders both map to [1,0,0,0] but have different labels. No network using only these features can distinguish them.

## Facilitation notes

Ask learners to predict first; otherwise the arithmetic activities can become passive cell execution. Keep the matrix gradient section optional. Use the loss curve to explain the purpose of training before examining its implementation.

The fixed seed makes the main lesson reproducible. Different initializations can produce inactive ReLU paths and stall near probability 0.5. If exploring seeds, treat this as a learning observation. A large learning rate can cause unstable training; restart with the defaults when necessary.

Do not interpret one hidden weight as global feature importance. Its sign describes a local effect on that neuron's weighted sum; downstream connections and activation matter too. Input scales also affect weight interpretation.

The test set contains different fictional messages, but feature patterns recur. With four binary features there are only 16 possible input vectors. Perfect accuracy on eight artificial examples is not evidence for deploying a spam filter. The output is not demonstrated to be calibrated, even when displayed as an estimated probability. Rounded values such as 1.000 are not a guarantee of certainty.

For threshold experiments, describe results as exploration. To select a threshold fairly, add a validation set and retain a separate untouched final test set. Do not let learners repeatedly optimize against these eight test emails and present that score as unbiased performance.

## Quick assessment

Give one point each for explaining:

1. The four feature meanings and consistent order.
2. Weight versus bias.
3. Propagation versus activation.
4. Why this network has 26 parameters.
5. Forward prediction versus training updates.
6. False positive versus false negative.
7. Why identical feature vectors force identical predictions.
8. Why this toy model's test results do not establish real-world quality.

Suggested mastery: 6/8, with propagation versus activation and training versus prediction both correct.

## Verification

All 15 code cells were executed locally in sequence using Python, NumPy, pandas, and Matplotlib. The six charts were generated. Assertions verify the slide's intermediate values, architecture, and disjoint email indices. Backpropagation was independently checked with finite differences for all 26 parameters away from ReLU's zero boundary. The notebook is prepared for Colab but was not executed in a hosted Colab runtime.
