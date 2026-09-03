# Advanced Review and Corrections

## Why this review was necessary

The first version had a strong concept, but a research-paper-level proposal must distinguish a promising architecture from evidence that has actually been demonstrated. This review records the main weaknesses found and the corrections applied.

| Issue found | Why it matters | Correction |
| --- | --- | --- |
| The title used the word “causal.” | Counterfactual perturbations of text, audio, or video do not identify causal effects on hiring outcomes. | Replaced “causal” with “counterfactual” and added an explicit causal-inference limitation. |
| H3 referenced an “adversarial-invariance layer” that was not specified elsewhere. | Reviewers could treat this as an unimplemented contribution or an unexplained confounder. | Removed the unsupported dependency and made the contribution an evidence gate plus fairness budget. |
| Fairness was described mainly as a metric collection. | A dashboard can detect harm but does not define what the system should do when harm is detected. | Added per-competency fairness budgets and explicit abstention states. |
| “Selection-rate ratio” was listed without a decision policy. | A score is not a hiring decision. Selection metrics are undefined until a review capacity or threshold is declared. | The implementation plan now requires a pre-registered review capacity/threshold policy and separates score fairness from decision fairness. |
| Thresholds could be tuned on the test set. | This produces optimistic estimates and invalidates the final comparison. | Gate thresholds and calibration must be selected on development/validation data and locked before test evaluation. |
| Counterfactual variants could leak across splits. | Near-duplicate records in train and test can inflate validity and fairness results. | Candidate/source-level splitting is mandatory for all original and transformed records. |
| Multimodal input may create accessibility harm. | Requiring face or voice data can exclude candidates and introduce non-job-relevant proxies. | Modality availability is treated as a first-class subgroup, and missing-modality paths must be evaluated. |
| The inaccessible Facebook PDF could have been silently inferred. | Unsupported source claims would weaken credibility. | The README explicitly labels the PDF as unverified because retrieval returned HTTP 403. |

## New research feature

The strongest new feature is the **fairness budget with abstention**. Instead of always producing a candidate score, the system checks whether the selected modality and output satisfy pre-registered limits for uncertainty, counterfactual sensitivity, subgroup error gaps, and calibration. If not, it abstains and routes the case to human review or an approved accessible assessment.

This is more realistic than assuming one fairness metric can solve every hiring context. The budget is a transparent policy parameter. The paper should include sensitivity analysis across budget levels and should never present one budget as universally correct.

## Statistical correction

The implementation must use nested validation for any learned gate, calibration model, or threshold. The development set is used for model selection. The validation set is used for threshold selection. The test set is scored exactly once after the design is frozen. All counterfactual siblings remain in the same candidate-level partition.

## Scope correction

The project is an audit and prototype study. It may establish evidence about score validity, reliability, counterfactual sensitivity, explanation quality, and simulated drift. It does not establish that the system improves hiring outcomes, predicts job performance, or is legally compliant without separate criterion-related validation, field testing, and legal review.
