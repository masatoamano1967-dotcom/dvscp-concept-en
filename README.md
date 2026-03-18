# DVSCP -- English Version
## Dynamic Vector Semantic Compression Protocol

**Author:** Masato Amano (M AI-studio)  
**Status:** Patent Application Pending  
**Version:** v1.0 / 2026-03-18

> Japanese repository: [dvscp-concept-ja](https://github.com/masatoamano1967-dotcom/dvscp-concept-ja)

---

## Overview

DVSCP is a communication protocol that extracts only the 'semantic skeleton' from text, compresses it into binary data, and delegates reconstruction to an AI on the receiving side.

```
[Original Text]
    |
    v (Rule-based, no AI)
[Semantic Skeleton Packets]
    |
    v (Any LLM)
[Reconstructed Text]
```

**Core principle:** Transmit only what the receiving AI cannot infer from context.

---

## Key Ideas

### Separation of Concerns
- **Sender (Encoder):** Fully deterministic rule-based processing. No AI.
- **Receiver (Decoder):** Any large language model reconstructs the message.

This means reconstruction quality improves automatically as AI models advance -- with no changes to the encoder.

### Semantic Inertia

Once a context is established, subsequent words become predictable. DVSCP omits what the receiving AI can infer, transmitting only inertia breakpoints.

> 'At the moment a word is born, it already contains a direction toward the next word.'

### Packet Structure

| Mode | Size | Content |
|---|---|---|
| V5 | 3 bytes | X + Y coordinates (WordNet synset) + SemanticByte |
| V6X | 1 byte | SemanticByte only (out-of-vocabulary token) |
| SKIP | 0 bytes | Omitted -- reconstructed from inertia |

**SemanticByte** encodes TAG (role) and DIR (direction) in 5 bits:
- TAG: SUBJ / VERB / OBJ / MOD / CONJ
- DIR: Neutral / Inner / Outer / **Reversal** (negation -- always transmitted)

---

## Benchmark Results (Measured / 2026-03-18)

| # | Original text | Original | Compressed | Reduction |
|---|---|---|---|---|
| 1 | The scientist quietly made a groundbreaking discovery. | 54B | 15B | **72.2%** |
| 2 | She smiled, but her eyes were filled with tears. | 48B | 15B | **68.8%** |
| 3 | He never gave up, even when everything seemed hopeless. | 55B | 16B | **70.9%** |
| 4 | The old man slowly walked along the river, remembering his youth. | 65B | 21B | **67.7%** |
| 5 | Despite the danger, she stepped forward without hesitation. | 59B | 16B | **72.9%** |
| **Total** | | **281B** | **83B** | **70.5%** |

All results are measured from the working implementation using spaCy + WordNet.

---

## Multi-AI Reconstruction Test

The same restoration prompt was submitted to three independent AI systems.

| Test | ChatGPT | Gemini | Perplexity | Meaning | Emotion |
|---|---|---|---|---|---|
| 1 | A scientist quietly made a groundbreaking discovery. | The scientist quietly made a groundbreaking discovery. | The scientist quietly made a groundbreaking discovery. | OK | OK |
| 2 | She smiled, but her eyes filled with tears. | They smiled, but their eyes filled with tears. | She smiled, but her eyes filled with tears. | OK | OK |
| 3 | Never give up, even when things seem hopeless. | Never give up, even when it seems hopeless. | Never give up, even when everything seems hopeless. | OK | OK |
| 4 | The old man slowly walked by the river, remembering his youth. | The old man walked slowly by the river, remembering his youth. | The old man walked slowly to the river, remembering his youth. | OK | OK |
| 5 | Despite the danger, he stepped forward without hesitation. | Despite the danger, they stepped forward without hesitation. | Despite the danger, he stepped forward without hesitation. | OK | OK |

**Meaning preserved: 5/5 | Emotion preserved: 5/5 | NEG/contrast preserved: 5/5**

> Note: Subject gender (she/he/they) varies -- gender is not encoded by design.
> Prepositions (along/by/to) vary within meaning-equivalent range -- by design.

See [sample_results.md](./sample_results.md) for full detail.

---

## Guardrail Mechanisms

1. **Mandatory Anchor Protocol (MAP):** Forces transmission after 5 consecutive skips.
2. **Uncertainty Echo (UE):** Receiving AI requests retransmission if confidence is low.
3. **Semantic Hash Verification (SHV):** Detects dictionary version mismatches.

---

## Comparison with Japanese Version

| Metric | English v1.0 | Japanese v5.1 |
|---|---|---|
| Tokenizer | spaCy (en_core_web_sm) | MeCab |
| Coordinate system | WordNet synset ID | LaBSE + UMAP |
| Average compression | **70.5%** | **77-85%** |
| Meaning preservation | 5/5 (100%) | 100% |
| Encoding | Rule-based, no AI | Rule-based, no AI |

Both versions achieve >70% compression with identical theoretical foundations,
demonstrating that **semantic inertia is a language-independent principle**.

---

## Repository Structure

```
dvscp-concept-en/
  README.md            <- This file
  THEORY.md            <- Theoretical background
  sample_results.md    <- Full reconstruction experiment results
  dvscp_interface.py   <- Public interface definition (implementation not disclosed)
  LICENSE
```
## Roadmap — Voice DVSCP (Next Generation)

A next-generation version integrating audio dimensions is currently under development.

| Axis | Description |
|---|---|
| PITCH | Fundamental frequency contour |
| INTON | Intonation pattern |
| EMOT | Emotional valence |
| Inertia | Prosodic continuity (omitted when predictable) |

**Expected improvement:** Voice DVSCP demonstrated compression of 1 minute of speech
from ~1.9MB to 582B — a reduction exceeding 99%.

The text DVSCP versions (Japanese / English / Chinese) form the semantic layer
of this unified architecture. Voice DVSCP is the primary invention;
text DVSCP serves as its complement.

*Voice DVSCP — Patent Application Pending / M AI-studio*
---

## License

Copyright 2026 Masato Amano. All Rights Reserved.  
This repository contains intellectual property subject to a pending patent application.  
Reference for research purposes is permitted.  
Implementation or redistribution requires explicit written permission.
