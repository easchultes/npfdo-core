<!--
AUTHOR: Erik Schultes (ORCID: 0000-0001-8888-635X)
SESSION: Making MAC FAIR/npfdo A
DATE: 2026-09-26
URL: https://claude.ai/chat/c4ca9550-1c9a-48ed-b41e-2b0c1e938980
CREATED_AT: P05/R05
DESCRIPTION: Bootstrap term set v0.1, draft 2 (supersedes the P04/R04 draft): the three terms minted under w3id.org/npfdo/terms/ (FDORecord, SlideDeck, Skill), each as a term record in the Chat C format with its RDF form, plus the reused-term register and the term-record minting recipe. SlideDeck's minting ground now leads with the near-fit; version predicate corrected.
-->

# np-FDO bootstrap term set, v0.1 (draft 2)

**Three terms. No predicate.** Base `https://w3id.org/npfdo/terms/`. Naming convention: singular noun, CamelCase, British English labels, alternatives as `skos:altLabel`. Each term is minted as its own np-FDO record (specification §9, objects 5 to 7), with the class IRI as referent, typed `owl:Class`. Mappings are permitted, never required; a mapping whose target IRI is unverified is omitted at v0.1 rather than minted on trust.

## 1. Term records

### 1.1 FDORecord

| Field | Value |
|---|---|
| IRI | `https://w3id.org/npfdo/terms/FDORecord` |
| Preferred label | FDO record |
| Definition | A nanopublication that is the metadata record of one FAIR Digital Object under the np-FDO core specification: it types, labels and attributes exactly one referent, states the conditions of use of any materialisation, declares the specification version it conforms to, and is marked as such in its publication information. |
| Scope note | A property of the record, not of the referent. Excludes nanopublications that mention or describe an FDO without conforming to the core; excludes the templates that generate records, which are carrier objects. Used as the object of `npx:hasNanopubType` on the nanopublication, never as the type of the referent. |
| Example | The specification record; the conference record for the Bonn 2026 meeting. |
| Alternative labels | np-FDO record |
| See also | `np:Nanopublication` (every FDO record is one); `npx:ExampleNanopub` (a record may carry both, and is then excluded from listings) |
| Mapping | `skos:closeMatch` to the FDO Framework's metadata-record class, once its IRI is verified (specification §11, V4). Omitted at v0.1 |
| Group | Infrastructure and self-description |
| Ground for minting | Carrier-binding. A claim about a nanopublication that no external term carries. |

```turtle
npfdo:FDORecord a owl:Class ;
    rdfs:label "FDO record"@en ;
    skos:definition "A nanopublication that is the metadata record of one FAIR Digital Object under the np-FDO core specification: it types, labels and attributes exactly one referent, states the conditions of use of any materialisation, declares the specification version it conforms to, and is marked as such in its publication information."@en ;
    skos:scopeNote "A property of the record, not of the referent. Excludes nanopublications that mention or describe an FDO without conforming to the core, and excludes the templates that generate records. Used as the object of npx:hasNanopubType on the nanopublication."@en ;
    skos:example "The np-FDO core specification record."@en ;
    skos:altLabel "np-FDO record"@en ;
    rdfs:seeAlso np:Nanopublication, npx:ExampleNanopub ;
    rdfs:isDefinedBy <https://w3id.org/npfdo/terms/> .
```

### 1.2 SlideDeck

| Field | Value |
|---|---|
| IRI | `https://w3id.org/npfdo/terms/SlideDeck` |
| Preferred label | Slide deck |
| Definition | A set of presentation slides authored as one document, typically for delivery at an event, and realised as one or more files. |
| Scope note | The document, not the act of presenting it and not the event: a talk is an event-like referent, a recording is a distinct object, and the conference is linked by `bibo:presentedAt`. The file is the deck's materialisation, not the deck. Distinguished from `bibo:Slideshow`, which is defined as a presentation of a series of slides; this term names the artefact. Type-specific slots (presentation date, duration; presenter, slide count and recording to select) are listed in the starter slot list. |
| Example | "FAIR-Ready AI: Inverting AI", presented at the Bonn 2026 meeting. |
| Alternative labels | slide set; presentation slides |
| See also | `bibo:Slide` (one slide); `bibo:Conference` |
| Mapping | `skos:closeMatch bibo:Slideshow` |
| Group | Scholarly outputs |
| Ground for minting | Near-fit (specification §4.1, ground b): `bibo:Slideshow` names the presentation of slides where the referent is the artefact, so adopting the IRI would adopt the wrong definition. Extension slots follow the minting; they do not carry it. |

```turtle
npfdo:SlideDeck a owl:Class ;
    rdfs:label "Slide deck"@en ;
    skos:definition "A set of presentation slides authored as one document, typically for delivery at an event, and realised as one or more files."@en ;
    skos:scopeNote "The document, not the act of presenting it and not the event. The file is the deck's materialisation, not the deck. Distinguished from bibo:Slideshow, which names a presentation of a series of slides; this term names the artefact and carries extension slots."@en ;
    skos:example "FAIR-Ready AI: Inverting AI, presented at the Bonn 2026 meeting."@en ;
    skos:altLabel "slide set"@en, "presentation slides"@en ;
    skos:closeMatch bibo:Slideshow ;
    rdfs:seeAlso bibo:Slide, bibo:Conference ;
    rdfs:isDefinedBy <https://w3id.org/npfdo/terms/> .
```

### 1.3 Skill

| Field | Value |
|---|---|
| IRI | `https://w3id.org/npfdo/terms/Skill` |
| Preferred label | Skill |
| Definition | An agent skill: a packaged, versioned set of instructions and supporting files that equips an AI agent or its runtime to perform a class of task, installed into a client rather than executed as a standalone program. |
| Scope note | Excludes human competences (Wikidata's "skill" denotes a competence, to be confirmed as `wd:Q205961`) and excludes software libraries and standalone programs. A skill is instruction-first material with optional scripts; its `SKILL.md` and supporting files are its materialisation. Type-specific slots (runtime or client; by reference: version string, install target, licence, specification implemented) are listed in the starter slot list. |
| Example | The nanopub skill (`knowledgepixels/nanopub-skill`); the npfdo minting skill. |
| Alternative labels | agent skill |
| See also | `bibo:Specification`: a skill implements a specification through referent-level `dct:conformsTo`, and the specification is a distinct referent |
| Mapping | None at v0.1. Checked: BIBO, SKOS, DCAT, DCTERMS; no fitting term. schema.org's HowTo is the nearest by sense and is not adopted (DCTERMS and RDFS preferred; schema.org avoided). |
| Group | AI |
| Ground for minting | No fitting external term. |

```turtle
npfdo:Skill a owl:Class ;
    rdfs:label "Skill"@en ;
    skos:definition "An agent skill: a packaged, versioned set of instructions and supporting files that equips an AI agent or its runtime to perform a class of task, installed into a client rather than executed as a standalone program."@en ;
    skos:scopeNote "Excludes human competences and excludes software libraries and standalone programs. A skill is instruction-first material with optional scripts; its SKILL.md and supporting files are its materialisation."@en ;
    skos:example "The nanopub skill published by Knowledge Pixels."@en ;
    skos:altLabel "agent skill"@en ;
    rdfs:isDefinedBy <https://w3id.org/npfdo/terms/> .
```

## 2. Reused terms, register

Recorded here so that the reuse decisions are auditable and so that Chat C starts from a record rather than a memory.

| Role | Reused IRI | Ground | Watch |
|---|---|---|---|
| Conference (type) | `bibo:Conference` | Purely referential at v0.1 | BIBO unrevised since 2009 |
| Specification (type) | `bibo:Specification` | Purely referential at v0.1 | as above |
| Vocabulary (type) | `skos:ConceptScheme` | W3C Recommendation | none |
| Vocabulary term (type) | `owl:Class` | Standard; KP's class listings key on it | none |
| Example marker | `npx:ExampleNanopub` | Network convention | none |
| Identity link | `npx:introduces`, `npx:describes` | Type and label rules key on them | none |
| Record supersession | `npx:supersedes` | Registries derive `npx:invalidates` from it | same-key rule |
| Marker mechanism | `npx:hasNanopubType` | Creates the type-specific store | none |
| Conforms to | `dct:conformsTo` | Exact sense | none |
| Presented at | `bibo:presentedAt` | Exact sense, document to event | BIBO, as above |
| Is part of | `dct:isPartOf` | Exact sense; KP house preference for DCTERMS | none |
| Derived from | `prov:wasDerivedFrom` | Exact sense | none |
| Replaces (referent-level) | `dct:replaces` | Exact sense | none |
| Materialises | `fdof:materializes` | FDO Framework; MAC production | FDOF maintenance status to confirm |
| Access URL, media type, byte size | `dcat:accessURL`, `dcat:mediaType`, `dcat:byteSize` | W3C Recommendation | none |
| Checksum | `spdx:checksum` | DCAT 3 practice | node form |
| Licence, issued, creator, description | `dct:license`, `dct:issued`, `dct:creator`, `dct:description` | DCTERMS | none |
| Attribution | `prov:wasAttributedTo` | Network convention | none |

## 3. Term-record minting recipe (Chat B)

Each term is one np-FDO record with the class IRI as referent. Order: `FDORecord`, `SlideDeck`, `Skill`, after the templates and the example (specification §9).

- **Assertion:** the Turtle block of the term, as given, with the full class IRI as subject. `a owl:Class` satisfies C1; `rdfs:label` satisfies C2; the SKOS statements are extensions permitted by the open shape (specification §5.4; checker caveat V7). No materialisation, so no M1.
- **Provenance:** `sub:assertion prov:wasAttributedTo orcid:0000-0001-8888-635X .`
- **Publication information:** `this: npx:hasNanopubType npfdo:FDORecord ; npx:introduces <full class IRI> ; dct:conformsTo <Trusty URI of the specification record> ; dct:created …^^xsd:dateTime ; dct:creator orcid:… ; dct:license <https://creativecommons.org/licenses/by/4.0/> ;` and the three template links (assertion template, standard provenance template, npfdo pubinfo template). `npx:introduces` uses the **full w3id IRI**, not `sub:…`, so the term's identity is independent of the record's Trusty URI and is kept unchanged when the record is superseded.
- **Type propagation:** each term record is indexed under `npfdo:FDORecord` and `owl:Class`, so it appears in KP's class listings and in the npfdo filter alike.
- **Path:** the skill path (write TriG, `check`, `sign`, verify `npx:signedBy`, stop; publish only on explicit instruction). The NanoDash path gains `owl:Class` in its type dropdown in Chat C.
- **Marker cardinality:** `npx:hasNanopubType` is unbounded on the record; the constraint is that one of its values is `npfdo:FDORecord` (specification §3.3). A term record carries the marker only; the example record carries the marker and `npx:ExampleNanopub`.
- **Definitions are frozen** in this file. A regretted wording is superseded by a new term record introducing the same IRI, never edited.
