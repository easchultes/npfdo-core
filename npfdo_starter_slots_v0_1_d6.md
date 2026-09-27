<!--
AUTHOR: Erik Schultes (ORCID: 0000-0001-8888-635X)
SESSION: Making MAC FAIR/npfdo A
DATE: 2026-09-26
URL: https://claude.ai/chat/c4ca9550-1c9a-48ed-b41e-2b0c1e938980
CREATED_AT: P09/R09
DESCRIPTION: Starter slot list v0.1, draft 6, freezing candidate (supersedes the P08/R08 draft). Range column checked against each predicate's declared range: the two URLs are IRIs, dct:requires takes a resource only, dct:extent's literal is marked as a simplification of dcterms:SizeOrDuration. Seeds the type catalogue's statement registry.
-->

# np-FDO starter slot list, v0.1 (draft 6, freezing candidate)

**The registered unit is a slot, not a predicate.** Predicates are already registered by Dublin Core, PROV, DCAT, BIBO and the rest; what is not registered anywhere is how a predicate is used: with which subject, which object kind, at which cardinality, in which role. A slot record is therefore predicate, range kind, cardinality, which is what a SHACL property shape needs: the registry and the shape are one thing expressed twice. A slot is registered once; a type-specific section references it rather than restating it, and lists only what is new.

**Range kinds:** *ID* identifier with a required format (ORCID, ROR, DOI, Trusty URI); *IRI* a resource by IRI with no required format (a URL); *FDO* typed reference to another FDO's referent IRI; *CL* controlled list; *LT* typed literal; *LF* free-text literal; *SN* structured node. The kind follows the predicate's declared range: a predicate whose range is a resource never takes a literal here, and the one literal simplification is marked as such. Every slot below passes the field test (specification §5.1): its value is a property of the referent as minted. The rejected candidates are listed in §H.

## A. The core: six unconditional, one per materialisation

| | Slot | Subject | Predicate | Range | Card. | Note |
|---|---|---|---|---|---|---|
| C1 | Type | referent | `rdf:type` | CL (class IRI, specification §4) | 1..n | Dispatch, not inference |
| C2 | Label | referent | `rdfs:label` | LF with language tag | 1..n, at most one per language tag (`sh:uniqueLang`) | Propagates to the record's label |
| C3 | Attribution | assertion | `prov:wasAttributedTo` | ID (ORCID, ROR, agent IRI) | 1..n | Provenance graph |
| C4 | Marker | record | `npx:hasNanopubType` | value: `npfdo:FDORecord` | at least one value equal to `npfdo:FDORecord`; predicate unbounded | Pubinfo; the admission rule; a qualified value shape, not a property cardinality |
| C5 | Identity link | record | `npx:introduces` and/or `npx:describes` | FDO (the referent IRI) | 1..n, one object | Pubinfo; full IRI when introducing a w3id term |
| C6 | Conforms to | record | `dct:conformsTo` | ID (Trusty URI of the specification record) | 1 | Pubinfo; fixed value in the pubinfo template |
| M1 | Conditions of use | materialisation | `dct:license` | CL (licence IRI) | 1..n per node | Binds only where a node exists |

## B. The materialisation cluster (structured node, 0..n per referent)

| Slot | Predicate | Range | Card. | Note |
|---|---|---|---|---|
| Materialises | `fdof:materializes` | FDO (the referent) | 1 | Node is the subject; the FDO Framework direction |
| Access URL | `dcat:accessURL` | IRI (declared range: a resource) | 1 | A locator as at minting; per DCAT it may resolve to the object, to a page about it, or to a page gating it, which HTTP does not distinguish; the download URL and the access status say which |
| Download URL | `dcat:downloadURL` | IRI (declared range: a resource) | 0..1 | The URL of the bytes; SHOULD be present where a checksum is present, since a checksum is unverifiable through a page |
| Conditions of use (M1) | `dct:license` | CL | 1..n | |
| Media type | `dcat:mediaType` | ID (IANA entry, `http://www.iana.org/assignments/media-types/<type>/<subtype>`, the DCAT 3 example form) | 0..1 | `fdof:hasEncodingFormat` takes the same IRI; one predicate per slot, DCAT chosen |
| Checksum | `spdx:checksum` | SN (`spdx:Checksum`: `spdx:algorithm` an SPDX 2.2 individual such as `spdx:checksumAlgorithm_sha256`, `spdx:checksumValue`) | 0..1 | SHOULD be present for mutable external targets |
| Byte size | `dcat:byteSize` | LT `xsd:nonNegativeInteger` | 0..1 | |
| Issued | `dct:issued` | LT `xsd:date` | 0..1 | Of the manifestation |
| Access status | `dct:accessRights` | CL: the EU Publications Office access-right authority table, `http://publications.europa.eu/resource/authority/access-right/` (`PUBLIC`, `RESTRICTED`, `NON_PUBLIC`), as used by DCAT-AP | 0..1 | A1.2 profile, reference 1 (specification §5.4): whether a procedure is needed; candidate per-materialisation constraint in a later core |
| Access policy | `odrl:hasPolicy` | ID (a policy object, may itself be an FDO record) | 0..n | A1.2 profile, reference 2: permissions, prohibitions, duties. ODRL supplies the structure of a duty; a vocabulary for presenting a credential of a named kind and issuer is a profile matter, none adopted at v0.1 |
| Access procedure | `dcat:accessService` | ID (a `dcat:DataService` with `dcat:endpointURL` and `dcat:endpointDescription`; may itself be an FDO record or an operation object) | 0..n | A1.2 profile, reference 3: the DCAT-native binding, adequate where authorising and serving coincide; the general form, for several typed acts, is a reference to operation objects defined by the endpoint specification |

## C. Referent-level reusables (assertion, all optional)

| Slot | Predicate | Range | Card. | Note |
|---|---|---|---|---|
| Description | `dct:description` | LF | 0..1 | |
| Creator | `dct:creator` | ID (ORCID, ROR) | 0..n | Of the referent; distinct from the record's creator |
| Contributor | `dct:contributor` | ID | 0..n | |
| Publisher | `dct:publisher` | ID (ROR) | 0..1 | |
| Rights holder | `dct:rightsHolder` | ID | 0..1 | |
| Created | `dct:created` | LT `xsd:date` | 0..1 | Of the referent |
| Issued | `dct:issued` | LT `xsd:date` | 0..1 | Of the referent as a whole; per-manifestation issue dates go on the node |
| Version string | `dcat:version` | LF | 0..1 | DCAT 3's literal version indicator. `dct:hasVersion` is a relation to another resource, not a string, and is not used for this slot. A new version is a new referent linked by `dct:replaces` |
| Language | `dct:language` | CL (Library of Congress ISO 639 IRI) | 0..n | |
| Subject or keyword | `dct:subject` | ID or CL | 0..n | |
| Source | `dct:source` | ID | 0..n | A plain pointer to where the referent was derived or copied from; `prov:wasDerivedFrom` for a qualified one |
| Conditions of use, referent-level | `dct:license` or `odrl:hasPolicy` | CL or ID | 0..n | Only where a licence or policy on the referent itself is meaningful; a policy is an extension (specification §5.4) |
| Access rights, referent-level | `dct:accessRights` | CL | 0..1 | Extension; for referents whose access is restricted as a whole (a specimen collection, a restricted dataset) |
| Contact point | `dcat:contactPoint` | ID or SN (vcard) | 0..1 | Optional here; IRI preferred over an e-mail literal |
| Identifier of the referent elsewhere | `dct:identifier` | LT | 0..n | A DOI or handle for a referent whose identity was minted here |
| Conforms to, referent-level | `dct:conformsTo` | FDO (a specification referent) or any IRI | 0..n | Same predicate as C6, different subject: the referent conforms, not the record |

## D. Qualified references (pair: relation from the list, target IRI)

| Relation | Range | Card. | For |
|---|---|---|---|
| `dct:isPartOf` | FDO or any IRI | 0..n | Membership, collections, the vocabulary a term belongs to |
| `bibo:presentedAt` | FDO (an event referent) or any IRI | 0..n | Document to event |
| `prov:wasDerivedFrom` | FDO or any IRI | 0..n | Derivation of the referent |
| `dct:replaces` | FDO or any IRI | 0..n | The referent is a new version of, or corrects, another referent |

Targets SHOULD be referent IRIs of FDOs where they exist and MUST be permitted to be any IRI. Record supersession is not a qualified reference; it is `npx:supersedes` in pubinfo (§E).

## E. Record-level statements (pubinfo; carrier convention, not counted in the core)

| Slot | Predicate | Range | Card. | Note |
|---|---|---|---|---|
| Created | `dct:created` | LT `xsd:dateTime`, UTC | 1 | Of the record |
| Creator | `dct:creator` | ID | 1 | Of the record; ORCID or agent IRI |
| Licence | `dct:license` | CL | 1 | Of the record; discharges R1.1 for metadata |
| Label | `rdfs:label` | LF | 0..1 | Omit; the referent's label propagates through the identity link |
| Supersedes | `npx:supersedes` | ID (Trusty URI) | 0..1 | Same signing key only; otherwise `prov:wasDerivedFrom` in provenance |
| Template links | `nt:wasCreatedFromTemplate`, `…ProvenanceTemplate`, `…PubinfoTemplate` | ID (template IRIs) | 1, 1, 1..n | No exceptions; the root record points at generic templates |
| Example marker | `npx:hasNanopubType npx:ExampleNanopub` | value | 0..1 | Excludes the record from listings; coexists with C4 on the same predicate |
| Created at | `npx:wasCreatedAt` | ID | 0..1 | Documented network convention: only when the record was actually made at that tool instance, never by default |
| Signature | `npx:signedBy`, `npx:hasSignature`, … | supplied by tooling | 1 | Never hand-written |

## F. Provenance-level statements (optional)

| Slot | Predicate | Range | Card. | Note |
|---|---|---|---|---|
| Derived from | `prov:wasDerivedFrom` | ID | 0..n | Source the description was extracted from |
| Generated by | `prov:wasGeneratedBy` | SN (`prov:Activity`) | 0..1 | Tool or activity that produced the description; FAIA attaches here |

## G. Type-specific sections

A type-specific section references registered slots and lists only what is new. Predicates marked **select** have candidates and are chosen with the catalogue; none is minted at v0.1. The honest count is stated for each type, since it is part of the minting ground.

### G.1 `npfdo:SlideDeck`

Uses, by reference: presentation event (§D `bibo:presentedAt`); creator, description, version string (§C); materialisation cluster (§B).

New slots:

| Slot | Predicate | Range | Card. | Status |
|---|---|---|---|---|
| Presentation date | `dct:date` | LT `xsd:date` | 0..1 | decided; the network's presentation template uses the same predicate |
| Duration | `dct:extent` | LT `xsd:duration`, a documented simplification of the declared range `dcterms:SizeOrDuration` | 0..1 | decided; of the delivered talk as planned |
| Presenter | `bibo:performer` **select**, or `dct:creator` where presenter and author coincide | ID (ORCID) | 0..n | select |
| Slide count | `bibo:numPages` **select** (slides counted as pages) | LT `xsd:nonNegativeInteger` | 0..1 | select |
| Recording | `dct:relation` **select** | FDO or any IRI | 0..n | select; a recording is a distinct referent |

Two new slots decided, three to select. The minting ground for `SlideDeck` is the near-fit (specification §4.2); the slots follow it and do not carry it.

### G.2 `npfdo:Skill`

Uses, by reference: version string (§C `dcat:version`); source repository (§C `dct:source`); install target and licence (§B: the `SKILL.md` and its files are the materialisation, the install target is its access URL, Apache 2.0 for code, CC BY 4.0 for documents); conforms to, referent-level (§C: the skill implements a specification).

New slots:

| Slot | Predicate | Range | Card. | Status |
|---|---|---|---|---|
| Runtime or client | `dct:requires` | ID or IRI (declared range: a resource; no literal) | 0..n | decided; the agent client or runtime the skill installs into, by IRI |

One new slot decided; the remainder are references. The minting ground for `Skill` is that no external term fits (specification §4.2).

## H. Rejected by the field test (computed by query, never stored)

Citation count · download count · view count · current verification status · number of terms in a vocabulary · current maturity level · "latest version" pointers · conformance results. Each is a query over the corpus or a dated observation minted as its own np-FDO record.

## I. Anticipated extension slots, placed but not decided

| Slot | Subject | Placement | Predicate | Status |
|---|---|---|---|---|
| Content identifier (ISCC) | materialisation node | beside the checksum in §B; a property of the file as minted | from the ISCC or Liccium vocabulary, to verify | anticipated; extension, not core |
| Verified agent identity (eIDAS 2.0) | not a slot | an attested object (specification §5.2) or a carrier signature scheme; a verification status is time-varying and never a field | none | anticipated; never core |

## J. Deferred to the catalogue

Funder (predicate to select) · Language of the record · Keywords with a controlled scheme · FAIA source type, activity code and system for all three subjects · the catalogue's own base IRI · SKOS mappings for every reused type · the second list, statements as registry entries with frequency and provenance.
