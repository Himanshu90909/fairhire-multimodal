# FairHire-Multimodal: Experiment Plan

## Objective

Implement a research-grade, reproducible audit of AI-assisted candidate evaluation. The implementation should compare text-only, unconstrained multimodal, retrieval-grounded, and evidence-gated systems under the same data splits and evaluation harness.

## Recommended project structure

```text
fairhire-multimodal/
├── README.md
├── research-proposal.md
├── experiment-plan.md
├── configs/
│   ├── data.yaml
│   ├── models.yaml
│   └── thresholds.yaml
├── src/fairhire/
│   ├── data_schema.py
│   ├── counterfactuals.py
│   ├── competency_store.py
│   ├── retrieval.py
│   ├── modality_gate.py
│   ├── evaluator.py
│   ├── fairness.py
│   ├── reliability.py
│   ├── drift.py
│   └── monitoring.py
├── tests/
├── notebooks/
├── reports/
└── data/README.md
```

## Minimal record schema

```json
{
  "record_id": "synthetic-0001",
  "job_id": "job-family-a",
  "competency_id": "structured-problem-solving",
  "text": "redacted or approved transcript",
  "audio_uri": null,
  "video_uri": null,
  "expert_score": 3.5,
  "group_metadata": {
    "audit_group": "controlled_condition_a",
    "language_variety": "en-IN",
    "modality_available": "text"
  },
  "consent_status": "synthetic",
  "transformation_parent": null,
  "transformation_type": "original",
  "dataset_version": "v0.1"
}
```

Do not put real candidate identifiers, raw biometric data, or protected attributes in a public repository. Store sensitive metadata separately and publish only aggregate results.

## Experimental conditions

| Condition | Inputs | Purpose |
| --- | --- | --- |
| B0 | Text + fixed rubric | Non-generative baseline. |
| B1 | Text + unconstrained LLM | Measures the effect of generative evaluation without grounding. |
| B2 | Text + competency RAG + citations | Tests evidence grounding. |
| B3 | Text/audio/vision + unconstrained fusion | Measures raw multimodal gains and risks. |
| B4 | Text/audio/vision + RAG + evidence gate | Proposed system. |
| B5 | B4 + calibration and abstention | Tests operational safety. |

All conditions must use the same source records, candidate-level splits, and evaluation seeds where applicable. Counterfactual variants belonging to one source record must never be split across train and test partitions.

## Counterfactual generation

Create one transformation at a time. Examples include:

- Replace a name with a matched synthetic name while preserving all other text.
- Replace an educational institution with a matched-format institution label.
- Normalize or perturb spelling conventions while preserving semantic content.
- Add controlled background noise or microphone compression to test robustness.
- Change camera framing and lighting in consented or synthetic video.

Each transformation must include a preservation check. For text, compare semantic embeddings and human judgments. For audio and video, verify that the intended content and job-relevant behavior remain unchanged. Reject transformations that change the construct being assessed.

## RAG design

Represent each job family as a versioned competency package:

```json
{
  "competency_id": "structured-problem-solving",
  "definition": "Breaks ambiguous problems into testable components.",
  "positive_anchors": ["states assumptions", "compares alternatives", "checks consequences"],
  "negative_anchors": ["jumps to an answer without reasoning"],
  "non_evidence": ["accent", "camera quality", "institution prestige"],
  "rating_scale": {"1": "not demonstrated", "3": "partially demonstrated", "5": "strongly demonstrated"},
  "version": "2026-09-03"
}
```

The retriever should return the competency definition, anchors, and non-evidence rules. The evaluator should cite evidence spans or time intervals and return a schema-valid JSON object. A citation validator should reject outputs whose evidence is absent from the input or whose rationale invokes a non-evidence feature.

## Evidence gate

Start with a simple interpretable gate. Train or estimate incremental validity for each modality and competency on a development set. Require all of the following before enabling a modality:

- positive incremental validity above the pre-registered minimum;
- no material increase in worst-group error or counterfactual sensitivity;
- acceptable reliability under missing or noisy input;
- accessibility and policy approval;
- no dependence on prohibited or explicitly non-evidence features.

The gate should be evaluated on a held-out set. Do not tune thresholds on the final test set. Use nested validation when the gate, calibrator, or abstention threshold is learned: development data selects the model family, validation data selects thresholds, and the locked test set is scored once.

## Decision-policy separation

A model score is not itself a hiring decision. Define a review-capacity or threshold policy before computing selection-rate or adverse-impact metrics. Report score-level disparity separately from decision-level disparity. If no valid decision policy or outcome label exists, report calibration, error gaps, and counterfactual sensitivity but do not claim adverse impact.

## Fairness budget and abstention tests

Configure a per-competency budget for counterfactual score change, worst-group error gap, calibration gap, and uncertainty. Run sensitivity analysis at strict, moderate, and permissive budget levels. The system must return an explicit abstention state when a budget is exceeded or evidence is insufficient. Measure coverage, abstention rate, subgroup abstention gaps, and the validity of the cases that remain scored.

## Evaluation pseudocode

```text
for split in [development, validation, test]:
    freeze dataset version and candidate-level split

for condition in experimental_conditions:
    for record in split:
        criteria = retrieve_competency(record.job_id, record.competency_id)
        available = detect_available_modalities(record)
        admitted = evidence_gate(criteria, available, condition)
        output = evaluate(record, criteria, admitted, condition)
        validate_schema_and_citations(output)
        persist_trace(record, criteria, admitted, output)

aggregate candidate-level predictions
compute validity, reliability, fairness, counterfactual, explanation, and drift metrics
bootstrap confidence intervals
render tables and plots
```

## Metric implementation checklist

- **Validity:** Spearman correlation, weighted agreement, ROC AUC where justified, precision at review capacity.
- **Calibration:** Expected calibration error, reliability diagrams, subgroup calibration.
- **Fairness:** Selection-rate ratio, adverse impact, equal opportunity difference, equalized-odds gaps, residual error gaps, intersectional worst-group metrics.
- **Counterfactuals:** Mean absolute score change, 95th percentile change, explanation-change rate.
- **Reliability:** Test–retest correlation, equivalent-form agreement, rater agreement, missing-modality robustness.
- **Explanations:** Evidence citation precision, unsupported-claim rate, evidence coverage, expert usefulness, rationale stability.
- **Drift:** PSI, Wasserstein distance, KS statistic, feature-relevance change, subgroup metric change, explanation-embedding drift.

Every metric report must include the denominator, confidence interval, subgroup sample size, missingness, and whether the estimate is based on original or counterfactual records.

## Error taxonomy

Tag errors using a controlled taxonomy so that model improvements are targeted rather than anecdotal:

1. Unsupported inference from identity or appearance.
2. Accent, dialect, grammar, or fluency sensitivity unrelated to the competency.
3. Educational prestige shortcut.
4. Modality quality or device-condition sensitivity.
5. Retrieval miss or wrong competency mapping.
6. Citation hallucination.
7. Overconfident score with insufficient evidence.
8. Construct underrepresentation.
9. Annotation disagreement.
10. Distribution shift or missing-modality failure.

## Reproducibility requirements

Pin package and model versions. Store configuration hashes, prompt versions, retrieval results, random seeds, evaluation timestamps, and dataset checksums. Use candidate-level bootstrap resampling with a published seed. Keep an experiment manifest that maps every reported table to a command and configuration file. Record compute cost and latency per condition.

## Paper-ready result tables

The final report should include:

- overall validity and reliability by condition;
- subgroup performance and calibration;
- counterfactual sensitivity by transformation type;
- modality ablation and evidence-gate decisions;
- fairness–validity Pareto frontier;
- drift-alert simulation;
- qualitative error examples with redacted evidence traces.

## Ethics and release plan

The public repository should contain code, synthetic fixtures, schemas, configuration examples, and aggregate demonstration outputs only. Internal or partner data should remain in governed storage. Before any human-subject data collection, obtain the required ethics, privacy, security, and legal approvals. The project should document a deletion policy, access controls, retention period, incident response, and model-card limitations.

## Suggested paper outline

1. Introduction and problem definition.
2. Related work on hiring AI, multimodal models, RAG, fairness, and measurement validity.
3. FairHire-Multimodal framework.
4. Counterfactual benchmark and annotation protocol.
5. Experimental conditions and statistical analysis.
6. Results: validity, fairness, reliability, explanation quality, and drift.
7. Error analysis and ablations.
8. Limitations, governance, accessibility, and responsible-use boundaries.
9. Conclusion.

## Suggested title alternatives

1. **When More Modalities Hurt: Evidence-Gated Multimodal AI for Fairer Hiring Assessment**
2. **FairHire-Multimodal: Counterfactual and Drift-Aware Auditing of AI-Assisted Candidate Evaluation**
3. **From Scores to Evidence: Retrieval-Grounded and Fairness-Aware Multimodal Hiring AI**
