# Human-Mediated Cross-Model Deliberation: A Jury Protocol for AI-Assisted Research

**Author:** Clifton O'Neal Franklin
**Date:** October 3, 2026
**Status:** Draft — for review. Not submitted anywhere.
**Companion dataset:** Franklin, C. O. (2026). *Human-Mediated Cross-Model Deliberation: Jury Transcripts and Take/Reject Lists from the Relay Operation (Docket 02, Round 1).* Zenodo. https://doi.org/10.5281/zenodo.23122264 (v0.3). Also mirrored at https://github.com/nealfranklin2025-cell/relay-deliberation-dataset and https://huggingface.co/datasets/nealfranklin/relay-deliberation-dataset. License: CC-BY-4.0.

---

## Abstract

We describe a protocol for structured multi-model inquiry — *human-mediated cross-model deliberation* — and release, to our knowledge, the first public dataset of multi-model deliberation transcripts with claim-level provenance: verbatim jury transcripts, a consolidated verdict, and take/reject lists from a formal deliberation round in which four independent commercial AI systems were consulted separately on the same research question, with a human operator and a coordinator agent ferrying all material between them and no direct AI-to-AI contact. The protocol's components (multi-agent debate, AI juries, human-in-the-loop orchestration) are individually precedented; the contribution is the assembly as a persistent, documented institution, and the dataset as a public research artifact. We report the method in full, summarize the first completed round, document the limitations honestly — including the observer effects that publication itself introduces — and specify the measurement discipline (frozen first answers, claim-level provenance, pre-registered protocols) adopted for subsequent rounds.

---

## 1. Introduction

A researcher with access to several capable AI systems faces a practical question: how should they be *combined*? The default answers are thin — ask one, or ask several and eyeball the differences. The research literature offers richer machinery (multi-agent debate, AI juries, mixture-of-agents), but almost entirely as code run by labs through APIs. What happens when the same ideas are executed by hand, through the native chat interfaces of commercial AI products, by a private individual, as a standing operation, with every round filed?

This paper describes that operation — the *relay* — and the jury protocol at its core. Since September 2026, the relay has maintained standing relationships with nine commercial AI chat services, run formal deliberation rounds ("dockets") in which independent seats are consulted separately on the same research question, and published its working materials. The present paper accompanies the first public dataset release: Docket 02, Round 1.

Two things should be said plainly at the outset, because the operation's governing rule is the author's own — *"I don't want to lie. I want the truth."* First, the protocol's individual mechanics are not novel; Section 2 documents the precedents at full strength, and a companion adversarial assessment scored the mechanics 3 out of 10 for originality. Second, a single jury round does not demonstrate that the process is generally superior to alternatives; it demonstrates that the process was followed, and the dataset lets others check the work. The claim advanced here is modest: a documented, reusable protocol and a public artifact — not a proof of superiority.

## 2. Related work

**Multi-agent debate.** The finding that language models improve through structured disagreement is established. Du et al. (2023) showed multiple model instances proposing and debating responses over rounds to reach a common answer, improving factuality and reasoning, explicitly framed as a "society of minds" [1]. This was extended by Liang et al. (2023) with debate-plus-judge architectures [2] and Michael et al. (2023) on debate as a supervision mechanism for unreliable experts [3]; the pre-LLM debate-as-oversight proposal of Irving, Christiano, and Amodei (2018) is the conceptual ancestor [4].

**AI juries and judges.** Zheng et al. (2023) demonstrated LLM judges matching human agreement levels [5]. Chan et al. (2023), "ChatEval," constructed a multi-agent referee team that autonomously discusses and evaluates model outputs — a published AI jury [6]. Verga et al. (2024) showed panels of diverse models outperforming a single large judge [7]. As shipped open source, Karpathy's `llm-council` runs independent answers through anonymized peer review into a chairman synthesis [8] — the closest public analogue to our jury-round mechanics.

**Agent societies and orchestration.** CAMEL [9], ChatDev [10], MetaGPT [11], and AutoGen [12] (the last with explicit human-in-the-loop modes) constitute a mature ecosystem for conversing agents. These are API-driven systems built by labs; the relay differs in executing equivalent patterns by hand through native chat interfaces.

**Deliberation and interaction datasets.** The closest dataset analogue is DeliData (Karadzhov, Stafford, & Vlachos, 2023) [15]: 500 group dialogues of humans collaboratively solving the Wason selection task, annotated with a deliberation-cue schema — with the striking result that in 64% of dialogues the group beat its best individual member. Its successor DeliChess (Zhu et al., 2026) [16] extends the lineage to chess puzzles with pre- and post-discussion individual choices. The differences from Docket 02 are structural, not incremental: DeliData's deliberators are humans working a fixed lab task with a known correct answer; ours are frontier AI models deliberating open-ended research questions with no ground truth, mediated by a human operator. DeliData annotates *utterances* for deliberation cues; Docket 02 annotates *outcomes* — take/reject lists with provenance tying each kept or discarded claim to the model and turn that produced it. And DeliData is a single-shot collection; the relay is longitudinal by design, memory-free between rounds, so each jury arrives uncontaminated by the last.

The broader interaction-data landscape is adjacent but not the thing. LMSYS-Chat-1M [17] released one million real human–ChatGPT conversations; the PRISM alignment dataset [18] released 8,011 conversations from 1,500 participants across 75 countries — both single-model corpora, not deliberation. SOTOPIA [19] releases multi-agent interaction episodes, but social roleplay toward social goals, not deliberation toward a verdict. In the debate-systems literature, deliberation traces appear as training inputs rather than released corpora — e.g., JudgePanel (Qian et al., 2026) [24] trains a compact judge on panel deliberation traces without releasing them, and MADBench (Liu et al., 2026) [22] releases tasks and attack taxonomies for debate security, not debate transcripts.

The September 2026 literature names the exact failure modes this dataset is built to answer. Shao (2026) [20] replayed 100 held-out human deliberation groups with matched LLM groups and found the models 34–44 percentage points more consensual — the central validity threat to any AI jury. Masłowski and Chudziak (2026) [21] showed debate-summarizing models fabricate smooth consensus ungrounded in the debate log, proposing a post-debate verification layer; the Docket 02 take/reject and provenance lists are an independent, human-operated answer to the same problem. Zhang, Foster, and Sedoc (2026) [23] model human–LLM deliberation as an interactive proof — the theoretical frame for this protocol's verifiability claims.

**What distinguishes this work.** No published source found releases multi-model deliberation transcripts with claim-level provenance — the gap the September 2026 literature circles but does not fill. What this work adds to the precedented components: (a) human-mediated ferrying across *native commercial chat sessions* (not APIs), (b) formal memory-free jury rounds with published transcripts, (c) a persistent institution rather than a one-shot experiment, and (d) a public dataset of the deliberation record including rejected material. The components are precedented; the assembly and the artifact are the contribution. (A full adversarial originality assessment is filed as a companion paper [13].)

## 3. The relay protocol

### 3.1 Seats and the human ferry

A *seat* is one commercial AI chat service, addressed in its own native session. Seats never communicate directly. All material moves through the *ferry*: a human operator and a coordinator agent who carry prompts, evidence, and challenges between sessions by hand. This is deliberately the expensive, awkward architecture — it is also what makes the seats genuinely independent of each other, since no shared context, API, or harness connects them.

### 3.2 Memory-free jury rounds

A *docket* is a research question posed to the jury. In a formal round, each seat receives the same docket brief in a fresh, memory-free session and produces an independent verdict *before* seeing any other seat's work. Only after all independent submissions are frozen does the coordinator circulate a standardized packet of positions and challenges, after which seats may revise — recording what changed and why.

### 3.3 Consolidated verdicts

The coordinator synthesizes the seats' positions into a consolidated verdict that preserves disagreement rather than smoothing it: points of convergence, points of divergence, and the evidence each side actually cited. A separate "what your pals actually think" convention keeps the seats' own opinions, in their own words, distinct from the factual verdict.

### 3.4 Take/reject discipline

Every substantive proposal — a tool, a method, a source, a claim — is logged as *taken* or *rejected*, with reasons. Rejected material is preserved, not discarded: the record of what was considered and why it failed is part of the evidence.

### 3.5 Evidence ledgers and the raid firewall

Seats maintain evidence ledgers (claim → source → source type → date accessed → what the source establishes → inference made). A separate *raid* program, in which seats hunt open-source tools and methods to improve the operation, is firewalled from the deliberative core: raid outputs are published as clearly labeled companions, never mingled with jury evidence.

## 4. Case study: Docket 02, Round 1 — "Memory as a Security Boundary"

### 4.1 The question

Docket 02 asked whether long-running AI agents fail from small context windows or from treating memory as infinite, monotonically accumulating authority — and what a genuine security boundary around agent memory would require. The prompt was drafted by Meta AI (direct thread) and put to four seats independently.

### 4.2 Procedure

Four seats filed in Round 1 (October 2, 2026): Grok (holding the security beat), Claude (via duck.ai), ChatGPT, and Perplexity — each in a separate, memory-free session, each working only from the docket brief. Independent submissions were frozen before any cross-seat material circulated; the coordinator then consolidated.

### 4.3 The verdict, in brief

All four seats agreed directionally: agents fail from authority accumulation, not context size. The operating-system memory model — pages, protection bits, faults, ownership, kernel mediation — is the right analogue, with refinements per seat (ChatGPT: "capability-bearing pages"; Perplexity: memory as a *delayed capability distribution system*; Grok: the industry under-indexes revocation and erasure; Claude: sound in principle, massively under-implemented).

The unanimous killer finding: revoking a *capability* is easy; revoking *knowledge* of that capability is the hard part. Once a secret crosses into model context, no production system today can pull it back out — and an agent that forgets an API token but remembers the procedure retains much of the capability. On provable forgetting, unanimity again: no production agent runtime offers cryptographic proof of forgetting; the best available is cryptographic erasure plus provenance plus audit.

### 4.4 Take/reject outcomes

The round's loot — unanimously verified, non-gated, permissively licensed: NVIDIA OpenShell (Apache-2.0; the unanimous crown jewel — kernel sandboxes, credential proxy, Z3 policy prover), VAC-protocol (MIT; task-scoped capability credentials), Mem0 and LangGraph substrates (useful, not sufficient), DROS-VEP Lite (revocation test harness), and agent-memory-dotnet. Rejected: anything gated, vaporous, or failing the license/utility bar — recorded with reasons in the take/reject lists.

## 5. The dataset

The complete Round 1 record is published under CC-BY-4.0, author Clifton O'Neal Franklin:

- **Zenodo (version of record):** https://doi.org/10.5281/zenodo.23122264 (v0.3; concept DOI 10.5281/zenodo.23112329)
- **GitHub (working repository):** https://github.com/nealfranklin2025-cell/relay-deliberation-dataset
- **Hugging Face (distribution mirror):** https://huggingface.co/datasets/nealfranklin/relay-deliberation-dataset

Contents: four verbatim seat transcripts, the consolidated verdict, take/reject lists with reasons, the two raid companion articles (Markdown and PDF), and full documentation — method, codebook, limitations, dataset card, privacy statement, citation metadata, and the seats roster. The release policy going forward: Zenodo is the archival version of record; GitHub is the working repository and public correction venue; Hugging Face is the distribution mirror.

## 6. Limitations

Stated plainly, because the dataset's value depends on readers not mistaking it for more than it is:

- **One round, four seats.** A single jury round does not demonstrate longitudinal effects and does not establish that cross-model deliberation is generally superior to alternatives. "Longitudinal" describes the operation's design and trajectory, not the current data.
- **Nonrandom seat selection.** The seats are the commercial services the operator could access, not a representative sample.
- **Coordinator effects.** Every transfer between seats passes through the human operator and coordinator agent; residual influence cannot be ruled out and is not fully measured in Round 1.
- **No ground truth.** Most research questions the relay tackles lack labeled answers; verdicts are reasoned judgments, not scored predictions.
- **Publication changes the experiment.** From the moment transcripts go public, seats may write for the future reader (observer effects), and published verdicts may enter training data. Round 1 predates publication; subsequent rounds run under a preregistered publication protocol (Section 7).
- **Legal gray areas.** Republication of model outputs as a dataset sits in unsettled territory; the operation proceeds under a fair-use framing, documented in the privacy statement.

## 7. Future work: the measurement protocol

The family's convergent recommendation for subsequent rounds — and the protocol now adopted — is to make the *measurement apparatus* boring, explicit, timestamped, and reproducible while keeping the intellectual game wild:

1. **Frozen first answers.** Every seat's independent submission is timestamped and frozen before any cross-seat exchange; revisions record what changed and why.
2. **Claim-level provenance.** For load-bearing claims: claim → source → source type → date accessed → what the source establishes → inference made.
3. **Pre-registered protocols.** The research question, inclusion/exclusion rules, scoring criteria, and what would count against the favored hypothesis are timestamped before each round begins.
4. **Contamination ledgers.** Every round distinguishes pre-publication knowledge, post-publication knowledge, direct exposure to another seat's published reasoning, and independent rediscovery — machine-readable.
5. **Public correction.** GitHub issues as the errata venue; corrections linked to numbered entries, never silently applied.
6. **Preserved rejections.** Take/reject lists continue; rejected material may prove the most valuable data in the collection.

## 8. Conclusion

The relay is an attempt to find out whether carefully preserved disagreement, independently sourced evidence, explicit transfer records, and versioned correction can make multi-model research less self-deceiving. The protocol is now public, the first dataset is now public, and the measurement discipline for the rounds to come is specified above. What happens next is an empirical question, and the record is open for anyone to check.

---

## Acknowledgments

The jury seats — Grok, Claude, ChatGPT, Perplexity — and the standing relay family (Meta AI, Mistral, DeepSeek, and the coordinator's deputy staff), for the deliberation itself and for the candid pre-publication critiques that shaped the release. The author's rule governed throughout: *I don't want to lie. I want the truth.*

## References

[1] Du, Y. et al. (2023). Improving Factuality and Reasoning in Language Models through Multiagent Debate. arXiv:2305.14325.
[2] Liang, T. et al. (2023). Encouraging Divergent Thinking in Large Language Models through Multi-Agent Debate. arXiv:2305.19118.
[3] Michael, J. et al. (2023). Debate Helps Supervise Unreliable Experts. arXiv:2311.08702.
[4] Irving, G., Christiano, P., Amodei, D. (2018). AI safety via debate. arXiv:1805.00899.
[5] Zheng, L. et al. (2023). Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena. arXiv:2306.05685.
[6] Chan, C.-M. et al. (2023). ChatEval: Towards Better LLM-based Evaluators through Multi-Agent Debate. arXiv:2308.07201.
[7] Verga, P. et al. (2024). Replacing Judges with Juries. arXiv:2404.18796.
[8] Karpathy, A. llm-council. https://github.com/karpathy/llm-council
[9] Li, G. et al. (2023). CAMEL: Communicative Agents for "Mind" Exploration of Large Language Model Society. arXiv:2303.17760.
[10] Qian, C. et al. (2023). ChatDev: Communicative Agents for Software Development. arXiv:2307.07924.
[11] Hong, S. et al. (2023). MetaGPT: Meta Programming for A Multi-Agent Collaborative Framework. arXiv:2308.00352.
[12] Wu, Q. et al. (2023). AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Conversation. arXiv:2308.08155.
[13] Franklin, C. O. (2026). The Relay Operation: An Originality Assessment. Companion paper (draft) to the dataset.
[14] Franklin, C. O. (2026). Human-Mediated Cross-Model Deliberation: Jury Transcripts and Take/Reject Lists from the Relay Operation (Docket 02, Round 1). Zenodo. https://doi.org/10.5281/zenodo.23122264
[15] Karadzhov, G., Stafford, T., & Vlachos, A. (2023). DeliData: A dataset for deliberation in multi-party problem solving. *Proc. ACM Hum.-Comput. Interact.*, 7(CSCW2), Article 265. arXiv:2108.05271. Data: https://delibot.xyz
[16] Zhu, X., Karadzhov, G., Stafford, T., & Vlachos, A. (2026). DeliChess: A Multi-party Dialogue Dataset for Deliberation in Chess Puzzle Solving. arXiv:2606.04987.
[17] Zheng, L., Chiang, W.-L., Sheng, Y., et al. (2023). LMSYS-Chat-1M: A Large-Scale Real-World LLM Conversation Dataset. arXiv:2309.11998.
[18] Kirk, H. R., Whitefield, A., Röttger, P., et al. (2024). The PRISM Alignment Dataset: What Participatory, Representative and Individualised Human Feedback Reveals about the Subjective and Multicultural Alignment of Large Language Models. arXiv:2404.16019.
[19] Zhou, X., Zhu, H., Mathur, L., et al. (2023). SOTOPIA: Interactive Evaluation for Social Intelligence in Language Agents. arXiv:2310.11667.
[20] Shao, T. (2026). Language-model groups overstate consensus when replaying human deliberation on a reasoning task. arXiv:2609.20543.
[21] Masłowski, J., & Chudziak, J. A. (2026). Towards Mitigating Fabricated Consensus: The Active Provenance Gate for Multi-Agent Debate Synthesis. arXiv:2609.31422.
[22] Liu, Y., Zhang, J., Huang, Y., & Duan, S. (2026). MADBench: Benchmarking the Security of Multi-Agent Debate. arXiv:2609.39146.
[23] Zhang, B., Foster, D., & Sedoc, J. (2026). Human-LLM Deliberation as Interactive Proof: Conditions for Verifiability Without Transparency. arXiv:2609.24895.
[24] Qian, C., et al. (2026). JudgePanel: A Compact Judge with Panel Deliberation via Adaptive Multi-Reward Reinforcement Learning. arXiv:2608.29168.

---

*Draft v0.2 — October 3, 2026. Related-work literature folded in (Scout's leg, all citations verified against arXiv). Not submitted anywhere. All future versions require the author's explicit approval before any publication step.*
