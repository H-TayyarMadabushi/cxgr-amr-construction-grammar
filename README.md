# CxGr-AMR: Extending Abstract Meaning Representation Beyond Lexically Anchored Relations with Constructional Rolesets

In this repository, we have provided information on the dataset created for the CxGr-AMR project along with the promtps used in the semi-automatic pipeline. 

CxGr-AMR addresses cases where important semantic roles come from clause-level constructional structure rather than from the lexical verb alone. Standard AMR largely anchors argument structure in lexical rolesets, which can lead to incorrect or incomplete analyses for argument structure constructions such as resultatives, caused motion, intransitive motion, and ditransitives.

Please check out our [full paper](https://pure.unamur.be/ws/portalfiles/portal/119481894/bonial2026cxgr.pdf) for more details!

## Paper 

**CxGr-AMR: Extending Abstract Meaning Representation Beyond Lexically Anchored Relations with Constructional Rolesets**  
Claire Bonial, Claire Benet Post, Paul Van Eecke, Katrien Beuls, and Harish Tayyar Madabushi  
Proceedings of the Seventh International Workshop on Designing Meaning Representations (DMR 2026) @ LREC 2026

## Abstract

Current Abstract Meaning Representation (AMR) annotation guidelines, which largely tie argument structure to lexical rolesets, systematically misrepresent cases in which key semantic roles stem from clause-level structure rather than the verb, leaving these meanings either unnaturally attached, incorrect, or unexpressed. To address this limitation, we present CxGr-AMR, a novel extension of AMR that captures the semantics of various types of phrasal constructions, including argument structure constructions. We first examine how such cases are handled under current Standard-AMR guidelines and show that these analyses are often inadequate when constructionally contributed roles clash with those assigned by the verb. We then provide a theoretical grounding for our CxGr-AMR rolesets that lay out the relationship between the syntactic signatures of constructional slots and particular semantic roles associated with them. Finally, we develop an annotation-expert-in-the-loop pipeline for the semi-automatic annotation of sentences, and release a dataset containing 355 instances of phrasal constructions annotated with both Standard and CxGr-AMR.

## Overview

CxGr-AMR introduces constructional rolesets that make clause-level meaning explicit while remaining compatible with Standard AMR. The paper focuses on four English argument structure constructions:

- `resultative-91`
- `caused-motion-91`
- `intransitive-motion-91`
- `ditransitive-91`

These rolesets allow constructionally contributed semantics, such as result entailment, caused motion, path/goal structure, and caused transfer, to be represented directly rather than being forced into lexical verb rolesets. Thus, we go above the level of lexical items typically represented in AMR in a way that remains compatible with existing AMR graphs and tools. 

### Example 1: resultative-91

For example, consider the resultative sentence:

> The man shrieked himself unconscious.

in Standard AMR, it is represented as:

```amr
(s / shriek-01 
    :ARG0 (m / man
    :ARG1-of s)
        :mod (u / unconscious))
```

This analysis follows the lexical roleset for `shriek-01`, which has roles such as:

```text
ARG0: shrieker
ARG1: shriek itself, or utterance
ARG2: unfortunate listener
```

Under this parse, the man is treated both as the **shrieker** and as the **thing shrieked**. The resulting state, `unconscious`, is attached only as a modifier of the shrieking event. This is problematic because it does not capture the actual meaning of the sentence. Thus, it is parsed as:

**The man shrieked, the thing-shrieked was the man, and the shrieking was unconscious.**

The intended meaning is not simply that the man shrieked and that the shrieking was unconscious. Rather, the sentence means that the man’s shrieking caused him to become unconscious.

CxGr-AMR represents this constructional meaning with the `resultative-91` roleset:

```amr
(r / resultative-91
   :ARG0 (m / man)
   :ARG1 m
   :ARG2 (u / unconscious)
   :ARG3 (s / shriek-01
        :ARG0 m))
```

Here, the `resultative-91` construction makes the clause-level semantics explicit:

```text
ARG0-Cause: The man
ARG1-Patient: The man
ARG2-Result: unconscious
ARG3-Concomitant_Event: shriek-01
```

Parsed as: **The man’s shrieking caused him to become unconscious.**

This representation captures the resultative meaning directly: the man is the cause and patient of the construction, `unconscious` is the resulting state, and `shriek-01` is preserved as the concomitant event that brings about the result.

Although many sentences can be accurately parsed according to verbal semantics alone, a comprehensive meaning representation should have the machinery to represent all meaning-bearing elements of a language. Furthermore, argument structure constructions constitute some of the most frequent constructions of English, and they are cross-linguistically common as well (Goldberg, 1995; Perek and Lemmens, 2010), yet any instantiation with a relatively infrequent, creative verb that does not share the semantics of the construction will not be represented accurately under current guidelines, as seen in this example. 

### Example 2: caused-motion-91

Standard AMR is highly effective for many sentence types, but it often assumes that core semantic roles are projected by lexical predicates. This creates problems for sentences where a verb appears in a construction whose meaning is not reducible to the verb’s ordinary roleset.

For example, in caused motion sentences such as:

> They laughed the actor off the stage.

the verb `laugh-01` does not itself license a motion path or goal. CxGr-AMR captures the constructional meaning by introducing a `caused-motion-91` node while preserving the lexical verb as a concomitant event.

```amr
(z0 / laugh-01
    :ARG0 (z1 / they)
    :ARG2 (z2 / person
        :ARG0-of (z3 / act-01))
    :location (z4 / off
        :op1 (z5 / stage)))
```

```text
ARG0-PAG: laugher
ARG1-PPT: cognate object
ARG2-CAU: source of joy
ARG3-PRD: end state of Arg0, as result of laughing
```


## Roleset Descriptions

Here we go through each of the four English Argument Structure Constructions (ASCs) addressed in the paper and outline their new CxGr-AMR roleset.

### Resultative-91

The Resultative construction involves an Agent’s participation in one event that causes a change of state in a Patient. 

The subject (A) is the agent of some event expressed by the verb (B), wherein carrying out that event causes the object (C) to change to the resulting state expressed by the oblique (D).

```text
Arg0-Cause (SBJ slot (A))
Arg1-Patient (OBJ slot (C))
Arg2-Result (OBL slot (D))
Arg3-Concomitant_Event (V slot (B))
```

### Caused-Motion-91

The Caused Motion construction involves an agent doing something that causes a theme to move along a path or towards some goal. 

The subject (A) is the agent of an event expressed by the verb (B), wherein carrying out that event causes the object (C) to move along some path or to a destination/goal expressed as an oblique (D).

```text
Arg0-Cause (SBJ slot (A))
Arg1-Theme (OBJ slot (C))
Arg2-Goal (OBL slot (D))
Arg3-Concomitant_Event (V slot (B))
```

### Intransitive-Motion-91

The Intransitive motion construction involves a Theme in an event that causes or accompanies movement in space. 

The subject (A) is a theme carrying out an event (B) that causes or accompanies movement along a path expressed as an oblique (C).

```text
Arg1-Theme (SBJ slot (A))
Arg2-Path/Goal (OBL slot (C))
Arg3-Concomitant_Event (V slot (B))
```

For instance, in the sentence:

> The Weweantic River FLOWS through the pond.

The CxGr-AMR `intransitive-91` representation is:

```amr
(i / intransitive-motion-91
    :ARG1 (r / river
        :name (n / name
            :op1 "Weweantic"
            :op2 "River"))
    :ARG2 (p / pond)
    :ARG3 (f / flow-01
        :ARG1 r))
```

### Ditransitive-91

The Ditransitive construction involves an agent carrying out an event that is construed as causing a recipient to receive a theme. 

The subject (A) carries out an action denoted by the verb (B), which is construed as (intending to) cause a recipient, expressed as the object (C), to receive a theme, expressed as a second object (D).

```amr
Arg0-Cause (SBJ slot (A))
Arg1-Theme (OBL slot (D))
Arg2-Recipient (OBJ slot (C))
Arg3-Concomitant_Event (V slot (B))
```

For instance, in the sentence:

> Jack passed her the salt.

The CxGr-AMR `ditransitive-91` representation is:


```amr
(d / ditransitive-91
    :ARG0 (j / person
        :name (n / name
            :op1 "Jack"))
    :ARG1 (s / salt)
    :ARG2 (s2 / she)
    :ARG3 (p / pass-05
        :ARG0 j
        :ARG1 s
        :ARG2 s2))
```

## Dataset

The released dataset contains **345 instances** of phrasal constructions annotated with updated CxGr-AMR rolesets. The dataset includes the following construction types:

| Construction Type | Count |
| --- | ---: |
| Resultative | 185 |
| Intransitive Motion | 58 |
| Caused Motion | 52 |
| Ditransitive | 50 |
| **Total** | **345** |

The dataset was developed through a semi-automatic expert-in-the-loop annotation pipeline using:

1. Standard-AMR parsing with the [SPRING parser](http://nlp.uniroma1.it/spring/)
2. LLM-based graph editing to incorporate CxGr-AMR rolesets
3. Rule-based structural fixes
4. Manual post-editing and adjudication



## Citations

Adele E. Goldberg. 1995. Constructions: A construction grammar approach to argument structure. University of Chicago Press, Chicago, IL, USA.

Florent Perek and Maarten Lemmens. 2010. Getting at the meaning of the english at-construction: the case of a constructional split. CogniTextes. Revue de l’Association française de linguistique cognitive, 5(Volume 5).
