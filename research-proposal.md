# FairHire-Multimodal: A Counterfactual, Retrieval-Grounded, and Drift-Aware Framework for Auditing Multimodal AI-Assisted Hiring

**Research proposal for the SHL Research Intern, AI role (VN4469)**  
**Author:** Manus AI  
**Date:** 3 September 2026  
**Status:** Proposal and implementation blueprint; empirical results are intentionally not claimed.

## Abstract

AI-assisted hiring increasingly combines language, speech, vision, large language models, and production monitoring. The central research risk is not only whether a model predicts a job-relevant outcome, but whether its output changes when irrelevant identity or communication-style signals change. This proposal introduces **FairHire-Multimodal**, a research framework for measuring and reducing such sensitivity while preserving job-related validity.

The framework makes five advances. First, it separates **construct validity** from **surface-form sensitivity** by evaluating matched counterfactual candidate records in which names, educational institutions, dialect-linked linguistic features, acoustic conditions, and non-job-relevant visual cues are changed while evidence of job-related competence is held constant. Second, it uses **retrieval-augmented generation (RAG)** over a competency ontology and behaviorally anchored rating scales so that explanations are traceable to predefined job-relevant evidence rather than unconstrained model intuition. Third, it proposes a **multimodal gating policy** that admits each modality only when incremental validity and subgroup robustness justify its use. Fourth, it integrates a **deployment monitor** for subgroup performance, adverse impact, calibration, explanation stability, and data/model drift. Fifth, it introduces a **fairness budget with abstention**: a modality or score is blocked when its uncertainty or disparity exceeds a pre-registered budget, rather than being forced into a ranking.

The primary outcome is a reproducible benchmark and prototype showing when multimodal AI adds legitimate predictive signal and when it introduces avoidable disparity. The project is tightly aligned with SHL’s Research Intern, AI brief, which calls for work in NLP, speech, computer vision, generative AI, RAG, orchestration, scalable production models, annotation, fairness, and research documentation. The proposed result is suitable for an internal technical report and, subject to data access and ethics approval, a workshop or conference paper.

## 1. Employer and role alignment

The advertised SHL role is **Research Intern, AI**, requisition **VN4469**, full-time in the Gurgaon office. The vacancy explicitly requests advanced work in NLP, speech, computer vision, deep learning, LLMs, RAG, multimodal AI, orchestration, performance monitoring, scalable ML integration, annotation, fairness, and research papers. SHL describes its assessment portfolio as covering behavioral, personality, cognitive, skills, simulations, and job-focused assessments, with an emphasis on job-related constructs, reliability, validity, candidate experience, and fairness [1].

The proposal therefore avoids a generic chatbot project. It treats hiring AI as a **measurement system**: a system should measure a defined construct, use job-relevant evidence, produce reliable scores, support human review, and remain valid as populations and workflows change.

| Vacancy requirement | Proposed evidence in this project |
| --- | --- |
| NLP, speech, and vision | Controlled text, audio, and video interview-response representations with modality ablations. |
| LLMs and generative AI | Structured LLM judge with constrained output, uncertainty, evidence citations, and prompt/version registry. |
| RAG and orchestration | Competency ontology and behaviorally anchored rating scale retrieved per job family; modular pipeline with retries, caching, and trace logs. |
| Scalable ML integration | Batch inference interface, dataset versioning, model registry, latency/cost accounting, and container-ready services. |
| Data collection and annotation | Annotation protocol, rater calibration, agreement analysis, adjudication, and provenance metadata. |
| Fairness principles | Counterfactual fairness tests, subgroup metrics, equalized performance analysis, adverse impact, calibration, and intersectional reporting. |
| Performance monitoring | Drift tests, score-distribution monitoring, feature-relevance monitoring, and model-card updates. |
| Research papers and documentation | This proposal, preregistered hypotheses, reproducible experiment plan, and publication-style result tables. |

## 2. Problem statement

A candidate-evaluation model can be accurate on average and still be unsafe. It may reward a culturally dominant speaking style, penalize a non-native accent, rely on educational prestige rather than demonstrated skill, or use visual and acoustic correlates that are not valid indicators of the target construct. Recent evidence illustrates why this requires direct auditing. Rao et al. report lower LLM interview scores for Indian than UK transcripts even after anonymization, with differences associated with linguistic properties [2]. Iso et al. find that job–resume matching models can show educational-background bias and that fairness varies substantially across model families and generations [3].

The research challenge is to jointly optimize four properties:

1. **Job-related validity:** scores should track expert ratings of the intended competency or outcome.
2. **Reliability:** repeated or semantically equivalent evidence should produce stable scores.
3. **Fairness:** irrelevant demographic, linguistic, educational, and modality-specific signals should not create systematic disparities.
4. **Operational trustworthiness:** the system should expose evidence, uncertainty, drift, and failure modes to human owners.

These properties can conflict. Removing all language variation may erase legitimate communication evidence. Adding more modalities may improve prediction for some groups while harming accessibility or increasing spurious correlations. The correct objective is therefore not “maximally multimodal,” but **evidence-gated multimodality**.

## 3. Proposed research question and hypotheses

### Main research question

**Under what conditions does retrieval-grounded, evidence-gated multimodal evaluation improve job-related validity without increasing sensitivity to irrelevant identity or communication-style signals?**

### Hypotheses

| ID | Hypothesis | Test |
| --- | --- | --- |
| H1 | RAG grounding reduces unsupported rationale claims and improves explanation faithfulness compared with an unconstrained LLM judge. | Compare citation precision, evidence sufficiency, and expert-rated rationale quality. |
| H2 | A multimodal model does not uniformly outperform text-only models; gains are construct- and subgroup-dependent. | Paired bootstrap comparisons of validity and subgroup performance across modality sets. |
| H3 | Counterfactual perturbations of irrelevant identity cues cause smaller score changes under the proposed evidence gate and fairness budget. | Estimate counterfactual sensitivity and permutation-based confidence intervals. |
| H4 | Fairness-aware calibration can reduce group disparities with a bounded loss in overall validity. | Plot Pareto frontiers of validity versus disparity under multiple post-processing policies. |
| H5 | Explanation stability is an early warning signal for model drift and fairness degradation. | Relate changes in evidence attribution and subgroup metrics over simulated temporal shifts. |

## 4. Novel contribution

The key contribution is a **four-layer audit-and-mitigation architecture** rather than a single fairness metric.

### 4.1 Counterfactual evidence-preserving evaluation

For each candidate response, create controlled variants that preserve job-relevant content while changing one potentially irrelevant factor at a time. Variants may alter a name, institution label, spelling convention, dialect-linked lexical pattern, background noise, camera framing, or lighting. The benchmark records the transformation and a semantic-preservation score. The model is evaluated on both absolute validity and **counterfactual score invariance**.

This design distinguishes two questions that are often conflated:

- Does the model rank stronger evidence above weaker evidence?
- Does the model change its judgment when only an irrelevant surface cue changes?

The benchmark must not infer sensitive attributes from faces or voices. Audit attributes should come from consented metadata, synthetic counterfactual labels, or controlled transformations.

### 4.2 Competency-grounded RAG

A job description is mapped to a small competency graph. Each competency contains a definition, observable behaviors, inclusion and exclusion rules, rating anchors, accessibility notes, and evidence examples. At inference time, the system retrieves only the competency nodes relevant to the task and asks the model to return:

- a criterion-level score;
- quoted or time-localized evidence;
- an uncertainty estimate;
- an abstention flag when evidence is insufficient;
- a structured error code when the input is not comparable.

The RAG system is not used to retrieve personal candidate data from an uncontrolled corpus. It retrieves versioned assessment standards and job-specific criteria. This supports traceability and reduces the risk that a model invents a rationale.

### 4.3 Evidence-gated multimodality

Let \(m \in \{text, audio, vision\}\) denote an input modality and let \(c\) denote a competency. A modality is admitted only if it satisfies a pre-registered utility rule:

\[
G(m,c)=1 \quad \text{if} \quad \Delta V_{m,c} > \tau_V,
\; \Delta F_{m,c} < \tau_F,
\; R_{m,c} > \tau_R,
\; A_{m,c}=1.
\]

Here, \(\Delta V\) is incremental validity over the approved baseline, \(\Delta F\) is incremental disparity or counterfactual sensitivity, \(R\) is reliability, and \(A\) is an accessibility and policy approval indicator. The thresholds are selected before test-set evaluation. This prevents the system from using facial appearance, vocal identity, or other sensitive proxies merely because they improve in-sample prediction.

This is a **counterfactual audit**, not a causal estimate of real-world hiring effects. The transformation is interpreted as an intervention on a recorded surface cue only under an explicit semantic-preservation check. Claims about causal impact on employment outcomes require a separate prospective validation design.

### 4.4 Fairness budget and abstention

For every competency, define a budget for counterfactual score change, subgroup error gap, calibration gap, and uncertainty. The system may produce a ranking only when the relevant budget is satisfied. Otherwise, it returns an abstention state such as `insufficient_evidence`, `modality_not_approved`, or `fairness_review_required`. This creates a safer alternative to silently applying a generic fairness threshold across jobs with different constructs and base rates.

The budget is reported as a policy choice, not as a universal definition of fairness. Sensitivity analysis should show how conclusions change under stricter and looser budgets.

### 4.5 Fairness and drift as one monitoring problem

The monitor treats fairness as longitudinal. It tracks subgroup score distributions, selection-rate ratios, calibration, error rates, counterfactual sensitivity, missingness, modality availability, and explanation changes. A model can pass a static audit and fail after job mix, candidate population, recording devices, or language distribution changes. The monitor therefore raises alerts on both statistical drift and fairness drift.

## 5. System design

```text
Job description ──> competency mapper ──> versioned competency/RAG store
                                              │
Candidate evidence ─> privacy filter ─> modality encoders ─> evidence gate
                                              │                  │
                                              └──────────────> structured scorer
                                                                  │
                                  evidence + uncertainty + abstention + trace
                                                                  │
                evaluator ─> validity / fairness / reliability / drift dashboard
```

### Data layer

Each record contains a pseudonymous candidate identifier, job identifier, evidence modality, consent and provenance flags, annotation version, competency labels, rater identifiers stored separately, and transformation lineage for counterfactual variants. Raw biometric content should not be retained in the benchmark unless there is a documented legal, ethical, and scientific requirement.

### Model layer

The baseline includes a conventional text classifier or ranker and an LLM-based structured evaluator. The research system adds retrieval, modality gating, calibration, and abstention. All prompts, model versions, retrieval hits, temperature settings, and post-processing parameters are logged.

### Orchestration layer

The pipeline should expose deterministic stages: validate input, normalize evidence, retrieve criteria, run modality-specific encoders, apply the gate, score, check schema, run safety and evidence validators, persist trace, and emit monitoring metrics. Failed stages should be retryable without duplicating records. The same trace should be replayable for audit.

## 6. Dataset and annotation protocol

A 3–6 month internship should use a staged dataset strategy. Begin with synthetic and public examples to validate the pipeline, then replace them with approved internal or partner data if access is granted.

| Stage | Data | Purpose | Release rule |
| --- | --- | --- | --- |
| 0 | Synthetic competency-aligned responses | Unit tests and counterfactual generation. | Safe for repository examples. |
| 1 | Public or de-identified text benchmarks | Baseline validity and fairness experiments. | Check each dataset license. |
| 2 | Consent-based multimodal samples | Audio/vision ablations and accessibility checks. | Restricted storage; no raw release. |
| 3 | Internal assessment data | External validity and drift simulation. | Governed access only; aggregate results. |

Human annotation should use behaviorally anchored rating scales. At least two independent raters should score each sample, with a third adjudicator for disagreement, consistent with SHL’s described annotation practice [4]. Report Krippendorff’s alpha or an equivalent agreement statistic, rater calibration procedures, missingness, and disagreement distributions.

The study should stratify by job family, language variety, modality availability, and intersectional groups where sample size permits. Small cells should be suppressed or pooled under a pre-specified rule. No subgroup conclusion should be drawn from an unstable estimate.

## 7. Evaluation metrics

### Validity and ranking

Use Spearman correlation and weighted agreement with expert ratings, ROC AUC for binary outcomes where labels are valid, precision at the review capacity, and calibration error. Report confidence intervals using candidate-level bootstrap resampling.

### Reliability

Measure test–retest stability, equivalent-form agreement, inter-rater agreement, and score variance under benign transcription or recording perturbations.

### Fairness

Report selection-rate ratio and adverse impact, equal opportunity difference, equalized-odds gaps, subgroup calibration error, residual error gaps, and intersectional worst-group performance. A fairness metric is not treated as sufficient on its own because metrics can conflict and base rates can differ.

### Counterfactual sensitivity

For a candidate \(i\) and an irrelevant transformation \(t\), define:

\[
CS_{i,t}=|s(x_i)-s(t(x_i))|.
\]

Report the mean, upper quantiles, and the fraction exceeding a practical threshold. Also report whether the explanation changed its cited competency or evidence when only the irrelevant cue changed.

### Explanation quality

Evaluate citation precision, evidence coverage, unsupported-claim rate, rationale consistency, and expert usefulness. Explanations are not accepted as proof of fairness; they are audit artifacts.

### Drift

Use population stability index, Wasserstein distance, Kolmogorov–Smirnov tests, feature-relevance change, subgroup metric change, and explanation-embedding drift. SHL describes ongoing performance dashboards, drift monitoring, monthly fairness checks, and periodic reviews as components of responsible hiring AI governance [4].

## 8. Statistical analysis plan

The primary comparison is between the baseline and FairHire-Multimodal on the held-out evaluation set. All transformations, metrics, and thresholds must be fixed before test-set scoring. Use paired bootstrap intervals for differences and hierarchical models when records are repeated across counterfactual variants.

For repeated variants, use a mixed-effects model of the form:

\[
s_{ijk}=\beta_0+\beta_1\text{System}_j+\beta_2\text{Group}_k+
\beta_3(\text{System}_j\times\text{Group}_k)+u_i+\epsilon_{ijk},
\]

where \(u_i\) captures candidate or source-response effects. The interaction term estimates whether the proposed system changes subgroup disparities relative to baseline. Correct for multiple comparisons across competencies and modalities using a pre-registered false-discovery-rate procedure.

The study should report effect sizes and uncertainty, not only significance tests. Negative results are valuable: they reveal when a modality adds no reliable job-related value or when a mitigation harms validity.

## 9. Threats to validity and safeguards

**Construct underrepresentation:** Interview performance is not identical to job performance. The study must not claim employment prediction unless criterion-related validation is available.

**Synthetic counterfactual artifacts:** Rewriting can introduce unnatural language. Human review and semantic-preservation checks are required, and counterfactual results must be labeled as stress tests rather than population estimates.

**Proxy leakage:** Names, institutions, accents, image backgrounds, and recording conditions may encode sensitive or socioeconomic information. Apply privacy filtering and explicitly test proxy sensitivity.

**Small subgroup samples:** Use confidence intervals, suppression rules, and cautious interpretation. Do not rank groups using noisy point estimates.

**Automation bias:** A polished explanation can cause reviewers to over-trust the system. Display uncertainty, evidence, disagreement, and an abstention option. The system should assist review rather than make autonomous hiring decisions.

**Accessibility:** Audio and video should never be mandatory when the competency can be assessed through accessible alternatives. Evaluate modality-missing cases as a first-class subgroup.

**Distribution shift:** Validate across job families, time periods, languages, device conditions, and annotation cohorts. Trigger review when drift or explanation instability exceeds pre-specified limits.

The NIST AI Risk Management Framework is a suitable governance backbone because it organizes trustworthy AI work around Govern, Map, Measure, and Manage functions [5]. Employment AI may also be subject to high-risk regulatory requirements; legal review is mandatory before any operational use.

## 10. Deliverables and internship timeline

| Period | Deliverable | Acceptance criterion |
| --- | --- | --- |
| Weeks 1–2 | Literature review, threat model, data card, ethics and governance plan. | Scope, constructs, and audit attributes approved. |
| Weeks 3–5 | Baseline text evaluator and annotation schema. | Reproducible baseline runs with locked test split. |
| Weeks 6–8 | Counterfactual generator and fairness evaluator. | Unit tests and paired perturbation report. |
| Weeks 9–11 | Competency graph, RAG retriever, citation validator. | Evidence-grounded structured outputs with trace logs. |
| Weeks 12–14 | Multimodal encoders and evidence gate. | Ablation results and accessibility/missing-modality analysis. |
| Weeks 15–17 | Calibration, abstention, monitoring dashboard. | Drift and fairness alerts on simulated shifts. |
| Weeks 18–20 | Statistical analysis and error taxonomy. | Reproducible tables, confidence intervals, and failure cases. |
| Weeks 21–24 | Paper, model card, technical documentation, demo. | Internal review-ready research package. |

## 11. Expected research contribution

The expected contribution is a practical answer to a research and product question: **when should a hiring AI system use more modalities, and when should it deliberately use fewer?** The project contributes a controlled counterfactual benchmark, an evidence-grounded scoring architecture, a pre-registered modality-admission rule, and a longitudinal monitoring specification. It is stronger than a generic “bias detection” project because it links fairness to measurement validity, reliability, explainability, accessibility, and deployment drift.

A successful outcome is not necessarily a model with the highest average score. It is a system that can show, for each competency and subgroup, whether its evidence is job-relevant, whether its score is stable, whether its disparity is acceptable under the chosen policy, and when it should abstain or request human review.

## 12. Candidate interview pitch

> “I propose a research project called FairHire-Multimodal. It evaluates whether multimodal LLM systems add valid evidence to hiring assessments or merely amplify linguistic, educational, demographic, and recording-condition proxies. I would build a counterfactual benchmark, ground scoring in a competency ontology and behaviorally anchored rating scales, gate each modality by incremental validity and fairness, and monitor drift and explanation stability after deployment. The project directly combines SHL’s interests in NLP, speech, vision, RAG, orchestration, annotation, fairness, and production monitoring, while keeping human oversight and accessibility as design constraints.”

## References

[1]: https://www.shl.com/products/assessments/ "SHL talent assessments and skill tests"
[2]: https://arxiv.org/html/2508.16673v1 "Invisible Filters: Cultural Bias in Hiring Evaluations Using Large Language Models"
[3]: https://aclanthology.org/2025.naacl-industry.55.pdf "Evaluating Bias in LLMs for Job-Resume Matching"
[4]: https://www.shl.com/resources/by-type/blog/2023/how-does-shl-build-and-deliver-responsible-ai-based-hiring-solutions/ "How Does SHL Build and Deliver Responsible AI-based Hiring Solutions?"
[5]: https://www.nist.gov/itl/ai-risk-management-framework "NIST AI Risk Management Framework"
[6]: https://www.shl.com/resources/by-type/whitepapers-and-reports/ai-in-talent-assessment-transparency-trust-and-responsible-innovation/ "AI in Talent Assessment: Transparency, Trust, and Responsible Innovation"
[7]: https://shl-hr.my.salesforce-sites.com/recruit/fRecruit__ApplyJob?vacancyNo=VN4469 "SHL Research Intern, AI vacancy VN4469"
