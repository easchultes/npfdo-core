<!--
AUTHOR: Erik Schultes (ORCID: 0000-0001-8888-635X)
SESSION: Making MAC FAIR/npfdo A
DATE: 2026-09-26
URL: https://claude.ai/chat/c4ca9550-1c9a-48ed-b41e-2b0c1e938980
CREATED_AT: P14/R14
DESCRIPTION: np-FDO core specification v0.1, draft 8, frozen (supersedes the P10/R10 draft). One non-model edit: §3.5 scopes the record's contribution to independence from the hosting platform rather than sole provision, naming FAIR Signposting's header and Link Set forms. Nothing in the record model, the term set or the minting order changed.
-->

# np-FDO core specification

**Version 0.1, draft 8, frozen. 26 September 2026.** This file is the referent of the specification record (§8); its SHA-256 at the freezing commit is carried by that record. Key words MUST, SHOULD and MAY are used as in RFC 2119. Rationale is kept to one sentence per decision (§10); the full argument is in the accompanying rationale document.

## 0. Reading guide: the separation rule

Three things are distinguished throughout, and every statement in this document belongs to exactly one of them.

| Layer | Governed by | Vocabulary |
|---|---|---|
| The **referent**: the thing described | Its own community | Any published vocabulary; the type policy of §4 |
| The **record**: the metadata record of the referent, and what makes it an FDO record | This specification | `npfdo:` for the carrier-binding term and the types this specification attaches structure to; reused terms for everything else |
| The **carrier**: the nanopublication that holds the record, its identity, signature, versioning and indexing | The nanopublication specifications and network conventions | `np:`, `npx:`, `nt:`, `npa:` |

**The separation rule.** Carrier operations use the carrier's own vocabulary and mechanisms; record semantics use this specification; referent semantics use the referent's vocabulary. A term is never minted in one layer to do the work of another. This is why the record reuses `npx:describes`, `npx:introduces`, `npx:supersedes` and `npx:hasNanopubType` rather than shadowing them (§10, D7).

## 1. Purpose and scope

An np-FDO record is a nanopublication that carries the minimal metadata record of one FAIR Digital Object: an identifier resolving to a record that types the referent, labels it, attributes the description, states the conditions of use of any materialisation, declares which version of this specification it conforms to, and is marked as such. The record is what an agent needs in order to act on the referent without asking anyone. It answers four questions, two of them directly and two by reference:

| Question | Answered by | Where |
|---|---|---|
| What is this? | type, label, description, qualified references | the record (C1, C2) |
| What can I do with it? | the materialisation; the operations a type resolves to, once the catalogue attaches them | the record (§3.5); by reference to the type catalogue (§4.3). No type carries an operation set at v0.1; operation sets are the endpoint specification's, not this one's |
| What am I allowed to do with it? | the licence; a policy where conditions are richer | the record (M1); by reference to a policy object (§5.4) |
| What must I do to gain access? | the access URL for open materialisations; for restricted ones, a status, a policy and a procedure | the record (§3.5); by reference to a policy and a service (§5.4) |

The fourth question is a subset of the second: the acts that gain access are those operations whose performance discharges the conditions of the third. The record carries the first and third itself; it carries the second and fourth as references to typed objects that have their own identity, authority and version.

This document specifies the **record model** (§2), which is carrier-neutral, and its **nanopublication binding** (§3). It does not define FDOs, does not propose an ontology, and does not add expressive capability that RDF, PROV-O, DCAT and SHACL lack. Every mechanism here can be reproduced on plain nanopublications by convention; the claim of the layer is **mandatory presence and uniform placement**, so that a consumer admitted to an np-FDO endpoint never falls back on inference. Other carriers (RO-Crate, Signposting, Handle records) bind the same record model through their own idioms; the model in §2 is written to be lifted out of this document unchanged.

## 2. The record model

**Definition.** A record conforms to the core when it satisfies the six unconditional constraints C1 to C6 and, for every materialisation it carries, the constraint M1. There is no other requirement. The count is **six unconditional constraints plus one that binds per materialisation**.

| | Constraint | Subject | Card. | Discharges | Note |
|---|---|---|---|---|---|
| C1 | **Type** | referent | 1..n | F2, I2 | What the referent is, as a controlled term from a published vocabulary (§4). Typing is for dispatch, not inference |
| C2 | **Label** | referent | 1..n, at most one per language tag | F2, R1 | Human-readable name; English and Dutch labels on one referent are ordinary practice |
| C3 | **Attribution** | the description | 1..n | R1.2 | Who made this description; distinct from who made the referent |
| C4 | **Marker** | the record | value constraint: at least one type value equal to `npfdo:FDORecord` | F4 | The record declares itself an np-FDO record. This is the admission rule (§7). The typing predicate itself is unbounded, since a record may carry other types (§3.3) |
| C5 | **Identity link** | the record | 1..n, one referent | F3, F1 | The record names the identifier of the thing it describes, and describes exactly one thing |
| C6 | **Conforms to** | the record | 1 | R1.3, at the record layer | The version of this specification the record was built against, by immutable identifier. The record answers to a community standard for records; a domain standard for the referent is a referent-level `dct:conformsTo`, optional |
| M1 | **Conditions of use** | each materialisation | 1..n per materialisation | R1.1 | A licence at minimum. Binds only where a materialisation exists |

**Materialisation** is optional and repeatable (0..n): concepts, communities, people, specimens and conferences have none. A licence asserted of a non-digital referent would be a statement about the referent, and it would be false; R1.1 concerns (meta)data, and the metadata licence is supplied by the carrier for every record (below). Conditions of use MAY additionally be asserted of the referent itself, by reference to a policy, where that is meaningful (§5.4).

**One referent per record.** A record that describes two things is two records. This is what makes the identity link (C5) a filter aid, a validator entry point and an F3 discharge at once.

**Portability.** The constraints are stated as roles, not as predicates. A carrier binding is a table like §3.1: the seven roles, each mapped to that carrier's placement and predicate. §3 is the nanopublication binding; a binding for RO-Crate, Signposting or a Handle record is another such table over the same seven roles, and a record lifted from one carrier to another keeps its roles and changes only its predicates.

### 2.1 What the carrier supplies

The following FAIR sub-principles are discharged for the record by the nanopublication carrier and are therefore not constraints of this specification: F1 for the record (the Trusty URI: unique, persistent, content-bound); F4 for the record (network-wide registry indexing); A1 and A1.1 (resolution over HTTP through the registry network); A2 (immutable, replicated records outlive the referent's bytes); I1 (RDF); R1.1 for the record (`dct:license` on the nanopublication, required by network convention); R1.2 for the record (signature, creator and timestamp).

One sub-principle is discharged by neither the carrier nor the core: **A1.2**, that the protocol allows for an authentication and authorisation procedure where necessary. The carrier resolves the record openly, and a licence (M1) governs what may be done with data already obtained, not what must be done to gain access. This is a declared boundary of the core, stated again in §2.2 and §5.4.

### 2.2 What the record model adds to a plain nanopublication

A plain nanopublication may carry each of the following, and its guidelines recommend several of them; none is required, and none has a fixed place. The layer's contribution is exactly the difference between *may* and *must*, and between *found by inference* and *found by position*.

| Sub-principle, for the referent | Plain nanopublication | np-FDO record |
|---|---|---|
| F2, I2: the referent is typed from a published vocabulary | optional | C1, in the assertion |
| F2, R1: the referent is labelled | optional | C2, in the assertion |
| F3, F1: the record names what it is about, and the referent has a resolvable identifier | optional (`npx:introduces`) | C5, in the publication information |
| F4: the record is indexed as an FDO record, so an endpoint can admit by type | absent | C4, in the publication information |
| R1.1: the data carry a licence | absent (only the record's licence is required) | M1, on each materialisation |
| A1.2: what must be done to gain access, where access is restricted | absent | **not in the core**: a profile matter, three references on the materialisation (status, policy, procedure; §5.4, D19) |
| R1.2: the description is attributed to an agent | provenance graph required, content unconstrained | C3, in the provenance graph |
| R1.3: the record names the specification it answers to | absent | C6, in the publication information |

The six-plus-one are, in FAIR terms, the delta between a plain nanopublication and an FDO record. The layer adds no reasoning; it removes the need for reasoning at admission. A consumer that needs the type, the identifier, the licence or the attribution reads a known predicate in a known graph rather than inferring from whatever the assertion happens to contain.

The A1.2 row is the one declared gap. A licence answers "what may I do with this" and not "what must I do to gain access", so the core cannot express *restricted, and here is the procedure* in machine-actionable form. At v0.1 every object is open and the cost is nil; for restricted holdings the remedy is the A1.2 profile of §5.4: three references on each restricted materialisation, a status, a policy and a procedure. The status alone already changes what an agent does: it reads "restricted" in the record and follows the procedure, instead of dereferencing an access URL and inspecting whether a landing page or a file came back. A status value is, like a licence IRI, actionable without a policy engine, and it is the natural candidate for a per-materialisation constraint in a later version of the core.

The two layers are on different axes. A plain nanopublication makes **itself** findable, accessible and citable. An np-FDO record makes **the thing it describes** findable, accessible, interoperable and reusable, at a known position.

## 3. Nanopublication binding

### 3.1 Placement by graph

Statements are placed by their **subject**: the referent in the assertion, the description in the provenance, the nanopublication in the publication information.

| Graph | Role | Statement | Card. |
|---|---|---|---|
| assertion | C1 Type | `sub:object rdf:type <type IRI>` | 1..n |
| assertion | C2 Label | `sub:object rdfs:label "…"@lang` | 1..n, one per language tag |
| assertion | Description | `sub:object dct:description "…"` | 0..1 |
| assertion | Creator of the referent | `sub:object dct:creator <ORCID or ROR>` | 0..n |
| assertion | Materialisation | `sub:file fdof:materializes sub:object` plus the cluster in §3.5 | 0..n |
| assertion | M1 Conditions of use | `sub:file dct:license <licence IRI>` | 1..n per materialisation |
| assertion | Conditions of use, referent-level | `sub:object dct:license <IRI>` or `sub:object odrl:hasPolicy <IRI>` | 0..n |
| assertion | Access, restricted materialisation (profile, §5.4) | `sub:file dct:accessRights <status> ; odrl:hasPolicy <policy> ; dcat:accessService <service>` | 0..1, 0..n, 0..n |
| assertion | Qualified reference | one of the pairs in §3.6 | 0..n |
| provenance | C3 Attribution | `sub:assertion prov:wasAttributedTo <agent IRI>` | 1..n |
| provenance | Derivation of the description | `sub:assertion prov:wasDerivedFrom <source>` | 0..n |
| pubinfo | C4 Marker | `this: npx:hasNanopubType npfdo:FDORecord` | value constraint, §3.3 |
| pubinfo | C5 Identity link | `this: npx:introduces sub:object` and/or `this: npx:describes sub:object` | 1..n, same object |
| pubinfo | C6 Conforms to | `this: dct:conformsTo <Trusty URI of the specification record>` | 1 |
| pubinfo | Carrier convention | `dct:created`, `dct:creator`, `dct:license`, three `nt:wasCreatedFrom…Template` links, signature; `npx:supersedes` where applicable | as the network requires |

Agent identifiers are ORCID or ROR IRIs, or an agent IRI introduced on the network (a declared bot). FAIA statements (source type, activity code, system) MAY be added, one per graph, each with the subject proper to that graph (referent, description, record); their placement is not yet confirmed with the FAIA authors and they are not part of the core at v0.1.

### 3.2 The identity link

`npx:introduces` is used when the record mints the referent's identity: the referent IRI is then a sub-IRI of the record (`<Trusty URI>/object`), and it is **kept unchanged** in every later version of the record, which re-introduces the same full IRI. `npx:describes` is used when the referent has prior identity (a DOI, an ORCID, a ROR, an external class IRI). Both MAY be present; all identity links in one record MUST point to the same IRI. The referent IRI of an introduced referent resolves, through the network, to its record: this is the identifier-to-record resolution that constitutes the FDO.

Prior identity is decided at minting. A referent that has an identifier elsewhere before its record is minted (a DOI, for instance) is described, not introduced, and the record's identity link is that identifier. A referent that acquires such an identifier after minting records it in a superseding record with `dct:identifier`; the introduced IRI remains its identity.

These two predicates are the ones the network's type and label rules key on: the referent's `rdf:type` and `rdfs:label` propagate to the nanopublication, so a record about a conference is indexed under `bibo:Conference` as well as under `npfdo:FDORecord`, and needs no `rdfs:label` of its own. A minted twin of either predicate would forfeit this (D7, D8).

### 3.3 The marker

The record MUST carry `this: npx:hasNanopubType npfdo:FDORecord`. This is a constraint on the **value**, not on the predicate: `npx:hasNanopubType` is unbounded, because a record may carry further types, and the example record of §9 carries `npx:ExampleNanopub` beside the marker. In SHACL this is a qualified value shape with a minimum count of one, not a maximum count on the property. No second statement with the same meaning is carried: the network reads `rdf:type` and `npx:hasNanopubType` on the nanopublication as one clause, so `this: a npfdo:FDORecord` would be pure duplication and a divergence surface for the shape (D9). The term is ours; the mechanism is the network's.

**Non-interference.** `npx:hasNanopubType` is the network's own typing mechanism, its value may be any IRI, and several values on one nanopublication are the documented norm. The registries' response to a new value is to create one type-specific repository for it on first use, which is the intended way to obtain a fast per-type store; the full and meta repositories and the repositories of other types are unaffected, and no existing query changes behaviour. The one hazard is on our side: the store is keyed on the exact IRI, so a variant (scheme, trailing character) would open a second store. The marker IRI is therefore fixed before first use and never varied.

### 3.4 Version binding

`dct:conformsTo` names the **Trusty URI of the specification record** for the version the record was built against, never a mutable location and never a "latest" pointer. The specification's stable identity is `https://w3id.org/npfdo/spec/core`; each version is one record. The specification record itself carries `this: dct:conformsTo this:`, which is legitimate because the record contains the specification it conforms to (§5.3).

### 3.5 The materialisation cluster

One node per manifestation, with the node as subject. `fdof:materializes` links the node to the referent; the direction follows the FDO Framework and the templates already in production.

| Statement | Card. |
|---|---|
| `sub:file fdof:materializes sub:object` | 1 |
| `sub:file dcat:accessURL <URL>` | 1 |
| `sub:file dcat:downloadURL <URL>` | 0..1 |
| `sub:file dct:license <IRI>` (M1) | 1..n |
| `sub:file dcat:mediaType <IANA media-type IRI>` | 0..1 |
| `sub:file spdx:checksum [ a spdx:Checksum ; spdx:algorithm spdx:checksumAlgorithm_sha256 ; spdx:checksumValue "…" ]` | 0..1 |
| `sub:file dcat:byteSize "…"^^xsd:nonNegativeInteger` | 0..1 |
| `sub:file dct:issued "…"^^xsd:date` | 0..1 |

DCAT distinguishes the two URLs, and the record keeps the distinction. `dcat:accessURL` gives access to the materialisation and may resolve to the object itself, to a page *about* it, or to a page that *gates* it; over HTTP these three are indistinguishable, since each returns a document with status 200. `dcat:downloadURL` is the URL of the bytes. The second case is not a defect of the identifier: a DOI resolves to a landing page by design, so that the page can carry metadata, licence and access conditions for a human reader. Where the hosting platform publishes FAIR Signposting, the path from the page to the bytes is declared in HTTP, as an `item` link in the page's `Link` header or in a Link Set the header points to; where it does not, which remains the common case, the page must be interpreted to find one. The record declares the path either way, through the download URL and the access status of §5.4, so the declaration travels with the metadata rather than depending on the platform, survives the object being referenced from elsewhere, and does not change when the platform does. Signposting, one of the peer carriers, is therefore complementary: it serves the agent that arrives at the page, the record serves the agent that arrives at the record. **Where a checksum is present, the download URL SHOULD be present**, since a checksum is a claim about bytes and is unverifiable if the only URL in the record resolves to a page.

The media-type IRI is the IANA registry entry in the form DCAT 3 itself uses in its examples, `http://www.iana.org/assignments/media-types/<type>/<subtype>`; the checksum node and the algorithm individual `spdx:checksumAlgorithm_sha256` are the SPDX 2.2 terms that DCAT 3 adopted for `spdx:checksum`. An access URL passes the field test (§5.1) only as a locator **as at minting**. Where a target moves, the answer is a new materialisation or a new record, never an edited field. A materialisation of a mutable external file SHOULD carry the checksum, so that drift is detectable rather than silent.

### 3.6 Qualified references

A pair: a relation from the controlled list, and a target IRI. The target SHOULD be the **referent IRI** of another FDO where one exists (never its record's Trusty URI; the record is found by query) and MUST be permitted to be any IRI.

| Relation | For |
|---|---|
| `dct:isPartOf` | membership, collections, the vocabulary a term belongs to |
| `bibo:presentedAt` | a document and the event it was presented at |
| `prov:wasDerivedFrom` | derivation of the referent from another referent |
| `dct:replaces` | the referent is a new version of, or corrects, another referent |

`dct:replaces` is referent-level. Superseding a **record** is a carrier operation: `npx:supersedes` in the publication information, same signing key, from which the registries derive `npx:invalidates`. Where the key differs, the carrier rule applies: `prov:wasDerivedFrom` in the provenance graph and no supersession claim.

### 3.7 Worked sketch

The deck record, for a deck deposited in Zenodo. Trusty-URI placeholders are marked; prefixes are in Appendix A. The deck has prior identity, its version DOI, so the referent is the DOI, the identity link is `npx:describes` (§3.2), and the access URL resolves to the Zenodo page while the download URL returns the bytes, which is the page-and-file case of §3.5 with a checksum that can be verified.

```trig
sub:assertion {
  <https://doi.org/10.5281/zenodo.XXXXXXX> a npfdo:SlideDeck ;
      rdfs:label "FAIR-Ready AI: Inverting AI"@en ;
      dct:creator orcid:0000-0001-8888-635X ;
      bibo:presentedAt <RA…conference-record…/conference> .
  sub:file fdof:materializes <https://doi.org/10.5281/zenodo.XXXXXXX> ;
      dcat:accessURL <https://doi.org/10.5281/zenodo.XXXXXXX> ;
      dcat:downloadURL <https://zenodo.org/records/XXXXXXX/files/deck.pptx> ;
      dcat:mediaType <http://www.iana.org/assignments/media-types/application/vnd.openxmlformats-officedocument.presentationml.presentation> ;
      spdx:checksum [ a spdx:Checksum ; spdx:algorithm spdx:checksumAlgorithm_sha256 ; spdx:checksumValue "…" ] ;
      dct:license <https://creativecommons.org/licenses/by/4.0/> ;
      dct:issued "2026-09-29"^^xsd:date .
}
sub:provenance {
  sub:assertion prov:wasAttributedTo orcid:0000-0001-8888-635X .
}
sub:pubinfo {
  this: npx:hasNanopubType npfdo:FDORecord ;
      npx:describes <https://doi.org/10.5281/zenodo.XXXXXXX> ;
      dct:conformsTo <RA…specification-record…> ;
      dct:created "2026-09-29T09:00:00Z"^^xsd:dateTime ;
      dct:creator orcid:0000-0001-8888-635X ;
      dct:license <https://creativecommons.org/licenses/by/4.0/> ;
      nt:wasCreatedFromTemplate <RA…/template> ;
      nt:wasCreatedFromProvenanceTemplate <RA…> ;
      nt:wasCreatedFromPubinfoTemplate <RA…/template> .
}
```

Two things are declared rather than argued. The version DOI is used, not the concept DOI: the record describes this version, and the concept DOI, which relates versions, belongs in a qualified reference if it is wanted at all. And a Zenodo DOI strictly identifies the deposit, while the record treats it as identifying the deck; this is conventional for scholarly artefacts and is adopted as a convention here. The trade accepted by describing rather than introducing is the one §3.2 states: the DOI resolves to Zenodo, not to the record, and the record is found by query.

Without a deposit, the referent is introduced instead: the assertion subject is `sub:deck`, the identity link is `npx:introduces sub:deck`, and the referent IRI `<Trusty URI>/deck` resolves through the network to the record. The conference record takes that form: `sub:conference a bibo:Conference`, introduced, with no materialisation and no qualified reference. The two records are linked by the deck's `bibo:presentedAt`, whose target is the conference's referent IRI.

## 4. Typing policy

### 4.1 The minting rule

Predicates are never duplicated: a minted twin of a network predicate is invisible to the registry functions that depend on the original, so reuse is mechanical, not a preference. Classes are governed by cost, and the rule is:

> **Reuse** when an external term fits and the type is purely referential, and record a SKOS mapping to it. **Mint** when (a) no external term fits, (b) the nearest external term is a near-fit whose definition would be wrong for the referent, so that adopting the IRI would adopt the wrong definition, or (c) `npfdo` will attach structure to the type: extension slots, a shape, a profile, or a scope note that constrains rather than annotates. A minted near-fit carries a `skos:closeMatch` to the term it declined.

Reuse-first is the policy, not an admission of having nothing of its own. `npfdo` maintains a growing base vocabulary with mappings to existing IRIs wherever one fits, and the rule above is its criterion for when an IRI of its own is warranted.

### 4.2 Inventory at v0.1

| Minted | Ground |
|---|---|
| `npfdo:FDORecord` | Carrier-binding: a claim about a nanopublication that no external term carries |
| `npfdo:SlideDeck` | Near-fit: `bibo:Slideshow` names the presentation of slides where the referent is the artefact, so adopting it would adopt the wrong definition; `skos:closeMatch bibo:Slideshow`. Extension slots follow |
| `npfdo:Skill` | No fitting external term |

Reused unchanged: `bibo:Conference`, `bibo:Specification`, `skos:ConceptScheme`, `owl:Class`, `npx:ExampleNanopub`, and every predicate. **No predicate is minted at v0.1.**

### 4.3 What typing does and does not do

A type tells a consumer which reference class an instance belongs to and, once the catalogue attaches them, which shape and which operations apply. At v0.1 types resolve to definitions only; no type carries an operation set, and operation sets are defined by the endpoint specification, not by this one. A type does not license subsumption reasoning, and nothing in the core depends on it doing so. Ontologies MAY be layered over the typed corpus later as mappings; the corpus is their evidence base, not their product. The gain over untyped records is the one stated in §2.2: dispatch by position rather than by inference.

### 4.4 External-IRI dependency, stated

A reused type resolves to its owner's document, not to anything `npfdo` controls: `bibo:Conference` dereferences to the BIBO ontology file, which has had no revision since 2009 and is stable but unmaintained. A consumer dereferencing the type therefore receives BIBO's definition, not the catalogue's scope note or extension slots, and **catalogue-entry lookup is a query** (`npx:describes <type IRI>`), not a resolution. The same holds for `fdof:materializes` (FDO Framework ontology; maintenance status to be confirmed) and for `bibo:presentedAt`. Acceptable at v0.1, and stated so that it is read rather than discovered.

## 5. Conformance

### 5.1 The field test (decision D12)

> **Time-varying quantities are not fields.** Any value that changes independently of the referent (citation counts, download statistics, current verification status, term counts) is computed by query rather than stored. Where a value at a moment is itself significant, it is minted as an np-FDO record of a dated observation, which references and thus accretes to the referent, carrying source, method and date; never as a field on the referent. **Corollary: a field is appropriate only for values that are properties of the referent as minted.**

Applied: `conformsTo` names a version fixed at minting and passes; a conformance *result* does not, and is a separate object (§5.2). A FAIA flag records how the thing was made, fixed at minting, and passes. An access URL passes only as a locator as at minting (§3.5).

### 5.2 Three tiers of evidence

| Tier | Evidence | Cost | Default for |
|---|---|---|---|
| **By design** | Made from the template; `conformsTo` names the version | One field | Every record |
| **Re-derived** | The shape is re-run against the record | Nothing stored | On demand |
| **Attested** | A conformance record states that a named validator checked the record, at a time, against a shape version | One object per validator per shape version | Where a third party needs independent assurance |

By design is the default because the template is generative. Attestation is a service, not a mechanism.

### 5.3 The root record's exception, visibly

Exactly one record in the corpus, the **specification record**, is validated rather than generated. It precedes the templates that carry its Trusty URI as a fixed value, so it is authored directly with every constraint present, its three template links point at existing generic templates, and it conforms to itself by re-derivation. A consumer filtering on the `npfdo` pubinfo template would miss it, which is one more reason the marker, not the template, is the admission rule. Anyone auditing the corpus will find one record whose provenance differs from every other; this section is why.

### 5.4 Extensions, profiles and permissions

Everything not named in §2 is an extension. The core shape constrains the core and permits additional statements; a **profile** adds constraints and may never relax the core. Type-specific slots are declared beside the type in the catalogue and listed in the starter slot list.

Permissions and access follow the same line. The core requires a **licence** on each materialisation (M1), because a licence IRI is a controlled value that any consumer can act on without a policy engine. Everything beyond it is an extension, permitted everywhere and required nowhere by the core.

**The A1.2 profile.** A restricted materialisation carries three references, each to an object with its own type, identity and version, and none of them inline:

| Reference | Statement on the materialisation node | Answers |
|---|---|---|
| Status | `dct:accessRights <value>`, from the EU Publications Office access-right authority table (`http://publications.europa.eu/resource/authority/access-right/`: `PUBLIC`, `RESTRICTED`, `NON_PUBLIC`), as DCAT-AP uses it | whether a procedure is needed at all |
| Policy | `odrl:hasPolicy <policy object>` | the conditions: permissions, prohibitions and duties. ODRL supplies the structure of a duty (an action under constraints); the vocabulary for presenting a credential of a named kind and issuer is a profile matter, and no such profile is adopted at v0.1 |
| Procedure | `dcat:accessService <service object>` with its `dcat:endpointURL` and `dcat:endpointDescription` | where and how the acts that discharge the conditions are performed. This is the DCAT-native binding: in DCAT the service is the one that *serves* the materialisation, so the binding is adequate where authorising and serving coincide. Where the acts are several and typed (an authorisation endpoint, then a data endpoint), the general form is a reference to operation objects, which the endpoint specification defines; the service MAY be such an object |

DCAT 3 already places `dct:accessRights`, `odrl:hasPolicy` and `dcat:accessService` on a distribution, so the profile mints nothing. The policy and the service are objects in their own right and MAY themselves be FDO records; a policy that changes is a new policy object and a superseding record, never an edited field. A credential presented at the service is an act performed there, and the outcome of that act is never written into the record (§5.1). The profile makes the conditions and the procedure machine-readable; it does not enforce them. For a by-reference object, enforcement belongs to the host of the bytes or to the service, and a record can advertise and orchestrate access but cannot grant it.

Access to the record itself is a different axis: metadata may be open while the materialisation is restricted, and a record whose metadata must also be restricted is served from a node in restricted mode, which is a carrier and operator matter outside this specification. Permissions are therefore in the core only as a licence, and in profiles as a status, a policy and a procedure.

**Anticipated extensions, placed but not decided.** Two capabilities are expected to be layered over records without entering the core, and the separation rule already fixes where each would live. A content-derived identifier of a file (an ISCC code) is a property of a materialisation as minted, so it is a slot on the materialisation node beside the checksum, with its predicate taken from the identifier's own vocabulary; it passes the field test. A verified identity for the agent behind an attribution (an eIDAS 2.0 attestation) is either a carrier matter, if it takes the form of a qualified signature on the nanopublication, or an attested object in the sense of §5.2, minted as its own dated record about the agent or the record; a verification status is time-varying and is never a field (§5.1). Neither is a decision of this version and neither appears in §10.

## 6. Shape policy

SHACL is open by default: properties a shape does not mention are ignored, so a shape declaring only the core validates the core and permits everything else. `sh:closed true` is never used on the core shape. Because SHACL has no native notion of named graphs, "in the right place" is three small shapes, one per graph, with the harness choosing which graph to check against which. Targeting falls out of the record: the object of the identity link is the target node of the assertion shape; the nanopublication IRI is the target of the publication-information shape; the assertion graph IRI is the target of the provenance shape. Materialisation nodes are reached by the inverse path of `fdof:materializes`. The marker is a qualified value shape (§3.3); the label constraint uses `sh:uniqueLang`. Shapes are a future development; before they are built, the nanopublication tooling's SHACL-shapes-as-templates should be checked, since if they mature they settle the carrier for shapes as well.

## 7. The admission filter

An endpoint that admits both plain nanopublications and np-FDO records empties the claim of §1. The filter is three conditions on the carrier and nothing on the content: **the marker is present, the record is not an example, and the record is not invalidated** (retracted or superseded, both derived by the registries into `npx:invalidates`). Pattern, for verification against the live repositories before deployment:

```sparql
PREFIX npx:   <http://purl.org/nanopub/x/>
PREFIX npa:   <http://purl.org/nanopub/admin/>
PREFIX npfdo: <https://w3id.org/npfdo/terms/>
SELECT DISTINCT ?np ?referent WHERE {
  GRAPH npa:graph {
    ?np npa:hasValidSignatureForPublicKey ?pubkey .
    FILTER NOT EXISTS { ?inv npx:invalidates ?np ; npa:hasValidSignatureForPublicKey ?pubkey . }
  }
  ?np npx:hasNanopubType npfdo:FDORecord .
  FILTER NOT EXISTS { ?np npx:hasNanopubType npx:ExampleNanopub . }
  ?np npx:introduces|npx:describes ?referent .
}
```

Run against the type-specific repository for `npfdo:FDORecord` once the registries have created it (listed at `query.knowledgepixels.com/types`); there the marker condition is satisfied by loading. To list records by referent type, query that type's own repository (the type propagates, §3.2) and keep the marker condition. Whether `npx:hasNanopubType` is mirrored into `npa:graph` or must be matched in the publication-information graph is item V3 in §11.

## 8. Identifiers and namespace

| What | IRI | Note |
|---|---|---|
| Vocabulary | `https://w3id.org/npfdo/terms/` | Registered at w3id.org; holds the carrier-binding term and the minted types of §4.2. This IRI is the value of `rdfs:isDefinedBy` on every term and is the vocabulary's only identity; an OWL or SKOS rendering, if one is ever produced, is served at this IRI by content negotiation, not at a second IRI |
| Terms | `https://w3id.org/npfdo/terms/<Term>` | |
| Specification, stable identity | `https://w3id.org/npfdo/spec/core` | Each version is one record; `conformsTo` uses the record's Trusty URI, never this IRI |
| Records | `https://w3id.org/np/RA…` | Trusty URIs, supplied by the carrier |
| Introduced referents | `https://w3id.org/np/RA…/<name>` | Kept unchanged across versions of the record |

No other path under the prefix is defined at v0.1. The carrier-named prefix holds only carrier-binding terms and the types this specification attaches structure to. Referent types are carrier-neutral: the catalogue's own base is decided with the catalogue, and nothing at v0.1 depends on it. Governance is not encoded in IRIs; it is carried by the w3id registration and, on the network, by the Space that maintains the term kinds. An IRI need not resolve to be minted, only to be honoured; the redirect follows the registration.

## 9. The corpus at v0.1 and the minting order

Nine objects. Each Trusty URI is written to the repository's minting log as it is produced; the repository, not any conversation, is the state.

| # | Object | Type of referent | In the layer | Tier |
|---|---|---|---|---|
| 1 | Specification record | `bibo:Specification`; materialisation: this file at the freezing commit, with checksum | yes | re-derived (§5.3) |
| 2 | Assertion template | nanopublication of type `nt:AssertionTemplate` | no (carrier object) | n/a |
| 3 | Publication-information template | `nt:PubinfoTemplate`; carries the marker, the identity link and `dct:conformsTo <#1>` as a fixed value | no (carrier object) | n/a |
| 4 | Example record | `bibo:Conference`, fictitious; also typed `npx:ExampleNanopub` | yes, excluded from listings | by design |
| 5 | Term record `FDORecord` | `owl:Class` | yes | by design |
| 6 | Term record `SlideDeck` | `owl:Class` | yes | by design |
| 7 | Term record `Skill` | `owl:Class` | yes | by design |
| 8 | Conference record | `bibo:Conference` | yes | by design |
| 9 | Deck record | `npfdo:SlideDeck`, `bibo:presentedAt` #8; `npx:describes` the version DOI of the deposit; minted last, after the deposit exists, so that the DOI, the download URL and the checksum are real | yes | by design |

The order is the dependency order. #1 first, because #3 hardcodes its Trusty URI; #1 uses the marker IRI before #5 defines it, which RDF permits and which is what makes the exception of §5.3 the only one. #2 and #3 next; the provenance template is an existing standard one. #4 is the smoke test of the templates and the first object the admission filter of §7 must **exclude**. #5 to #7 put the vocabulary inside the layer it defines, which is a stronger demonstration than #8 or #9. #6 precedes #9 because #9 uses it, and #9 is last because its referent's identity is registry-assigned and its checksum is taken from the deposited bytes. Templates are carrier objects at v0.1; a `Template` type may bring them into the layer later through records that `npx:describes` them.

**Preconditions for minting:** the base of §8 fixed and its registration filed; this file frozen and its SHA-256 recorded; the three term definitions frozen.

## 10. Design decisions

Each decision and its one-sentence ground. Where each was taken is recorded in Appendix B.

| | Decision | Ground |
|---|---|---|
| D1 | One generic template; the type is a controlled value | Type-specific structure is an extension, not a template |
| D2 | The type statement describes the referent; FDO-ness is a property of the record | Different subjects, different graphs |
| D3 | The record lives in the assertion | Expressible over carriers with no graph split; typing is an attributable claim |
| D4 | Materialisation optional, 0..n | Non-digital referents exist |
| D5 | Carrier-named prefix holds carrier-binding terms and structured types only | A carrier-named referent type invites each carrier to mint its own |
| D6 | Public network; minting irreversible; supersede, never edit | Network property; a mutable field on an immutable record is a contradiction |
| D7 | The separation rule (§0) | Shadowing a carrier predicate forfeits the carrier's functions built on it |
| D8 | Reuse-first for predicates (mechanical); the three-ground minting rule for classes (cost) | A duplicate predicate breaks registry functions; a duplicate class only costs drift |
| D9 | Marker by `npx:hasNanopubType` only, as a value constraint | The network reads `rdf:type` on the nanopublication as the same clause; the predicate must stay open for further types |
| D10 | Identity link by `npx:introduces` and/or `npx:describes`, one referent | These are the predicates the type and label rules key on |
| D11 | Conditions of use bind per materialisation; referent-level optional | A licence on a conference is a false statement about the conference |
| D12 | Time-varying quantities are not fields | Consequence of immutability |
| D13 | Version binding by Trusty URI; the specification record conforms to itself; the root is validated, not generated | Something must be first; self-conformance is legitimate because the record contains the specification |
| D14 | The specification document cites no minted IRIs; reverse linkage is a query | Forward pointers are impossible on an immutable substrate |
| D15 | By design is the default tier; attestation is a service | Certifying everything costs one object per validator per shape version |
| D16 | Open core shape; profiles may only add | SHACL is open by default; closing it would forbid extensions |
| D17 | Term records are np-FDO records | A name may be used before its definition record exists; the vocabulary then lives in the layer it defines |
| D18 | FAIA: one optional statement per graph until placement is confirmed | Minting is irreversible; placement not yet confirmed with the authors |
| D19 | Permissions and access enter the core as a licence only; the A1.2 profile is three references on a restricted materialisation (status, policy, procedure), requirable by profiles; A1.2 is a declared boundary of the core | A licence IRI is actionable without a policy engine; what must be done to gain access is a profile matter at v0.1 |
| D20 | Deontic and agentic content enter a record by reference to typed objects (a licence document, a policy, a service), never inline | The record then contains statements of one kind only, facts about the referent, and points to statements of permission and of procedure rather than making them (these three kinds, of fact, of permission and of action, are what "register" means here); a referenced object carries its own type, authority and version, so conditions or procedure can be superseded without touching the description |

## 11. Items to verify before minting, not assume

| | Item | Decides |
|---|---|---|
| V1 | Whether `nt:hasTargetNanopubType npfdo:FDORecord` on the assertion template makes the form tooling stamp the marker. Evidence in hand: an existing conference-definition template on the network carries `nt:hasTargetNanopubType` with three values, so the mechanism exists and takes several types; the stamping into `npx:hasNanopubType` is confirmed on a test nanopublication | Whether the pubinfo template carries two statements or three |
| V2 | Whether a pubinfo template can bind the assertion's object placeholder. Evidence in hand: the same template declares an `nt:IntroducedResource` placeholder, so `npx:introduces` is produced from the assertion template; the pubinfo-side binding remains to confirm | How the form path produces C5 |
| V3 | Whether `npx:hasNanopubType` is mirrored into `npa:graph` in the query repositories | The exact form of the filter in §7 |
| V4 | The FDO Framework's IRI for its metadata-record class | Whether `FDORecord` carries a `skos:closeMatch` at v0.1 |
| V5 | Whether the w3id registration has merged | Resolution only; minting does not wait |
| V6 | Whether the signing library enforces template slots or only records the template reference | How much the tooling must do; the shape is the guarantee either way |
| V7 | Whether the template-conformance checker flags the SKOS extension triples on term records | Whether §5.4's extension reading holds against existing tooling |
| V8 | The FAIA term IRIs | Deferred until placement is confirmed |

Resolved since draft 1: the IANA media-type IRI form is the one DCAT 3 uses in its own examples; `spdx:checksumAlgorithm_sha256` and the `spdx:Checksum` node are SPDX 2.2 terms as adopted by DCAT 3.

## Appendix A. Prefixes

```
np:     <http://www.nanopub.org/nschema#>
npx:    <http://purl.org/nanopub/x/>
npa:    <http://purl.org/nanopub/admin/>
nt:     <https://w3id.org/np/o/ntemplate/>
npfdo:  <https://w3id.org/npfdo/terms/>
bibo:   <http://purl.org/ontology/bibo/>
fdof:   <https://w3id.org/fdof/ontology#>
dct:    <http://purl.org/dc/terms/>
dcat:   <http://www.w3.org/ns/dcat#>
prov:   <http://www.w3.org/ns/prov#>
spdx:   <http://spdx.org/rdf/terms#>
odrl:   <http://www.w3.org/ns/odrl/2/>
skos:   <http://www.w3.org/2004/02/skos/core#>
owl:    <http://www.w3.org/2002/07/owl#>
rdfs:   <http://www.w3.org/2000/01/rdf-schema#>
xsd:    <http://www.w3.org/2001/XMLSchema#>
orcid:  <https://orcid.org/>
```

## Appendix B. Decision provenance (non-normative; omitted from the published version)

| Decision | Where taken |
|---|---|
| D1 to D4, D6 | Opening brief, §2 |
| D5, D13, D14, D17 | npfdo A, P03/R03 |
| D7 | npfdo A, P04/R04 |
| D8 | npfdo A, P02/R02; Job Search 3, P176 and P179 |
| D9, D10, D11 | npfdo A, P02/R02; D9 restated as a value constraint at P05/R05 after Job Search 3, P179 |
| D12 | Proposal - FMI, 9 September 2026 |
| D15, D16, D18 | Opening brief, §3.8, §4.1, §4.2 |
| D19 | npfdo A, P05/R05; A1.2 boundary added at P06/R06 after Job Search 3, P180; restated as procedure with three references at P07/R07 |
| D20 | npfdo A, P07/R07, drawing on the epistemic, deontic and agentic register separation established in ISO 42001 (P27) and Job Search 3 (P23); register defined in the clause at P08/R08 after Job Search 3, P181 |
