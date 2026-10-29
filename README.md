<!--
AUTHOR: Erik Schultes (ORCID: 0000-0001-8888-635X)
SESSION: Making MAC FAIR/Job search 4
DATE: 2026-10-29
URL: https://claude.ai/chat/979d3533-1fc4-4a5b-a95c-f3484fbe1d6c
CREATED_AT: P102/R102
DESCRIPTION: Repository readme for npfdo-core: what the specification is, the six-plus-one constraints, contents, published records, related skill, licence, citation. Facts taken from the repository as cloned on 29 Oct 2026 (spec v0.1 draft 8, MINTING_LOG.md, records/). Licence section assumes CC BY 4.0 for specification text and records; a LICENSE file must be added to match.
-->

# np-FDO core

The **np-FDO core specification** defines a minimal metadata record for a FAIR Digital Object, published as a [nanopublication](https://nanopub.net). A record identifies exactly one object (its *referent*) and states what it is, who described it, and under what conditions any file of it may be used. It also declares which version of this specification it conforms to. Records are signed and content-addressed (Trusty URIs), so they are tamper-evident and can be verified by any client without trusting the server that returns them.

The record is what a person or an AI agent needs in order to act on an object without asking anyone. It answers four questions: *What is this? What can I do with it? What am I allowed to do with it? What must I do to gain access?* It answers the first and third directly, and the second and fourth by reference.

**Status.** Version 0.1, frozen 26 September 2026.
**Stable identity:** `https://w3id.org/npfdo/spec/core` (each version is one specification record).
**Vocabulary:** `https://w3id.org/npfdo/terms/`

## The record model in one table

A record conforms when it satisfies six unconditional constraints. It must also satisfy one further constraint for each materialisation (file or other manifestation) it carries.

| | Constraint | Applies to | FAIR sub-principles discharged |
|---|---|---|---|
| C1 | Type | referent | F2, I2 |
| C2 | Label | referent | F2, R1 |
| C3 | Attribution | the description | R1.2 |
| C4 | Marker (`npfdo:FDORecord`) | the record | F4 |
| C5 | Identity link (exactly one referent) | the record | F1, F3 |
| C6 | Conforms to (specification version, by immutable identifier) | the record | R1.3 |
| M1 | Conditions of use (a licence at minimum) | each materialisation | R1.1 |

The nanopublication carrier already provides identity, signature, timestamp, indexing and resolution for the record itself. The specification adds only what a plain nanopublication does not guarantee: **mandatory presence and uniform placement** of the statements above. A consumer can therefore read a known predicate in a known graph instead of inferring from whatever the assertion happens to contain.

**What this specification does not do.**
- It does not define FAIR Digital Objects. The FDO concept belongs to the FDO Forum community, and this is one realisation of it.
- It proposes no new ontology.
- It adds no expressive capability beyond RDF, PROV-O, DCAT and SHACL.

The record model is carrier-neutral. Bindings for RO-Crate, Signposting or Handle records would map the same seven roles to their own idioms.

**One declared gap.** A licence says what may be done with data already obtained, not what must be done to obtain it. The core therefore does not express restricted access (FAIR A1.2) in machine-actionable form. Specification §5.4 describes the profile that does.

## Contents

| File | What it is |
|---|---|
| `npfdo_core_spec_v0_1_d8.md` | The specification, v0.1 (normative) |
| `npfdo_bootstrap_terms_v0_1_d2.md` | The three terms minted at v0.1 (`FDORecord`, `SlideDeck`, `Skill`) and the register of reused terms |
| `npfdo_starter_slots_v0_1_d6.md` | Starter slot list: how reused predicates are used in records (subject, range, cardinality) |
| `records/` | Source and signed TriG files of every record in the v0.1 corpus; `records/discarded/` holds one record withdrawn before publication |
| `MINTING_LOG.md` | Each published record: Trusty URI, date, signed file, SHA-256 |
| `FAIR-Ready_AI_v1.0.pdf`, `.pptx` | Slides of the talk "FAIR-Ready AI" (Bonn, September 2026), an example referent of type `SlideDeck` |

## The published corpus

The v0.1 corpus was published to the public nanopublication network on 27 September 2026. It comprises:
- the specification record;
- the assertion and publication-information templates;
- an example record;
- the three term records;
- a conference record.

The specification record contains the specification it conforms to, which is the one permitted self-reference (§5.3). The Trusty URIs are listed in `MINTING_LOG.md` and resolve through `https://w3id.org/np/`.

## Making records

Records can be made in two ways:
- **In a browser,** in [Nanodash](https://nanodash.knowledgepixels.com), using the published templates.
- **With an AI assistant,** using the `npfdo-mint` agent skill: <https://github.com/easchultes/npfdo-skills>. The assistant drafts, signs and verifies a record but never publishes it. Publication is a separate step that a person runs, and the skill never reads or writes signing keys.

## Licence

The specification text and the records in this repository are licensed under [Creative Commons Attribution 4.0 International (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/); see [`LICENSE`](LICENSE).

## Citation

Schultes, E. (2026). *np-FDO core specification*, version 0.1. https://w3id.org/npfdo/spec/core

## Contact

Erik Schultes · ORCID [0000-0001-8888-635X](https://orcid.org/0000-0001-8888-635X) · erik@partnersinfair.com
