# Funding Pitch: FairHire-Multimodal

## Executive request

**Project:** FairHire-Multimodal — Counterfactual, Retrieval-Grounded, and Drift-Aware Auditing of Multimodal AI-Assisted Hiring  
**Requested support:** INR 12,00,000 for a 24-week research pilot  
**Primary beneficiary:** SHL AI and People Science teams  
**Funding type:** Research internship / applied responsible-AI pilot  
**Decision requested:** Approve a time-boxed feasibility study with a gated continuation decision after Week 8.

This proposal does not ask SHL to fund an unbounded model-building effort. It asks for a controlled research pilot that answers a high-value product question: **which modalities and generative-AI components improve job-related assessment, and which introduce avoidable fairness, accessibility, or reliability risk?**

## Why fund this now

SHL’s Research Intern, AI vacancy explicitly seeks work in NLP, speech, computer vision, generative AI, RAG, orchestration, production monitoring, annotation, fairness, and research documentation [1]. SHL also describes responsible assessment AI as requiring innovation alongside human oversight, ethical guardrails, fairness, transparency, and compliance [2].

The project converts those priorities into a measurable artifact. It can help SHL make better decisions about model design before costly production integration. It can also produce a reusable audit harness, evidence-grounded scoring prototype, monitoring specification, and publication-quality research package.

Recent research shows why a targeted study is valuable. LLM interview evaluations have shown cross-cultural score disparities even after transcript anonymization [3]. LLM job–resume matching performance and bias vary across model families, with educational-background effects remaining important [4]. A small, controlled study can test whether similar risks appear in the target assessment context and whether they can be reduced without damaging job-related validity.

## What the sponsor receives

| Output | Business value | Delivery |
| --- | --- | --- |
| Counterfactual audit benchmark | Finds sensitivity to irrelevant names, institutions, linguistic forms, and recording conditions before deployment. | Week 8 prototype; Week 20 validated report. |
| Competency-grounded RAG evaluator | Makes scores traceable to job-specific behavioral anchors rather than free-form model intuition. | Week 11. |
| Evidence-gated multimodal policy | Prevents a modality from being used merely because it improves aggregate accuracy. | Week 14. |
| Fairness budget and abstention mechanism | Creates an operational response when uncertainty or disparity exceeds policy limits. | Week 17. |
| Drift and fairness monitor | Detects degradation as candidate populations, jobs, devices, or language distributions change. | Week 17. |
| Paper-ready technical report | Supports internal knowledge sharing, external publication review, and employer brand. | Week 24. |
| Model card and governance checklist | Makes limitations, intended use, monitoring, and human oversight explicit. | Week 24. |

## Research proposition

The project tests whether a system that is **grounded in competency evidence and selectively admits modalities** can achieve a better validity–fairness–accessibility trade-off than an unconstrained multimodal system.

The central success condition is not the highest average score. The system succeeds if it can identify when a modality adds valid job-related evidence, quantify when it creates disparity, abstain when evidence is insufficient, and provide an auditable trace for human review.

## Work packages

| Work package | Scope | Success metric |
| --- | --- | --- |
| WP1: Measurement definition | Define constructs, job families, rating anchors, audit attributes, and governance rules. | Signed-off construct and data card. |
| WP2: Baseline and benchmark | Implement text baseline, structured LLM baseline, source-level splits, and counterfactual lineage. | Reproducible baseline with leakage tests. |
| WP3: Grounding and evidence | Build competency graph, RAG retriever, citation validator, and abstention schema. | Reduced unsupported-claim rate versus baseline. |
| WP4: Multimodal gate | Compare text, audio, vision, and fused conditions using pre-registered admission rules. | Gate decisions supported by held-out validity and fairness results. |
| WP5: Monitoring and decision support | Add calibration, fairness budgets, drift simulation, and dashboard outputs. | Alerts recover injected drift and policy violations. |
| WP6: Research communication | Produce technical report, model card, demo, and paper draft. | Sponsor review-ready package. |

## Budget estimate

The estimate is intentionally conservative and should be adapted to SHL’s internal compensation, compute, and data-governance policies.

| Cost category | Estimate (INR) | Assumption |
| --- | ---: | --- |
| Research intern stipend | 7,20,000 | INR 30,000/month for 24 weeks. |
| Secure compute and inference | 1,50,000 | Approved cloud/GPU/API usage with spend caps. |
| Annotation and expert adjudication | 1,20,000 | Limited pilot sample, two raters plus adjudication. |
| Data protection, storage, and tooling | 60,000 | Restricted storage, logging, backup, and experiment tracking. |
| Publication, review, and contingency | 1,50,000 | Documentation, review, accessibility checks, and 12.5% contingency. |
| **Total pilot request** | **12,00,000** | **Time-boxed 24-week pilot.** |

If SHL provides an intern stipend, internal compute, approved datasets, and existing annotation staff, the incremental research budget can be materially lower. The budget should not be interpreted as a final compensation offer.

## Stage-gated funding model

To reduce sponsor risk, release support in three gates rather than committing all resources at once.

| Gate | Timing | Evidence required | Continue if |
| --- | --- | --- | --- |
| Gate 1: Feasibility | Week 8 | Baseline, data card, leakage tests, counterfactual generator, initial audit. | Data and construct are usable, and at least one measurable audit signal exists. |
| Gate 2: Prototype | Week 16 | RAG evaluator, modality ablations, evidence gate, fairness-budget simulation. | The system produces actionable differences between conditions without unacceptable data risk. |
| Gate 3: Research package | Week 24 | Locked analysis, monitoring report, model card, demo, and paper draft. | Results are reproducible and limitations are clearly documented. |

A gate may stop or redirect the project. A negative result is still a successful research outcome when it prevents unsupported production use or identifies a modality that should not be deployed.

## Expected return on support

The pilot may reduce downstream cost in four ways. First, it can identify unproductive modalities before full integration. Second, it can expose dataset and annotation problems before model training. Third, it can provide reusable evaluation and monitoring infrastructure for future AI products. Fourth, it can strengthen SHL’s evidence base for responsible innovation, customer assurance, and research publication.

These are expected benefits, not guaranteed financial returns. The project should track practical indicators such as engineer hours saved by reusable evaluation components, number of model risks detected before deployment, inference cost per evaluated candidate, annotation agreement, and time to reproduce a result.

## Risk controls

The project will not make autonomous employment decisions. It will not infer protected attributes from faces or voices. It will not place raw candidate data in the public repository. It will not claim job-performance prediction without criterion-related validation. Audio and video will not be mandatory when the competency can be assessed through accessible alternatives.

The sponsor retains authority over approved datasets, model endpoints, publication review, legal review, and any decision to continue beyond the pilot. The public repository contains only research documentation and safe synthetic fixtures unless SHL authorizes otherwise.

## Sponsor decision

The requested decision is **not** “approve an AI hiring product.” The requested decision is:

> Approve a 24-week, stage-gated research pilot to measure validity, fairness, accessibility, reliability, and drift before deciding whether any component merits further product investigation.

## References

[1]: https://shl-hr.my.salesforce-sites.com/recruit/fRecruit__ApplyJob?vacancyNo=VN4469 "SHL Research Intern, AI vacancy VN4469"
[2]: https://www.shl.com/resources/by-type/whitepapers-and-reports/ai-in-talent-assessment-transparency-trust-and-responsible-innovation/ "AI in Talent Assessment: Transparency, Trust, and Responsible Innovation"
[3]: https://arxiv.org/html/2508.16673v1 "Invisible Filters: Cultural Bias in Hiring Evaluations Using Large Language Models"
[4]: https://aclanthology.org/2025.naacl-industry.55.pdf "Evaluating Bias in LLMs for Job-Resume Matching"
