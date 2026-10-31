---
title: "Reading the Intent Behind the Fake: Reverse Prompt Engineering and the Fight Against AI Deception"
date: 2026-10-31
image: cover.jpg
tags: 
  - Reverse Prompt Engineering
categories: 
  - Research
---

# We can't spot fake content anymore

A systematic review of 56 studies, covering more than 86,000 participants, found that humans detect deepfakes with about 55% accuracy. That is a coin flip. For AI-generated *text*, we drop to roughly 52%, worse than a coin flip.

Meanwhile, the flood of synthetic content keeps growing. Industry threat reports suggest generative-AI-driven phishing has surged by over 1,200% since ChatGPT launched, and multimodal deepfake fraud attempts have reportedly grown more than twentyfold in three years. In one documented case, a finance employee joined a video call with what looked like his company's CFO and several colleagues, every one of them a deepfake,and transferred $25 million to scammers.

Even the machines struggle. Commercial AI detectors that claim 95–99% accuracy in the lab can fall to around 78% on real-world content that has been compressed, edited, or reposted. Watermarks, the invisible "made by AI" signatures embedded in generated content,can be stripped, spoofed, or simply broken by the next model update.

So if neither people nor software can reliably answer *"is this fake?"*, maybe we're asking the wrong question. A more useful one is:

> **What was this content made to do, and who benefits?**

That question is what our research is about.

# First, a quick primer: what's a prompt?

Every piece of AI-generated content, an email, an image, a cloned voice, a "news" article, started with an instruction. That instruction is called a **prompt**.

Crafting those instructions carefully so the AI produces exactly what you want is called **prompt engineering**. It's a normal, legitimate craft: marketers, developers, and designers all do it.

But here's the thing: the prompt is where the *intent* lives. A disinformation campaign isn't a random accident of a misfiring model, it's usually a carefully engineered prompt, designed to bypass an AI's safety guardrails and mass-produce persuasive lies. The prompt is the smoking gun we normally never get to see.

Which raises an obvious idea: what if we could work *backwards*?

# Reverse prompt engineering: reading the recipe from the cake

**Reverse prompt engineering (RPE)** is exactly that: given only a piece of AI-generated output, infer the instruction that most likely produced it.

Think of it like tasting a cake and reconstructing the recipe. You can't be sure your version is *the* original, but a close reconstruction tells you a lot about what the baker set out to make.

The field got a serious push from researchers Hanqing Li and Diego Klabjan at Northwestern University. Their 2024 work on reverse prompt engineering ([arXiv:2411.06729](https://arxiv.org/html/2411.06729v3)) showed something surprising: you can recover a hidden prompt from as few as **five** outputs of a language model, with no access to the model's internals, no training data, and no special tools. Earlier methods needed 64 or more outputs and expensive training; theirs needs a handful of samples and a clever search process.

That search process borrows from evolution. Their method generates a batch of candidate prompts, tests how well each one reproduces the observed output, keeps the best performers, and breeds improved versions of them,mutating and combining the strongest candidates, generation after generation, until the candidates converge on a prompt that behaves like the real one. No new model is trained. The AI, in effect, reverse-engineers its own instructions.

The results beat the previous state of the art, and, just as importantly, the recovered prompts read like natural language, not garbled text.

This work matters for one big reason: **it proved that prompts are not really secrets.** If a system prompt hidden behind a chatbot can be reconstructed from a few of its answers, everyone building products on top of "confidential" prompts needs to think again. And every investigator trying to understand malicious AI content got a new tool.

# Our first study: mapping the horizon

That breakthrough inspired our team's first contribution: a **horizon scan**,a systematic survey of where this emerging field is and where it's going. In *"Reverse Prompt Engineering: A Horizon Scan of Prompt Attribution and Recovery in Generative AI"*, we analysed academic and industry developments and found a field moving fast, but unevenly.

Three findings stand out:

**1. The toolbox is real and growing.** Beyond Li and Klabjan's genetic approach, methods like Output2Prompt, DORY, IPAD, and XPrompt recover prompts through learned mappings, uncertainty estimates, or detection pipelines. Different techniques, same goal: making the invisible instruction visible.

**2. There's a "modality gap."** Nearly all working RPE tools handle *text*. But the most dangerous deepfakes are images, audio, and video,and there, inversion gets much harder. The same prompt can produce a different image every time; totally different prompts can produce nearly identical pictures. The link between instruction and output simply isn't as direct.

**3. Industry transparency is thin.** Major providers offer watermarking (Google's SynthID, for example), but watermarks identify *outputs*, not *instructions*. They don't tell you what the content was asked to do. Community efforts like CL4R1T4S, which collects and publishes the hidden system prompts of commercial chatbots, remain the exception rather than the rule.

From this scan we proposed a conceptual framework for future RPE systems,multi-modal, privacy-conscious, and grounded in clear evaluation metrics,and mapped out use cases from academic integrity to digital forensics to regulatory compliance.

# Our latest work: a baseline for fighting deepfakes, deception, and disinformation

The horizon scan told us where the field was. Our newest paper, *"Reverse Prompt Engineering in Generative AI: A Baseline for Deepfake, Deception and Disinformation Mitigation,"* goes a step further: it positions RPE explicitly as a **defensive capability** against what we call the **D3 threats**,**Deepfakes, Deception, and Disinformation**.

The core idea is a shift in how we do forensics:

- **Old question:** "Is this artefact AI-generated?" (binary classification)
- **New question:** "What strategic intent shaped it?" (semantic auditing)

Because disinformation campaigns are *engineered*, built from prompt templates crafted to slip past safety filters,auditing the instruction layer lets investigators uncover a campaign's objectives, spot the jailbreak techniques used, and cluster content from the same source.

We're careful, though, not to oversell it. RPE delivers three distinct capabilities, each with different evidentiary weight:

1. **Prompt recovery**, a *plausible* reconstruction of the instruction, not certified truth. Many different prompts can explain the same output, so recovery gives you a ranked list of candidates, not a confession.
2. **Prompt attribution**, a *probabilistic* estimate of which model family produced the content, based on stylistic fingerprints. It should come with a confidence level, never presented as proof.
3. **Content provenance**, verification of origin and history, strongest when RPE findings are *corroborated* by independent metadata or cryptographic signals.

A concrete example from the paper: a company runs an ad featuring an AI-generated celebrity likeness without permission, then claims it was an "accidental hallucination", the prompt only asked for a "generic person", they say. RPE can't prove the company lied. But it can produce a ranked list of plausible prompts, and a candidate like *"hyperrealistic photo of \[Celebrity\] holding \[Brand\] wristwatch, authentic endorsement style"* becomes an investigative lead. Combined with subpoenaed prompt logs or repeated regenerations converging on the same result, it *supports*,though never single-handedly proves, that the resemblance was deliberate.

That honesty about limits is deliberate. In a courtroom or a newsroom, a recovered prompt is one piece of evidence, not a verdict.

# The honest caveats

RPE is promising, not magic. Three constraints matter most:

- **The modality gap is still open.** Today's frameworks recover *text* prompts. Extending them to images, audio, and video, the media where deepfakes do the most damage, remains the field's hardest unsolved problem.
- **It can be evaded.** Small changes after generation, swapping synonyms, light colour grading, can disrupt the stylistic signatures RPE relies on.
- **It's heavy.** Iterative optimisation takes time and compute, making RPE better suited to *post-incident investigation* than real-time filtering of millions of posts.

And there's a darker side we can't ignore: RPE is dual-use. The same techniques that help investigators could be used to steal a company's proprietary prompts, clone a commercial service, or de-anonymise people whose personal data leaks into their queries. That's why our paper pairs the technical case with a legal and policy one, including the need for ethical guidelines and licensing for RPE tools.

# Why the law should care

Legally, prompts occupy a strange no-man's-land. Most jurisdictions don't consider raw prompts copyrightable,they're treated as functional instructions. So companies fall back on trade secret law and contracts, and unauthorised prompt extraction is already being litigated (in 2025, *OpenEvidence v. Pathway Medical* framed prompt extraction as trade secret misappropriation). Importantly, *defensive* RPE,security auditing and disinformation forensics,typically falls under research exceptions and fair-use-style protections.

On the policy side, regulation is arriving faster than the tools. The EU AI Act, for example, requires that synthetic outputs be clearly marked as AI-generated. But what happens when bad actors strip the mark? RPE offers a *secondary auditing mechanism*: an independent way to check what a piece of content was instructed to do when the label is missing.

To make that practical, we propose **prompt governance**,treating the instruction layer as something organisations actively monitor and secure, the way they manage code or user data today. That means version control and audit trails for system prompts, standardised "ingredient label" disclosures for public-facing models, and runtime auditing that catches jailbreak attempts before they reach the model. International standards like ISO 42001 and the NIST AI Risk Management Framework give this a ready-made home.

# Why this matters to you, not just to researchers

Two psychological effects make AI deception a problem for everyone:

- **The illusory truth effect:** the more often we see a claim, the more likely we are to believe it,even if it's synthetic. Disinformation campaigns exploit this by flooding channels with repetition.
- **The liar's dividend:** once the public knows deepfakes exist, bad actors can dismiss *genuine* evidence as fake. The flood of synthetic content doesn't just create lies,it destroys our ability to agree on truth.

Defences that only say "fake" or "not fake" feed both problems. A tool that can say *"this content is consistent with an attempt to defame candidate X using neutral-sounding language"* gives journalists, fact-checkers, and courts something far more useful: insight into *intent*.

# Where we go from here

Our research roadmap is straightforward, if ambitious:

1. **Close the modality gap**,extend prompt recovery from text to images, audio, and video.
2. **Test robustness in the wild**,against compressed, edited, adversarially obfuscated content, using realistic benchmarks.
3. **Standardise**,shared datasets and evaluation metrics so results can be compared and trusted.
4. **Integrate into governance**,make prompt-level auditing a first-class part of AI accountability, not a forensic afterthought.

The question "is this fake?" is one we're already losing. But "what did the author ask the machine to do, and why?" is a question we can still learn to answer. That's the bet we're placing.

---

*This post draws on our papers "Reverse Prompt Engineering: A Horizon Scan of Prompt Attribution and Recovery in Generative AI" and "Reverse Prompt Engineering in Generative AI: A Baseline for Deepfake, Deception and Disinformation Mitigation," with background from Li and Klabjan's foundational work on reverse prompt engineering ([arXiv:2411.06729](https://arxiv.org/html/2411.06729v3)). Threat statistics are drawn from industry threat-intelligence telemetry and should be read as directional evidence of scale rather than audited measurements.*