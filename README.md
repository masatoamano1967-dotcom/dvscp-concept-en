# DVSCP — Dimensional Vector Semantic Compression Protocol

**Author:** Masato Amano  
**Status:** Concept Validation Phase — Patent Application Pending  
**Version:** v5.1

---

## Overview

DVSCP is a proposed communication protocol that extracts only the "semantic core" from text, compresses it to a minimal representation, and relies on AI at the receiving end to reconstruct the original message.

```
[Original Text] → Semantic Extraction (Rule-based) → Compressed Packet → AI Reconstruction → [Restored Text]
```

> Japanese repository: [dvscp-concept-ja](https://github.com/masatoamano1967-dotcom/dvscp-concept-ja)

---

## Core Ideas

### Separation of Concerns

- **Sender (Encoder):** No AI involved. Uses only deterministic rules to generate a semantic "seed."
- **Receiver (Decoder):** Leverages a large language model to reconstruct the full message from the seed.

This design means reconstruction quality improves automatically as AI models advance — with no changes needed to the encoder.

### Semantic Inertia

Once a context (inertia) is established in a sentence, many subsequent words become predictable from it. DVSCP exploits this by **omitting information that can be inferred from inertia**.

> "At the moment a word is born, it already contains a direction toward the next word."

### Three-tier Classification

| Class | Packet size | Target information |
|---|---|---|
| **Anchor** | Several bytes | Inertia-breaking tokens (proper nouns, negations, pivots) |
| **Inertia** | Minimal | Tokens predictable from current context |
| **Skip** | 0 bytes | Grammatical elements AI can reconstruct |

---

## Benchmark Results

### English

| Text type | Original | Compressed | Reduction |
|---|---|---|---|
| Narrative | 226 bytes | 85 bytes | **62.4%** |
| Technical explanation | 290 bytes | 53 bytes | **81.7%** |
| Negation + contrast | 181 bytes | 64 bytes | **64.6%** |
| Technical (long) | 471 bytes | 67 bytes | **85.8%** |
| **Total** | **1,168 bytes** | **269 bytes** | **77.0%** |

### Japanese

| Metric | Result |
|---|---|
| Original size | ~3,300–4,400 bytes |
| Compressed size | 508–1,158 bytes |
| **Reduction** | **77–85%** |
| Core intent preservation | 100% (verified across 4 independent AI systems) |

**Key finding:** English and Japanese compression ratios are comparable (~77%), suggesting semantic inertia is a **language-independent principle**.

See [`examples/sample_results.md`](./examples/sample_results.md) for detailed examples.

---

## Potential Applications

- **Low-bandwidth communication:** Space, underwater, disaster-response scenarios
- **Storage:** Archiving large volumes of text as semantic skeletons
- **Cross-language translation:** Natural multilingual reconstruction via semantic layer
- **Voice extension:** Research ongoing into audio DVSCP integrating pitch, emotion, and intonation

---

## Guardrail Mechanisms

Three safety mechanisms prevent meaning drift during compression:

1. **Mandatory Anchor Protocol (MAP):** Forces an anchor token after a set number of consecutive inertia tokens, preventing semantic drift.
2. **Uncertainty Echo (UE):** Allows the receiving AI to request retransmission if confidence falls below threshold — turning one-way transmission into a feedback loop.
3. **Semantic Hash Verification (SHV):** Embeds a compact hash in each packet to detect and correct dictionary version mismatches between sender and receiver.

---

## Questions for Researchers

Feedback on the following is most welcome:

1. **Linguistics / Cognitive Science:** How does "semantic inertia" map to existing theories of language processing?
2. **Information Theory:** What is the relationship between this approach and Shannon's framework?
3. **Universality:** To what extent can a design based on morphological analysis generalize across typologically different languages?
4. **Evaluation:** What metrics would best measure "semantic fidelity" in reconstruction?
5. **Failure cases:** In what scenarios would this approach break down?

---

## Repository Structure

```
dvscp-concept-en/
├── README.md               ← This file
├── THEORY.md               ← Theoretical background (concept level)
├── LICENSE
├── examples/
│   └── sample_results.md   ← Reconstruction experiment examples
└── interface/
    └── dvscp_interface.py  ← Interface definition (no implementation)
```

---

## License

© 2026 Masato Amano. All Rights Reserved.  
This repository contains intellectual property subject to a pending patent application.  
Reference for research purposes is permitted. Implementation or redistribution requires explicit written permission.
