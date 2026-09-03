# FairHire-Multimodal

## Auditing and Mitigating Cultural, Linguistic, and Educational Bias in Multimodal AI-Assisted Hiring

This repository contains a research proposal and reproducible experiment plan designed for the **SHL Research Intern, AI (VN4469)** role in Gurgaon.

The project targets the role’s stated areas of work: natural language processing, speech processing, computer vision, large language models, retrieval-augmented generation, orchestration, production monitoring, data annotation, fairness, and research documentation.

## Research question

> Can a multimodal, retrieval-grounded candidate-evaluation system improve job-relevant validity while reducing sensitivity to irrelevant cultural, linguistic, demographic, and educational signals?

## Core idea

Build an auditable benchmark and prototype pipeline that compares:

1. A text-only baseline for job–resume or interview-response evaluation.
2. A multimodal system that uses transcript, acoustic features, and visual features only when they are demonstrably job-relevant.
3. A retrieval-augmented system grounded in a competency framework and behaviorally anchored rating scales.
4. A fairness-aware post-processing and monitoring layer that reports subgroup performance, adverse impact, calibration, drift, and explanation stability.

The project does **not** infer protected attributes from faces, voices, or names. Sensitive attributes are used only in controlled, consented audit data to measure disparate outcomes and are excluded from model inputs wherever possible.

## Repository contents

- [`research-proposal.md`](research-proposal.md): Full research proposal, literature grounding, hypotheses, method, metrics, timeline, risks, and expected contributions.
- [`experiment-plan.md`](experiment-plan.md): Reproducible implementation plan, ablations, data schema, evaluation protocol, and suggested Python package structure.
- [`REVIEW_AND_FIXES.md`](REVIEW_AND_FIXES.md): Advanced audit of the first version, identified mistakes, and the scientific corrections applied.

## Status

This is a research design and implementation-ready scaffold. No candidate data, model outputs, or claims of empirical performance are included yet.

## Responsible-use boundary

This work is intended for research and quality assurance. It must not be used to make autonomous employment decisions. Any future deployment would require validated job-related constructs, consent and data governance, human review, legal review, accessibility testing, and continuous monitoring.

## Source note

The internship vacancy was accessible at the SHL applicant portal. The additional Facebook-hosted PDF supplied with the request returned HTTP 403 during retrieval and is therefore not treated as evidence. Please upload that PDF directly if it should be incorporated.

## License

The proposal and experiment plan are released under the MIT License for research and educational use. Deployment in employment decisions remains subject to applicable law, professional standards, and organizational review.
