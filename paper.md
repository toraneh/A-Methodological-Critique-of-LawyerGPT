---
title: "A Methodological Critique of LawyerGPT: Evaluation Validity, Data Provenance, and Claim Calibration in an Indian Legal-Domain LLM Study"
author: "Harsh Torane"
date: "2 October 2026"
fontsize: 12pt
geometry: margin=1in
linestretch: 1.5
header-includes: |
  \usepackage{etoolbox}
  \AtBeginEnvironment{longtable}{\footnotesize}
---

# Abstract

This paper provides a methodological critique of the first-draft manuscript LawyerGPT-Trained on Indian Legal Dataset (Behera and Agharia, 10 August 2023; hereafter, “the LawyerGPT draft”). The manuscript reports fine-tuning Falcon-7B-instruct and Llama 2 on a legal corpus built from selected Indian constitutional provisions, court cases, and synthetic instruction-output pairs, and it claims superior legal-text generation relative to GPT-3.5-turbo and other leading models, with GPT-4 serving as evaluator. This critique does not reject the value of the research direction. Rather, it argues that the draft does not cleanly separate legal-domain generalization from familiarity with training material, benchmark-specific effects, stylistic preference, and evaluator dependence.

Three methodological features are especially consequential. First, GPT-4 contributed substantially to synthetic-data construction and later served as the principal evaluator. Second, the test set explicitly includes questions extracted from the training dataset. Third, “unseen data” is not formally defined at the document or case level, so question-level novelty cannot be assumed to indicate generalization. Additional limitations include small per-category benchmark size (approximately 15–20 questions), mixed evaluation populations, undefined scoring criteria, no independent legal-expert validation, ambiguous dataset provenance and versioning, and the absence of numerical results in the supplied text.

The draft’s strongest claims—“significant superiority,” “deep and direct understanding,” and related characterizations—exceed what its described methodology can establish. A narrower interpretation is warranted: the study demonstrates the feasibility of a selected legal instruction-tuning pipeline yielding task-specific performance under the prompts and metrics used. The paper concludes with recommendations for document-level contamination control, explicit dataset versioning, blinded evaluation, independent legal-expert validation, reproducible scoring, ablation experiments, temporal validation, and more careful claim calibration.

\newpage

# 1. Introduction

Large language models (LLMs) have attracted substantial attention for domain adaptation, including legal applications, and prior work has adapted LLMs to legal corpora in various ways, from legal-domain encoders to general-purpose instruction-following systems. Legal-domain adaptation is difficult because legal texts rely on specialized terminology, jurisdiction-specific doctrine, dense citation structures, temporally changing rules, and distinctions between factual description, legal authority, interpretation, and argumentation.

These features make legal evaluation materially different from ordinary text-generation assessment. A response may be fluent and coherent while nevertheless misstating a legal rule, citing an irrelevant authority, relying on an outdated law, or drawing an unsupported conclusion. For this reason, legal performance should not be inferred solely from writing quality or stylistic polish. It must be evaluated in ways that distinguish linguistic quality from legal correctness and familiarity with source material from true generalization.

The LawyerGPT draft investigates this problem by fine-tuning Falcon-7B-instruct (the draft’s stylization) and Llama 2 on a dataset constructed from Indian legal materials. The first draft describes a pipeline involving manually curated prompts, synthetic instruction generation, legal-document summarization, and instruction tuning. It reports benchmarking against GPT-3.5-turbo, GPT-4, and Claude, with GPT-4 serving as the principal evaluator.

The project addresses a legitimate research question: whether relatively compact open models can be adapted to Indian legal material using a targeted instruction-tuning corpus. The use of Indian legal sources and the attempt to construct a domain-specific instruction set are sensible research directions. However, the strength of the manuscript’s conclusions exceeds what the experimental methodology described in the first draft can cleanly establish.

Several design features are especially consequential. The authors state that GPT-4 was used during dataset construction, including the transformation of legal material into instruction-input-output examples. They also report that summarization of selected constitutional articles was executed using GPT-3.5-turbo, GPT-4, and Claude, with approximately 40% of that data generated using GPT-3.5-turbo, 40% using GPT-4, and 20% using Claude. The draft does not specify precisely which corpus these proportions describe, a point examined in Section 16. The draft further states that the test set contains questions extracted from the training dataset, alongside general-knowledge questions, hypothetical questions, and questions drawn from unseen data.

The draft then states that GPT-4 was used as the evaluator and characterizes this as having “ensured an unbiased assessment.”

These design choices do not necessarily invalidate the project. But they impose significant limits on what the benchmark can establish. In particular, the study combines training-derived questions with held-out questions, uses a model family that contributed to synthetic-data generation as the principal judge, and does not provide enough numerical detail to assess the robustness of the observed differences. The result is a study that is potentially informative but difficult to interpret as a clean evaluation of legal generalization.

This paper therefore provides a methodological critique of the study as presented in its 10 August 2023 draft. The aim is constructive: to separate what the reported methodology directly supports from what requires qualification, and to specify what additional evaluation work would be necessary to justify stronger comparative claims.

\newpage

# 2. Scope and Method of the Critique

This critique concerns the manuscript as supplied in its first-draft form, dated 10 August 2023. It does not attempt to reproduce the training procedure, retrain the models independently, or independently evaluate the released model checkpoints.

The analysis begins with close textual examination of the supplied manuscript. Claims about dataset construction, evaluation composition, model comparison, and experimental procedure are treated as valid where the manuscript explicitly describes them. Methodological implications are identified separately from empirical claims.

The critique also considers publicly accessible project artifacts where they provide additional information about dataset composition, provenance, or versioning. These artifacts are not assumed to be identical to every dataset version used in every experiment unless that correspondence can be established. A later public artifact may differ from the exact corpus used in an earlier experiment. Accordingly, the critique distinguishes between:

1. Documented methodological features;
2. Publicly observable artifacts;
3. Methodological implications;
4. Unresolved empirical questions.

The goal is not to argue that the models cannot perform legal tasks. The goal is to determine whether the evidence presented is sufficient for the comparative and capability claims made in the draft. This paper is intentionally limited to the first-draft manuscript as supplied; it does not assume that later versions, supplementary materials, private evaluation files, or subsequent experiments contain the same omissions.

\newpage

# 3. Summary of the Main Methodological Concerns


| Issue                                                     | Evidence reported or publicly observable                                                                                            | Methodological implication                                                                                                                |
| --------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| Shared synthetic-data provenance and evaluator dependence | GPT-4 is used during dataset construction and later as the principal evaluator                                                      | The evaluation is not procedurally independent of the model family that contributed substantially to synthetic supervision                |
| Training-evaluation overlap                               | The draft explicitly states that the test set includes questions extracted from the training dataset                                | Performance on those questions should not be interpreted as generalization performance                                                    |
| Question-level versus document-level separation           | “Unseen data” is not formally defined                                                                                               | Novel wording does not necessarily mean the underlying case, statute, passage, or legal issue was unseen                                  |
| Small per-category benchmark                              | Approximately 15–20 questions per category                                                                                          | Individual items are a substantial fraction of the category total, making the reported results sensitive to a small number of examples    |
| Mixed evaluation populations                              | General knowledge, seen questions, hypothetical questions, and unseen questions are combined                                        | The benchmark mixes several constructs rather than measuring a single clearly defined target                                              |
| Undefined scoring dimensions                              | Terms such as “reasoning,” “interpretation,” and “coherence” are used without operational definitions                               | Independent replication and scorer agreement are difficult                                                                                |
| Missing numerical results                                 | The draft references attached benchmark results but does not reproduce them in the supplied text                                    | The magnitude and robustness of the reported differences cannot be assessed from the document alone                                       |
| Internally inconsistent comparison set                    | The abstract names GPT-3.5-turbo and Claude as baselines; the results section names GPT-3.5-turbo and GPT-4                         | The set of compared models cannot be determined from the draft                                                                            |
| No independent legal validation                           | GPT-4 is the principal evaluator; no legal-expert protocol is described                                                             | Legal correctness is not independently established                                                                                        |
| Narrow training distribution                              | The draft emphasizes selected constitutional provisions and approximately 50 cases                                                  | Limited source coverage does not establish broad Indian legal competence                                                                  |
| Synthetic-data quality assurance                          | Large portions of the corpus are generated by LLMs                                                                                  | Generator errors can become training supervision without independent review                                                               |
| Ambiguous generator proportions                           | 40% GPT-3.5, 40% GPT-4, 20% Claude are reported for article summarization without a dataset-version mapping                         | The provenance of the final training corpus cannot be reconstructed reliably                                                              |
| Dataset versioning                                        | Public artifacts contain multiple dataset versions, and the draft states the training set was “dynamically adapted” during training | The exact mapping between version, checkpoint, and reported result is unclear                                                             |
| Temporal legal validity                                   | Legal questions may depend on the law in force at a specific date                                                                   | A benchmark should clearly state the legal regime and date against which answers are judged                                               |
| Technical terminology                                     | QLoRA is described as “Quantized Lottery Ticket Hypothesis”                                                                         | This is a factual technical error; the correct term is Quantized Low-Rank Adaptation                                                      |
| Claim calibration                                         | The draft uses strong language such as “significant superiority” and “deep and direct understanding”                                | These claims exceed what the reported methodology can support                                                                             |
| Internal numerical inconsistency                          | The introduction reports “approximately 3.4k instructions”; the dataset-preparation section and conclusion report 3,300             | The final dataset size cannot be determined from the draft                                                                                |
| Misattributed citation                                    | The draft attributes Chain-of-Thought prompting to the LIMA paper                                                                   | Chain-of-Thought prompting is due to Wei et al. (2022); LIMA concerns data quality, and the draft’s gloss mischaracterizes LIMA’s finding |
| Uncited related work                                      | The draft mentions InLegalBERT but provides no citation                                                                             | The related-work coverage cannot be verified from the draft                                                                               |
| Unresolved placeholders                                   | The draft contains unresolved placeholders such as “\[link to the paper\]” and “\[link to the dataset\]”                            | Referenced supporting material cannot be identified, and the draft was not finalized                                                      |

\newpage

# 4. Claim, Evidence, and Inference

A useful way to assess the draft is to separate the claims made by the authors from the evidence described in it.


| Manuscript claim                                | Evidence described in the draft                                                                                                 | What the evidence establishes                                                                                                                 |
| -------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| “Significant superiority” over existing LLMs    | Qualitative descriptions of model outputs and comparative assertions; numerical results are not reproduced in the supplied text | The direction and magnitude of any performance difference cannot be independently assessed from the supplied text                             |
| GPT-4 provided an “unbiased assessment”         | GPT-4 was used as the evaluator                                                                                                 | GPT-4 was used as an automated evaluator; procedural independence and absence of systematic evaluator effects are not established             |
| “Advanced level of comprehension and reasoning” | Qualitative GPT-4 evaluation of model responses                                                                                 | The statement reflects an evaluator judgment, not independent legal validation                                                                |
| Generalization to legal questions               | Test set included training-derived questions and heterogeneous categories                                                       | Performance on seen or training-derived material is not equivalent to performance on genuinely held-out legal material                        |
| Performance on “unseen data”                    | The draft labels one category as unseen                                                                                         | The draft does not define whether “unseen” means unseen questions, passages, documents, cases, or issues                                      |
| Broad Indian legal capability                   | Training emphasis is on a selected constitutional subset and approximately 50 cases                                             | The described training distribution does not by itself establish broad coverage of Indian law                                                 |
| Reproducibility                                 | The draft claims that parameters were “meticulously documented”                                                                 | The supplied draft contains unresolved placeholders and does not provide enough detail to reconstruct the pipeline or benchmark entirely      |
| Effectiveness of synthetic-data generation      | Thousands of prompts are reported                                                                                               | Dataset size establishes scale but not necessarily correctness, diversity, or legal fidelity                                                  |
| General legal reasoning                         | Answers are evaluated on reasoning, interpretation, coherence, and related criteria                                             | The evaluation provides evidence about performance under the specified prompt and judge, but not clean legal-reasoning ability as a construct |


This distinction is central. The relevant question is not whether the reported outputs appeared strong. The question is whether the design permits the draft to infer broad legal reasoning capability from those observations.

\newpage

# 5. Evaluator Independence and the “Unbiased Assessment” Claim

## 5.1 What the draft reports

The draft states that GPT-4 was used during dataset construction. Specifically, the authors report that they initially created 150 manually curated prompts and subsequently generated 933 prompts “utilizing GPT-4 as a guiding mechanism.” They further state that GPT-4 assisted in transforming material into instruction-input-output examples. The draft also states that “the summarization of selected articles was executed using highly rated LLMs, namely GPT-3.5 turbo, Claude, and GPT-4,” with approximately 40% of that data generated using GPT-3.5-turbo, 40% using GPT-4, and 20% using Claude. As discussed in Section 16, the draft does not specify whether these proportions describe the article summaries alone or the final instruction corpus; they should not be assumed to apply to the case-derived prompts without further documentation.

Later, in the results section, the authors state:

> “By employing GPT-4 as the evaluator of the results produced by the inferenced \[sic\] models, we ensured an unbiased assessment of our model’s performance.”

\newpage

The same model family therefore contributes substantially to the construction of the training distribution and is subsequently used as the principal evaluator of the resulting model outputs.

## 5.2 Procedural dependence versus demonstrated bias

The issue is not that GPT-4 cannot be used as an evaluator. Research has shown that LLM-based evaluation can correlate with expert human judgments in particular tasks. Chiang and Lee report substantial agreement between LLM evaluation and expert human evaluation in the task settings they studied. Zheng et al. likewise report strong agreement between GPT-4 judgments and human preferences in MT-Bench and Chatbot Arena while documenting limitations involving position, verbosity, and self-enhancement effects.

The issue is independence.

When a model family contributes substantially to generating the synthetic training distribution and is then used to judge outputs from a model trained on that distribution, the evaluation is not independent in the conventional experimental sense. The judge and the data-generation process are procedurally connected.

This does not establish that GPT-4 systematically favored the fine-tuned model. It establishes a lack of procedural independence that should be acknowledged and tested:

> Procedural dependence is not demonstrated evaluator bias. The first is established by the described workflow; the second requires empirical evidence.

The potential concern is that the fine-tuned model may learn response structures, stylistic patterns, reasoning formats, or answer conventions represented disproportionately in synthetic data generated by the same model family that later evaluates the outputs. If so, the evaluation could partly reward conformity to the generator’s distribution rather than independently established legal correctness. The draft does not provide an experiment capable of determining whether such an effect occurred.

\newpage

## 5.3 What would strengthen the study

A stronger evaluation design would include at least one independent evaluation pathway:

- Human evaluation by qualified legal professionals;
- A benchmark constructed independently of the models used for synthetic-data generation;
- Blinded comparison among models;
- Multiple independent evaluators;
- Explicit agreement measurements between human and automated evaluation;
- Or an externally constructed benchmark with independently verified answers.

The existing GPT-4 evaluation could still be reported, but it should be described as an automated LLM evaluation rather than as inherently “unbiased” evaluation. An appropriate procedural statement would be:

> “GPT-4 was used as an automated evaluator in the initial benchmarking experiment.”

That formulation accurately describes the procedure without asserting an unsupported methodological guarantee. Potential evaluator effects that the draft should have controlled or acknowledged include preference for longer or more polished answers, preference for particular response structures, position effects in pairwise comparisons, difficulty distinguishing confident legal prose from legal correctness, difficulty identifying subtle citation errors, dependence on prompt formulation, and the dependence between the evaluator and the synthetic training distribution described above.

\newpage

# 6. Training-Test Overlap and the Undefined “Unseen” Category

## 6.1 The draft’s description

The most direct methodological concern appears in the draft’s inference section. The authors state:

> “Our testing set was meticulously structured to reflect a diverse array of question types, including general knowledge questions, queries extracted from the training dataset, hypothetical questions (for instance, explaining a fabricated legal case) … and questions drawn from unseen data.”

This statement confirms that at least one evaluation category consists of questions extracted from the training dataset. The draft therefore does not merely present a hypothetical risk of contamination. It acknowledges some degree of overlap between training and testing data.

## 6.2 Why this matters

A question derived from training material cannot be interpreted in the same way as a genuinely held-out question. If the purpose of the benchmark is to estimate general legal reasoning, performance on previously observed questions provides limited evidence of out-of-sample generalization. The model may reproduce, reconstruct, or exploit information encountered during fine-tuning.

This does not make training-derived questions useless. They can measure memorization, reproduction, or familiarity with the learned material. They should, however, be reported as a distinct evaluation population:

> Performance on seen material is not performance on unseen material.

A benchmark that combines the two without separate reporting makes any aggregate performance statement difficult to interpret.

## 6.3 “Unseen” requires a formal definition

The draft’s “unseen data” category is potentially the most valuable component of the benchmark, but “unseen” requires formal definition. Recent work on data contamination distinguishes contamination at the level of exact instances, paraphrases, and semantic content. In the absence of an explicit definition, at least five readings of “unseen” are possible:

1. The exact question string was absent from training;
2. A paraphrase of the question was absent from training;
3. The passage used to formulate the question was absent from training;
4. The underlying document or case was absent from training;
5. The underlying legal issue and closely related authorities were absent from training.

These represent substantially different evaluation conditions. For example, if a case was present in the training corpus and a new question was later asked about that same case, the question may be linguistically novel while remaining highly familiar at the document level. For legal-domain models, document-level separation is especially important: if the same case appears in training and testing under different questions, the test question does not constitute a clean test of generalization.

Legal-domain evaluation therefore requires explicit documentation of the split unit. At minimum, the study should state whether separation occurred at the question, passage, document, case, statute, or legal-issue level. A defensible evaluation would reserve entire cases or source documents for testing and ensure that semantically near-duplicate questions and answer-bearing passages are also excluded from training.

The relevant methodological principle is:

> Question novelty is not sufficient evidence of legal-domain generalization when the underlying source material remains present in the training distribution.

\newpage

# 7. Benchmark Size and Statistical Resolution

The authors state that each evaluation category contains approximately 15–20 questions.

A benchmark of this size is not inherently invalid. It can be appropriate for a pilot or exploratory experiment. The methodological concern arises from the combination of small category sizes, heterogeneous questions, opaque scoring, and absence of uncertainty reporting.

With approximately 15–20 observations per category, individual items constitute a substantial fraction of the category-level sample. A small number of unusually easy, difficult, ambiguous, or evaluator-sensitive questions could materially influence the category result.

The supplied draft does not report:

- Item-level scores;
- Score distributions;
- Variance;
- Confidence intervals;
- Standard errors;
- Inter-rater agreement;
- Statistical tests;
- Effect sizes;
- Or a prespecified analysis plan.

Consequently, the reader cannot determine how stable the reported differences are. The criticism is therefore not that a 15–20-item category is automatically invalid. Rather:

> A small and heterogeneous benchmark requires correspondingly cautious claims, transparent item-level reporting, and uncertainty estimates.

The study should either expand the benchmark or explicitly characterize it as preliminary.

\newpage

# 8. The Benchmark Mixes Distinct Evaluation Objectives

The categories described by the authors are:

- General knowledge questions;
- Queries extracted from the training dataset;
- Hypothetical questions involving fabricated legal cases;
- Questions drawn from unseen data.

These categories should not be treated as interchangeable evidence of legal reasoning.

## 8.1 General-knowledge questions

General-knowledge questions measure broad language-model capabilities. They may provide a useful control condition but do not directly establish Indian legal competence.

## 8.2 Training-derived questions

Training-derived questions can measure memorization, reproduction, or familiarity with the training distribution. They should not be treated as clean estimates of generalization.

\newpage

## 8.3 Fabricated legal cases

Hypothetical cases can test structured application of legal principles. They introduce a separate evaluation issue, however: a fabricated fact pattern may not have a single authoritative answer. An evaluator must therefore distinguish among:

- Whether the model identifies relevant legal rules;
- Whether it applies those rules coherently to the facts;
- Whether the conclusion is legally defensible;
- And whether the answer merely resembles plausible legal prose.

A hypothetical question can therefore be useful without necessarily having a uniquely correct output.

## 8.4 Unseen questions

Questions based on genuinely held-out source material are potentially the most relevant for measuring generalization. However, as Section 6.3 explains, the draft does not define what “unseen” means, so it remains unclear whether the category represents document-level generalization, question-level novelty, or another form of evaluation.

## 8.5 Construct validity

The benchmark consequently appears to measure several constructs: general language ability; memorization or familiarity; hypothetical legal analysis; legal-domain generalization; and response quality as judged by an LLM. These should be reported separately rather than collapsed into a single undifferentiated claim of “legal reasoning.”

\newpage

# 9. Legal Knowledge, Grounding, and Legal Reasoning Are Different Constructs

The draft frequently uses terms such as “reasoning,” “comprehension,” “interpretation,” and “understanding.” These should be separated more carefully. At least three related but distinct capabilities should be distinguished:

## 9.1 Legal knowledge

Does the model correctly identify a legal rule, statute, constitutional provision, or case?

## 9.2 Legal grounding

Does the model correctly connect its claims to authoritative legal sources? This includes whether cited authorities actually exist, whether they support the proposition for which they are cited, and whether the law is temporally applicable.

## 9.3 Legal reasoning

Does the model apply the relevant legal rules to the stated facts and derive a defensible conclusion?

A model can perform well on legal knowledge without demonstrating robust legal reasoning. Likewise, a fluent answer can appear reasoned while containing incorrect legal premises. The benchmark should therefore avoid treating improvements in answer quality as evidence of improved legal reasoning without separate grounding and validity checks.

\newpage

# 10. Undefined Performance Metrics

The results section states that the models were evaluated according to:

- Soundness of reasoning;
- Structure of generated responses;
- Relevance to input queries;
- Reasoning;
- Interpretation;
- Analysis of impact;
- Coherence;
- Clarity.

However, the draft does not define these dimensions operationally. For example:

- What constitutes a score of 4 rather than 3 for “reasoning”?
- How is legal correctness separated from coherence?
- How is “analysis of impact” scored?
- How is citation accuracy evaluated?
- What constitutes a hallucination?
- How is an evaluator instructed to treat a legally plausible but non-authoritative conclusion?

Without operational definitions, the benchmark cannot be reliably reproduced. A stronger rubric would specify criteria such as:

\newpage


| Criterion         | Example operational definition                                                              |
| -------------------------- | -------------------------------------------------------------------------------- |
| Factual accuracy  | Whether factual assertions agree with the supplied or authoritative source                  |
| Legal correctness | Whether the response states the applicable legal rule accurately                            |
| Authority         | Whether cited cases or statutes actually support the proposition for which they are cited   |
| Temporal validity | Whether the legal rule applied was in force and applicable at the specified evaluation date |
| Relevance         | Whether the response directly addresses the question                                        |
| Completeness      | Whether material components of the requested analysis are addressed                         |
| Reasoning         | Whether the conclusion follows from stated legal rules and facts                            |
| Hallucination     | Whether unsupported authorities, facts, quotations, or legal propositions are introduced    |
| Clarity           | Whether the response is understandable without sacrificing legal precision                  |


A prespecified rubric would also permit measurement of inter-rater reliability.

\newpage

# 11. Missing Numerical Results and Reproducibility

## 11.1 The draft does not reproduce the benchmark results

The draft repeatedly describes the results in qualitative terms, but it does not reproduce the underlying numerical benchmarking results; it states only that “these benchmarking results are attached for your reference.” The supplied first draft therefore does not permit independent assessment of:

- Number of evaluated items;
- Score per model, per category, and per criterion;
- Mean scores and score distributions;
- Variance and statistical uncertainty;
- Effect sizes;
- Or item-level evaluator judgments.

This limitation should not be phrased as proof that the authors never produced numerical results. A more defensible statement is:

> The body of the supplied first draft does not reproduce the numerical benchmarking results, and therefore the magnitude and robustness of the reported differences cannot be assessed from the manuscript text alone.

If the benchmark attachment exists, it should be incorporated into the archival record and cited explicitly.

\newpage

## 11.2 Missing experimental detail

A publishable empirical paper should allow another researcher to reconstruct the principal experiment. The current draft requires substantially more detail, grouped here by category:

- Data: exact dataset composition and version; immutable identifiers or hashes; cleaning and deduplication procedures; train/validation/test split; document-level contamination controls.
- Training: model checkpoints; LoRA and quantization configuration; learning rate, batch size, gradient accumulation, epochs or steps, sequence length, optimizer, and random seeds; hardware and software versions.
- Evaluation: evaluation prompts and evaluator model/version; evaluation temperature and number of runs; scoring rubric and evaluator instructions; blinding and ordering procedures; raw evaluation results.

The draft claims that all parameters were “meticulously documented for transparency and reproducibility.” Yet the supplied first draft contains unresolved placeholders and does not itself provide the complete configuration necessary to reproduce the evaluation.

The unresolved placeholders are directly observable in the supplied text. The draft’s related-works section contains “\[link to the dataset\]” and “\[link to the paper\]” in place of citations; its training section contains “\[link to the paper\]” and “\[link to the Open LLM Leaderboard\]” in place of the evidence for Falcon-7B-instruct’s prior performance. The draft also mentions InLegalBERT without any citation. These placeholders are consistent with the document’s stated status as a first draft, but they prevent verification of the draft’s supporting claims as supplied.

If the complete experimental configuration exists in supplementary material or a repository, it should be explicitly linked to the corresponding model and dataset version. Otherwise, the reproducibility claim should be narrowed.

## 11.3 Evaluation-prompt reproducibility

The evaluation procedure is especially sensitive to prompt wording, and the draft does not reproduce the complete GPT-4 evaluator prompt. This omission matters because small changes in an evaluator prompt can affect score distributions, preference for verbosity, weighting of criteria, treatment of uncertainty, citation expectations, and comparative judgments.

A reproducible evaluation should publish the exact evaluator system and user prompts, the evaluation criteria and output schema, the score range, examples of scored responses, the temperature, the model version, and any post-processing code. If the evaluator was asked to provide qualitative reasoning before assigning a score, that procedure should also be documented.

\newpage

# 12. Blinding and Order Effects

The first draft does not establish whether GPT-4 was blind to model identity. If an evaluator knows that one response came from “LawyerGPT” and another came from GPT-4 or GPT-3.5, that knowledge can affect judgments. For pairwise evaluation, response ordering can also matter.

A stronger procedure would:

- Replace model names with anonymous identifiers;
- Randomize response order;
- Use identical prompts;
- Repeat pairwise comparisons with reversed order;
- And report whether evaluator judgments change with ordering.

This is especially relevant because LLM-as-a-judge research has identified position effects among other evaluator limitations.

\newpage

# 13. Missing Independent Legal-Expert Validation

The supplied draft does not describe a human evaluation protocol involving Indian legal practitioners, legal scholars, or other qualified legal-domain experts.

This is consequential because the target of evaluation is not generic language quality. The authors make claims about legal reasoning, interpretation, legal impact, law-enforcement implications, case analysis, and broader legal understanding. These properties are difficult to establish solely through general-purpose language-model judgment.

An expert-annotation study need not involve every generated output. A practical design could involve a representative subset of benchmark responses evaluated independently by multiple qualified legal experts. The study could then report:

- Inter-rater agreement;
- Agreement between experts and GPT-4;
- Error categories;
- Hallucination rates;
- Citation errors;
- Temporal-law errors;
- And cases in which human and automated judgments disagree.

Such evidence would provide substantially stronger support for claims about legal competence. The appropriate claim at present is narrower:

> The first draft describes automated LLM evaluation, but it does not describe independent legal-expert validation of the reported legal-performance judgments.

\newpage

# 14. Narrowness of the Principal Legal Training Distribution

The authors state that they selected Articles 12, 14, 15, 19, and 21 of the Constitution of India and incorporated landmark cases predominantly relating to those provisions. They report selecting approximately 50 cases and generating approximately 3,300 prompts.

This is a valid starting point for a pilot study. It is not equivalent to broad coverage of Indian law. Indian law extends across many domains, including:

- criminal law
- civil procedure 
- criminal procedure
- evidence law
- contract law
- property law
- family law 
- taxation law
- labour law 
- administrative law
- environmental law
- intellectual property law
- consumer law
- data protection law


The draft should therefore distinguish between:

> performance on a selected Indian constitutional/legal corpus

and:

> general legal understanding in the Indian context.

The latter requires a substantially broader evidence base.

\newpage


# 15. Dataset Versioning and Public Artifact Provenance

The public project artifacts introduce a reproducibility question not fully addressed in the first draft. The public repository links three instruction datasets, and additional artifacts are hosted under the project’s Hugging Face namespace.

The existence of multiple public versions does not establish an error in the original study; dataset development is normal in ML research. However, it raises a reproducibility problem. The draft does not provide a precise version mapping between dataset version, training iteration, model checkpoint, evaluation dataset, evaluation date, and reported result. A reproducible paper should be able to answer:

> Which exact dataset produced the Falcon checkpoint whose results are reported in the evaluation section, and which exact testing dataset produced those results?

The versioning concern is strengthened by a further statement in the draft:

> “our training set was dynamically adapted in response to the iterative steps taken during the training phase.”

This sentence indicates that the training corpus was not fixed across iterations. The train-test boundary therefore cannot be assumed to have remained stable, and performance observed in one iteration cannot be assumed to correspond to the same corpus as another.

The publicly identifiable artifacts include:


   Artifact | Approx. size | Claimed role | Public identifier |
 | ----------------------- | -----------: | -------------------- | ------------------------- |
 | Lawyer\allowbreak\_\allowbreak GPT\allowbreak\_\allowbreak India | 150 | Initial question-answer pairs | nisaar/\allowbreak Lawyer\allowbreak\_\allowbreak GPT\allowbreak\_\allowbreak India |
 | Constitution\allowbreak\_\allowbreak Of\allowbreak\_\allowbreak India\allowbreak\_\allowbreak Instruction\allowbreak\_\allowbreak Set | 933 | GPT-4-guided instruction set | nisaar/\allowbreak Constitution\allowbreak\_\allowbreak Of\allowbreak\_\allowbreak India\allowbreak\_\allowbreak Instruction\allowbreak\_\allowbreak Set |
 | Articles\allowbreak\_\allowbreak Constitution\allowbreak\_\allowbreak 3300\allowbreak\_\allowbreak Instruction\allowbreak\_\allowbreak Set | ~3.3k | Article-derived instruction dataset | nisaar/\allowbreak Articles\allowbreak\_\allowbreak Constitution\allowbreak\_\allowbreak 3300\allowbreak\_\allowbreak Instruction\allowbreak\_\allowbreak Set |
 | LLAMA2\allowbreak\_\allowbreak Legal\allowbreak\_\allowbreak Dataset\allowbreak\_\allowbreak 4.4k\allowbreak\_\allowbreak Instructions | ~4.4k | Llama 2-format legal dataset | nisaar/\allowbreak LLAMA2\allowbreak\_\allowbreak Legal\allowbreak\_\allowbreak Dataset\allowbreak\_\allowbreak 4.4k\allowbreak\_\allowbreak Instructions |


The table lists only artifacts confirmed in the public repository at the time of writing. Additional artifacts reported in connection with the project (including a small testing split and intermediate Llama 2-format versions) could not be confirmed in the public record and should be verified against dataset cards and commit history before publication. Row counts above should likewise be confirmed against the dataset cards before quantitative reliance.

A further tension deserves note. The draft states that the Llama 2 model was trained on “the same dataset” as Falcon-7B-instruct. However, the publicly identifiable artifacts include a separate Llama 2-format dataset of approximately 4.4k instructions, whose size and format differ from the approximately 3.3k-instruction corpus described in the draft. Whether the Llama 2 experiments used the Falcon corpus, a reformatted superset, or an iteratively expanded version cannot be determined from the draft alone. The draft’s own introduction also reports the instruction set as “approximately 3.4k instructions,” while its dataset-preparation section and conclusion report 3,300. The exact final dataset size, and the correspondence between the two models’ training corpora, should therefore be documented explicitly.

The important point is not that every public artifact must have been used in the original experiment. Rather, the final paper should identify exact versions and hashes, or otherwise immutable identifiers, for every dataset used in each reported experiment.

\newpage

# 16. Data Provenance and Synthetic-Data Quality

## 16.1 Provenance categories

The draft states that its dataset combines “human-generated and synthetic data.” At the same time, the described workflow uses GPT-4 to assist in generating and transforming instruction-input-output pairs. At least four provenance categories are relevant:

1. Human-authored;
2. Human-selected;
3. Machine-generated;
4. Machine-transformed.

A dataset can be human-selected but machine-generated, or human-authored but machine-transformed. These distinctions matter because “human-generated and synthetic data” may otherwise give an unclear impression of how much of the actual training supervision was authored or verified by humans.

A complete provenance record should identify, for each dataset stage: the source document and its authority; the selection mechanism; the generator model and generation prompt; the extent of human editing and validation; the rejection rate; the dataset version; and the final inclusion status. In particular, the reported 40/40/20 generator proportions should be tied to a specific dataset version and generation stage; without such a mapping, the proportions cannot be independently connected to the final training distribution.

\newpage

## 16.2 The 400-task question

The draft states that it “adopted the instruction format of Stanford’s Alpaca project, which utilizes a diverse instruction set from 400 different tasks.” This description is itself questionable: the Alpaca project used 175 seed task templates, not 400. The draft later states that the authors “created a diverse instruction set composed of 400 varied tasks, which we applied across all legal court cases.” It is therefore unclear whether the 400 tasks derive from Alpaca or from the authors’ own construction. This ambiguity matters for the redundancy analysis below and should be resolved explicitly.

The draft’s introductory section also misattributes Chain-of-Thought prompting to “the Less is More Aligned (LiMA) paper,” stating that Chain-of-Thought prompting “has been shown to enhance accuracy relative to the volume of data used, as discussed in the Less is More Aligned (LiMA) paper.” This description conflates two distinct lines of work. Chain-of-Thought prompting is due to Wei et al. (2022), while LIMA (Zhou et al., 2023) argues that a small number of high-quality, carefully curated examples can suffice for alignment—a claim about data quality, not about prompting for rationale. The draft therefore appears to misread both papers: it assigns the wrong finding to LIMA and omits the correct citation for Chain-of-Thought prompting. Because these works are offered as motivating evidence for the dataset methodology, the misattribution weakens the draft’s conceptual foundations and should be corrected before the motivation can be evaluated on its merits.

## 16.3 Synthetic-data quality assurance

Synthetic data can be valuable for expanding task coverage. However, synthetic legal data create a specific quality-control problem: the generator itself can produce incorrect legal propositions, incomplete reasoning, incorrect citations, outdated rules, or interpretations that are plausible but unsupported. Synthetic generation therefore creates a pathway by which generator errors can enter the training corpus.

The draft does not sufficiently document an independent verification process for these generated examples. In particular, it does not clearly report:

- How many synthetic examples were manually reviewed;
- What proportion were rejected;
- Whether legal citations were checked against primary sources;
- Whether generated summaries were compared with source documents;
- Whether generated legal propositions were independently verified;
- Whether multiple models were used to cross-check outputs;
- Or whether legal experts reviewed a sample of the synthetic corpus.

The size of the synthetic corpus should therefore not itself be treated as evidence of dataset quality.

## 16.4 Size is not quality; scale is not diversity

The draft emphasizes the expansion of the instruction set from 150 prompts to 933 prompts and then to approximately 3,300 prompts. This demonstrates data expansion, but not necessarily increased information quality. If approximately 400 tasks are repeatedly applied to approximately 50 cases, the resulting examples may be highly correlated, and much of the dataset may represent repeated transformations of the same underlying information rather than genuinely new supervision.

The authors should therefore report not only the number of prompts, but also:

- Number of unique source documents, cases, legal provisions, and legal issues;
- Number of distinct task types and task-template frequencies;
- Prompts per case and per legal issue;
- Duplicate and near-duplicate rates;
- Average input and output lengths and token distribution;
- And the proportion of examples generated from each source.

This would allow readers to distinguish dataset scale from dataset diversity, and to assess whether the same source text appears in both training and evaluation through different instructions.

\newpage

# 17. Temporal Validity of Legal Evaluation

Legal information is temporally sensitive.

A legal-domain benchmark must specify the legal position against which an answer is judged. A model can reproduce a historically correct rule while providing an answer that is outdated under the law applicable at a later evaluation date. This issue is especially important for a study that relies on historical legal documents and may be evaluated over time.

The benchmark should therefore record:

- Date of the legal source;
- Date of training;
- Date of evaluation;
- Legal regime applicable at evaluation;
- Whether subsequent amendments were incorporated;
- And whether the task is intended to test historical or current law.

This distinction should be made explicit:

> Historical legal accuracy and current legal accuracy are different evaluation targets.

A legal model may be accurate relative to its source corpus while being temporally outdated.

\newpage

# 18. Textbooks Are All You Need and the Need for More Precise Characterization

The draft states:

> “By applying the method proposed in the paper ‘Textbooks are all you need,’ we generate synthetic data to preserve and enhance semantic relationships within the text.”

This appears to overstate what the cited work establishes.

Gunasekar et al.’s Textbooks Are All You Need concerns high-quality training data and synthetic textbook-like examples in the context of code modeling, not a validated legal summarization pipeline. It does not establish a general legal-domain curriculum or legal-text synthesis method.

Accordingly, the draft should distinguish between:

- Drawing inspiration from the high-quality synthetic-data principle; and
- Applying a demonstrated methodology to legal documents.

The latter is not established by the cited paper. A more accurate methodological description would be:

> “The authors draw inspiration from work emphasizing high-quality synthetic supervision and adapt this general principle to legal-document processing.”

If the authors intended to implement a specific procedure from the cited work, that procedure should be identified explicitly and its applicability to legal data justified.

\newpage

# 19. Quantization Claims and QLoRA Terminology

## 19.1 The 4-bit quantization claim is too strong

The draft describes 4-bit quantization as reducing GPU usage “without compromising the performance and capabilities of these models.”

This is categorical and likely too strong. Quantization can substantially reduce memory requirements and, under appropriate conditions, preserve much of a model’s performance. However, performance preservation is empirical and depends on model architecture, quantization method and format, task, dataset, inference configuration, and fine-tuning setup.

A more defensible formulation would be:

> “The 4-bit quantization configuration substantially reduced memory requirements while enabling the fine-tuning procedure used in this study; any effect on downstream task performance should be evaluated empirically.”

## 19.2 QLoRA terminology

The draft refers to a “QLoRA (Quantized Lottery Ticket Hypothesis) configuration.” This expansion is incorrect.

QLoRA refers to Quantized Low-Rank Adaptation, a parameter-efficient fine-tuning method described by Dettmers et al. The method combines quantization of the base model with trainable low-rank adapters. The draft should therefore replace “Quantized Lottery Ticket Hypothesis” with “Quantized Low-Rank Adaptation (QLoRA).” This is a technical terminology correction rather than an interpretive criticism.

\newpage

# 20. Missing Baseline and Ablation Analysis

The draft attributes reported performance to several components: legal-domain data; synthetic instruction generation; summarization; the 400-task instruction framework; dataset expansion; QLoRA; and iterative training. However, the study does not isolate the contribution of each component.

Useful comparisons would include:

1. Base Falcon-7B-instruct;
2. Falcon fine-tuned only on human-selected or human-verified examples;
3. Falcon fine-tuned on synthetic examples;
4. Falcon fine-tuned on independently validated synthetic examples;
5. Falcon trained with and without the summarization stage;
6. Falcon trained on different dataset sizes;
7. Falcon trained with varying proportions of synthetic and human-authored data.

These ablations would help determine whether observed improvements arise from domain adaptation, synthetic supervision, dataset size, task diversity, coverage breadth, or fine-tuning method. Without ablations, attributing gains to “careful dataset preparation” remains difficult.

\newpage

# 21. Problems With the Baseline Comparison

## 21.1 The comparison set is internally inconsistent

The draft’s abstract reports that the fine-tuned model “displayed significant superiority” over “existing LLMs such as GPT-3.5-turbo and Claude,” while its results section describes benchmarking against “GPT-3.5-turbo and GPT-4.” The draft does not state which set of models was actually compared, or whether the comparison set changed between iterations. This internal inconsistency should be resolved before any comparative claim is evaluated.

## 21.2 The strongest claim is not supported by the described controls

The draft states that the fine-tuned Falcon model’s performance against GPT-4 was “notably robust” and describes the outputs as demonstrating:

> “a deep and direct understanding of the case’s impact on law enforcement and the broader societal implications.”

This is a strong claim given the evaluation design. If GPT-4 contributed substantially to synthetic-data generation and was subsequently used as the evaluator, the comparison does not constitute a conventional independent benchmark.

Furthermore, the supplied draft does not provide enough information to determine:

\newpage

- Whether GPT-4 received identical prompts;
- Whether GPT-4 received the same legal context;
- Whether source documents were supplied;
- Whether retrieval was available;
- Whether generation settings were comparable;
- Whether answers were evaluated blindly;
- Whether response order was randomized;
- Whether the evaluator knew which model produced which answer;
- Or whether each model received equivalent contextual information.

These controls matter because comparisons among models can be affected by prompt design and information access independently of model capability. The draft should therefore report the exact prompt; the context provided to each model; model versions; generation parameters; the number of generations; whether responses were anonymized and order randomized; the evaluation protocol; and complete scores.

## 21.3 Additional internal inconsistencies

Two further internal inconsistencies appear in the draft. First, the introduction states that the current instruction set “comprises approximately 3.4k instructions,” whereas the dataset-preparation section states that a total of 3,300 prompts were generated from approximately 50 court cases, and the conclusion refers to “3300 prompts.” The discrepancy is small but unresolved, and it compounds the versioning concern described in Section 15. Second, the draft claims that the Llama 2 model was trained on “the same dataset” as Falcon-7B-instruct, yet the public project artifacts include a distinct Llama 2-format dataset of approximately 4.4k instructions. A reader cannot determine whether the two models were in fact trained on identical corpora, and the comparative claim between them depends on that identity.

# 22. Public Artifacts Do Not Resolve the Evaluation Problem

The availability of public datasets and code is valuable, but public artifacts do not automatically solve the methodological issues identified above.

A repository can improve reproducibility while still containing training-test overlap, synthetic-data errors, narrow source coverage, ambiguous provenance, small evaluation sets, or an evaluator dependent on the training-data generation process. Similarly, a public test dataset does not establish that the model was prevented from seeing it during training.

The relevant questions remain:

1. Was the evaluation data excluded from training?
2. Was the underlying source material excluded?
3. Was the benchmark independently constructed?
4. Was it independently validated?
5. Was the evaluator independent?
6. Were scores reproducible?
7. Were model and dataset versions fixed?

Public availability is therefore necessary for reproducibility, but not sufficient for evaluation validity.

\newpage

# 23. What the Current Evidence Actually Supports

A more conservative interpretation of the study would be:

> The authors demonstrate the feasibility of constructing a domain-specific instruction-tuning dataset from selected Indian legal materials and applying it to open language models. The reported preliminary benchmarking suggests that the resulting model can produce useful responses on selected legal-domain prompts under the evaluation procedure described by the authors.

This is a meaningful result.

What the evidence does not yet establish is:

> The fine-tuned model possesses generally superior legal reasoning compared with GPT-3.5-turbo, GPT-4, Claude, or other general-purpose models.

The distinction is methodological rather than rhetorical. A model can perform well on a selected benchmark without the benchmark establishing broad generalization. The evaluation must distinguish:

- Seen-data familiarity from unseen-data performance;
- Legal knowledge from legal reasoning;
- Language quality from legal correctness;
- Historical legal knowledge from current legal accuracy;
- And evaluator preference from independently validated performance.

\newpage

# 24. Recommended Experimental Revision

A substantially stronger version of the study could retain much of its existing pipeline while replacing or supplementing the evaluation procedure.

## 24.1 Construct a contamination-controlled benchmark

Create a test set from legal documents completely excluded from fine-tuning. The split should occur at the document or case level rather than merely at the question level. Where possible, semantically overlapping passages and derivative questions should also be excluded.

## 24.2 Separate evaluation populations

Report results separately for seen material; held-out material from familiar legal domains; held-out material from unfamiliar legal domains; hypothetical legal reasoning; general-language questions; and source-grounded legal questions. These categories should not be collapsed into a single overall score without qualification.

## 24.3 Introduce independent human evaluation

Have multiple qualified legal-domain evaluators score a representative subset. The rubric should include factual correctness; legal correctness; relevance; completeness; citation accuracy; temporal validity; reasoning quality; unsupported claims; hallucination; and clarity.

## 24.4 Blind the evaluation

Evaluators should not know which model generated a response. For pairwise evaluation, response order should be randomized. Repeated evaluation with reversed order can be used to estimate order sensitivity.

\newpage

## 24.5 Report raw results

Provide the number of examples; mean and, where appropriate, median scores; category-level and item-level results; error rates; confidence intervals; effect sizes; human-versus-LLM agreement; and representative failure cases.

## 24.6 Perform ablations

Evaluate whether the claimed improvements result from more data; synthetic data; legal-domain data; summarization; task diversity; human validation; or the particular fine-tuning method.

## 24.7 Expand legal coverage

The benchmark should include multiple areas of Indian law rather than focusing primarily on a small group of constitutional provisions.

## 24.8 Validate generated legal data

A sample of synthetic examples should be independently checked against primary legal sources. At minimum, the study should report the proportion reviewed; the rejection/error rate; the types of errors found; citation-validation procedures; temporal-law validation; and whether validation occurred before or after fine-tuning.

## 24.9 Freeze dataset versions

Every reported model should be associated with a fixed dataset version and evaluation version (model checkpoint, dataset version, and evaluation version bound together), identified by immutable identifiers or cryptographic hashes where possible.

## 24.10 Report temporal validity

Each legal benchmark item should have a reference date or legal regime against which correctness is judged.

\newpage

# 25. A Proposed Evaluation Framework

A revised evaluation could be organized as follows:


| Evaluation set        | Source relationship to training          | Primary construct            | Recommended evaluator             |
| --------------------- | ---------------------------------------- | ---------------------------- | --------------------------------- |
| Seen questions        | Derived from training data               | Memorization/familiarity     | Automated + descriptive analysis  |
| Held-out same-domain  | New documents within represented domains | Domain generalization        | Legal experts + automated judge   |
| Held-out cross-domain | Legal domains absent from training       | Transfer                     | Legal experts                     |
| Hypothetical cases    | Novel fact patterns                      | Application/reasoning        | Legal experts                     |
| Source-grounded QA    | Explicit authoritative source supplied   | Grounding and legal accuracy | Expert/reference-based scoring    |
| General knowledge     | Outside legal domain                     | General capability control   | Standardized automated evaluation |


This framework would prevent the study from using one aggregate number to represent several fundamentally different capabilities.

\newpage

# 26. Proposed Legal-Expert Scoring Rubric

A practical expert rubric could use a 0–4 scale:


| Criterion         | 0                              | 2                        | 4                                           |
| ------------------------ | -------------------------- | -------------------- | ---------------------------------- |
| Factual accuracy  | Materially false               | Some errors              | No material factual errors                  |
| Legal correctness | Materially incorrect           | Partially correct        | Correct legal rule/application              |
| Authority         | Unsupported or fabricated      | Partially appropriate    | Authorities accurately support claims       |
| Reasoning         | Invalid or absent              | Partially coherent       | Legally coherent and appropriately reasoned |
| Relevance         | Does not answer question       | Partially relevant       | Directly addresses question                 |
| Completeness      | Major components omitted       | Some omissions           | Material issues addressed                   |
| Temporal validity | Uses inapplicable/outdated law | Mixed                    | Correct legal regime                        |
| Hallucination     | Major unsupported claims       | Minor unsupported claims | No material hallucinations                  |
| Clarity           | Confusing                      | Understandable           | Clear and legally precise                   |


Intermediate scores (1 and 3) are assigned when a response falls materially between the adjacent anchor descriptions; the scoring manual should include worked examples at every level, including the intermediate ones. Inter-rater reliability should be reported using a statistic appropriate to the number and type of raters and the scale used.

\newpage

# 27. A More Appropriate Interpretation of LLM-Based Evaluation

LLM evaluation should not be rejected categorically.

It can be useful when the evaluation criteria are explicit; the prompt is fixed; the evaluator is calibrated; the evaluation is blinded; human agreement is measured; multiple judges are used where appropriate; and the task is suitable for automated judgment. LLM-based annotation has been found to outperform crowd workers on some text-annotation tasks, and LLM judges can correlate substantially with human evaluation in particular settings.

The relevant methodological point for LawyerGPT is therefore:

> The first draft does not provide the task-specific validation necessary to establish that GPT-4 is an unbiased or sufficiently reliable evaluator of Indian legal reasoning.

That is a narrower and more defensible claim than rejecting LLM-based evaluation in general.

\newpage

# 28. Relationship to Prior Work on Data Quality and Synthetic Training

The draft draws conceptual inspiration from work emphasizing data quality, instruction tuning, and synthetic-data generation, including LIMA and Textbooks Are All You Need. It also invokes the Orca paper’s observation that strategically prepared datasets can yield higher accuracy and Chain-of-Thought prompting as motivating evidence.

Such work provides motivation for investigating whether carefully constructed data can improve downstream performance. It does not, however, by itself establish that a synthetic-data pipeline will produce legally accurate supervision or that improvements observed in other domains transfer directly to Indian legal reasoning.

This distinction is important because legal-domain synthetic data have additional requirements. A generated answer may be linguistically fluent while containing an incorrect legal proposition; an unsupported citation; an outdated rule; a misreading of a judgment; a distinction omitted from the source; or a plausible but legally indefensible interpretation.

Synthetic-data generation should therefore be treated as a component of the training methodology rather than as independent evidence of legal correctness.

\newpage

# 29. Limitations of This Critique

This critique is based on the first-draft manuscript dated 10 August 2023 supplied for review. It does not assume that later versions, supplementary materials, private evaluation files, or subsequent experiments contain the same omissions.

The critique does not independently inspect every example in every public dataset or retrain the released models. Consequently, it does not claim to have measured the extent of contamination, redundancy, or synthetic-data error. Where the draft explicitly reports training-derived test questions, that fact is treated as documented. Where the critique discusses contamination or redundancy risk, the language is intentionally framed as a methodological concern rather than proof of leakage.

Similarly, this paper does not independently evaluate Falcon-7B-instruct, Llama 2, GPT-3.5-turbo, GPT-4, or Claude on the authors’ benchmark. Its conclusions concern the adequacy and interpretability of the reported methodology, not an independent ranking of the models.

The existence of multiple public dataset versions is not treated as proof that the authors used the wrong dataset. It establishes a reproducibility question: the exact correspondence among dataset versions, training iterations, model checkpoints, and evaluation results should be documented. Artifact row counts and creation dates cited in Section 15 reflect the public record as observed at the time of writing and should be re-verified against dataset cards before publication.

The critique does not argue that synthetic data or LLM-based evaluation are inherently invalid. Both can be useful components of an experimental pipeline. The issue is whether their use in this particular configuration provides sufficient evidence for the draft’s strongest conclusions.

Finally, later literature cited in this critique is used to contextualize methodological issues rather than to retroactively impose requirements that the authors could necessarily have known in 2023. The primary evidence for the critique remains the methodology described in the 10 August 2023 draft.

\newpage

# 30. Conclusion

The LawyerGPT draft presents a potentially useful exploration of domain adaptation for Indian legal language models. The authors’ effort to construct an instruction-tuning corpus from Indian legal material, experiment with synthetic data, and fine-tune open models addresses a legitimate research problem.

However, the first-draft manuscript does not yet provide sufficient methodological evidence for its strongest comparative claims.

The most consequential issue is the evaluation design. GPT-4 is used substantially during synthetic-data construction and is then used as the principal evaluator. This creates a procedural dependence between training-data generation and evaluation. The design does not establish that GPT-4 systematically favored the fine-tuned model, but it does mean that the evaluation is not fully independent of the model family that contributed substantially to the training distribution.

More importantly, the draft explicitly states that the testing set contains questions extracted from the training dataset. Such questions should not be treated as equivalent to genuinely held-out evaluation items. The benchmark also combines general-knowledge questions, fabricated hypothetical cases, training-derived questions, and supposedly unseen questions. These categories test different properties and should not be treated as interchangeable evidence of legal reasoning. “Unseen” is never defined at the document or case level, so even that category cannot be assumed to measure generalization.

The benchmark is additionally small at approximately 15–20 questions per category, while the supplied draft does not provide sufficiently explicit scoring criteria, item-level numerical results, uncertainty estimates, or independent legal-expert validation. The draft is also internally inconsistent about its comparison set. Consequently, the claims that the fine-tuned model “outperformed” GPT-3.5-turbo and exhibited “deep and direct understanding” are stronger than the evidence described in the experimental methodology can currently support.

\newpage

The public project artifacts add a further reproducibility consideration. Multiple versions of the training and testing datasets are publicly identifiable, and the draft itself states that the training set was “dynamically adapted” during training. The draft does not provide a precise mapping between dataset versions, training iterations, model checkpoints, and evaluation results. This does not invalidate the project, but it should be resolved before quantitative claims are treated as reproducible.

There are also several technical and conceptual corrections that should be made. QLoRA should be expanded as Quantized Low-Rank Adaptation rather than “Quantized Lottery Ticket Hypothesis.” The claim that 4-bit quantization does not compromise performance should be expressed empirically rather than categorically. The use of Textbooks Are All You Need should be described as inspiration from work on data quality and synthetic code-training data rather than as a validated legal summarization methodology.

The appropriate interpretation is therefore narrower. The study demonstrates the feasibility of constructing and applying an instruction-tuning pipeline based on selected Indian legal material. Its reported results are suggestive of task-specific performance improvements under the evaluation procedure used. The first draft does not establish broad superiority in Indian legal reasoning over larger general-purpose models.

This distinction is important but constructive. The underlying project need not be abandoned. A revised study with document-level train-test separation, independently constructed evaluation data, transparent scoring criteria, blinded assessment, legal-expert validation, larger and more diverse benchmarks, explicit dataset versioning, synthetic-data quality assurance, temporal legal validation, and ablation experiments could substantially strengthen the empirical contribution.

The central methodological lesson is straightforward:

> In legal-domain LLM research, dataset scale, fluent output, and LLM-based evaluation are not by themselves evidence of legal competence.

\newpage

Claims about legal reasoning require evaluation procedures capable of distinguishing memorization from generalization; question novelty from document-level novelty; legal knowledge from legal reasoning; linguistic quality from legal correctness; historical legal knowledge from current legal accuracy; and evaluator preference from independently validated performance.

The LawyerGPT project provides a useful starting point for such an investigation. Its next methodological step should be to demonstrate that the reported improvements survive evaluation under conditions in which the training material, benchmark, evaluator, scoring procedure, and dataset versions are independently controlled and transparently documented.

\newpage

# References

\[1\] Behera, P., &amp; Agharia, N. (2023). LawyerGPT-Trained on Indian Legal Dataset. Unpublished first-draft manuscript, 10 August 2023.

\[2\] Dettmers, T., Pagnoni, A., Holtzman, A., &amp; Zettlemoyer, L. (2023). QLoRA: Efficient finetuning of quantized LLMs. Advances in Neural Information Processing Systems, 36.

\[3\] Touvron, H., Martin, L., Stone, K., Albert, P., Almahairi, A., Babaei, Y., Bashlykov, N., et al. (2023). Llama 2: Open foundation and fine-tuned chat models. arXiv preprint arXiv:2307.09288.

\[4\] Taori, R., Gulrajani, I., Zhang, T., Dubois, Y., Li, X., Guestrin, C., Liang, P., &amp; Hashimoto, T. B. (2023). Stanford Alpaca: An instruction-following LLaMA model. GitHub repository.

\[5\] OpenAI, Achiam, J., Adler, S., Agarwal, S., Ahmad, L., Akkaya, I., Aleman, F. L., Almeida, D., et al. (2023). GPT-4 technical report. arXiv preprint arXiv:2303.08774.

\[6\] Chalkidis, I., Fergadiotis, M., Malakasiotis, P., Aletras, N., &amp; Androutsopoulos, I. (2020). LEGAL-BERT: The Muppets straight out of Law School. Findings of the Association for Computational Linguistics: EMNLP 2020, 2898–2904.

\[7\] Gilardi, F., Alizadeh, M., &amp; Kubli, M. (2023). ChatGPT outperforms crowd workers for text-annotation tasks. Proceedings of the National Academy of Sciences, 120(30), e2305016120.

\[8\] Chiang, C.-H., &amp; Lee, H.-Y. (2023). Can large language models be an alternative to human evaluations? Proceedings of the 61st Annual Meeting of the Association for Computational Linguistics (Volume 1: Long Papers), 15607–15631.

\[9\] Zheng, L., Chiang, W.-L., Sheng, Y., Zhuang, S., Wu, Z., Zhuang, Y., Lin, Z., Li, Z., Li, D., Xing, E. P., Zhang, H., Gonzalez, J. E., &amp; Stoica, I. (2023). Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena. Advances in Neural Information Processing Systems, 36, 46595–46623.

\[10\] Xu, C., Guan, S., Greene, D., &amp; Kechadi, M.-T. (2024). Benchmark data contamination of large language models: A survey. arXiv preprint arXiv:2406.04244.

\[11\] Palavalli, M., Bertsch, A., &amp; Gormley, M. (2024). A taxonomy for data contamination in large language models. In Proceedings of the 1st Workshop on Data Contamination (CONDA), 22–40.

\[12\] Zhou, C., Liu, P., Xu, P., Iyer, S., Sun, J., Mao, Y., Ma, X., Efrat, A., Yu, P., Yu, L., Zhang, S., Ghosh, G., Lewis, M., Zettlemoyer, L., &amp; Levy, O. (2023). LIMA: Less is more for alignment. Advances in Neural Information Processing Systems, 36.

\[13\] Gunasekar, S., Zhang, Y., Aneja, J., Mendes, C. C. T., Del Giorno, A., Gopi, S., Javaheripi, M., Kauffmann, P., de Rosa, G., Saarikivi, O., Salim, A., Shah, S., Behl, H. S., Wang, X., Bubeck, S., Eldan, R., Kalai, A. T., Lee, Y. T., &amp; Li, Y. (2023). Textbooks Are All You Need. arXiv preprint arXiv:2306.11644.

\[14\] Agharia, N. (2023). Indian-LawyerGPT. Public GitHub repository containing the research paper, training/inference code, and associated datasets.

\[15\] Agharia, N. (2023). Public datasets under the nisaar namespace. Hugging Face.

\[16\] Agharia, N. (2023). Lawyer\_GPT\_India. Hugging Face dataset.

\[17\] Agharia, N. (2023). Articles\_Constitution\_3300\_Instruction\_Set. Hugging Face dataset.

\[18\] Agharia, N. (2023). LLAMA2\_Legal\_Dataset\_4.4k\_Instructions. Hugging Face dataset.

\[19\] Mukherjee, S., Mitra, A., Awadallah, H., et al. (2023). Orca: Progressive learning from complex explanation traces of GPT-4. arXiv preprint arXiv:2306.02707.

\[20\] Wei, J., Wang, X., Schuurmans, D., Bosma, M., Ich, Y., Xia, F., Chi, E., Le, Q. V., &amp; Zhou, D. (2022). Chain-of-thought prompting elicits reasoning in large language models. Advances in Neural Information Processing Systems, 35, 24824–24837.
