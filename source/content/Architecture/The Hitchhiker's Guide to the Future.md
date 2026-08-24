---
title: "The Hitchhiker's Guide to the Future: a network of personal futures"
description: "The architecture of a publication network with private diaries, public guides, paid memberships, node passports, and explicit semantic-search permissions."
tags:
  - architecture
  - Hitchhiker's Guide to the Future
  - privacy
  - networks
---

# The Hitchhiker's Guide to the Future: a network of personal futures

The Hitchhiker’s Guide to the Future is a network of paid personal futures.

A writer publishes a public guide, keeps a private diary, and chooses the price and permissions under which another person may enter the unfinished thinking behind the public work.

The promise is not prediction. It is a closer relationship with the future a person is trying to make.

## The core object is a node

A node contains:

- a public guide;
- a private diary;
- publication and version history;
- a membership offer;
- an access policy;
- optional participation in the semantic Atlas;
- receipts for the important changes.

A traveler can enter one node without automatically entering every other node. The network is not a shared bucket of thoughts. It is a set of explicit access relationships.

## Membership can create a node passport

A host membership may grant a traveler a **node passport**: the capability to create and operate one node of their own.

That is the growth loop:

```text
read a valuable node
  → receive access under its terms
  → write in a node of your own
  → publish grounding updates
  → contribute a distinct perspective
  → become a waymark for another traveler
```

The passport is not a universal key. It does not grant access to other writers’ private diaries. Its meaning is capability to create, not permission to read everything.

## Privacy is policy, not a checkbox

Diary entries are private by default. Public publication is an explicit action. Atlas inclusion is separately controlled.

The network distinguishes at least three questions:

1. Is this entry public?
2. Is this node contributing public writing to Atlas?
3. Is private material eligible for search by a particular entitled reader?

A search result must carry its source node, access reason, corpus class, and policy version. If a writer revokes Atlas inclusion, new indexing stops and derived search data is removed according to policy. Historical receipts remain receipts; the platform does not rewrite the past to make the audit trail prettier.

Private writing is not global training data by default. That requires separate affirmative consent.

## The first architecture is a modular monolith

The launch design keeps identity, nodes, entries, publications, memberships, billing, search, imports, domains, and receipts in one deployable application with clear domain modules.

The reason is not nostalgia. The first risks are product and trust risks:

- will writers price access?
- will members return to the diary?
- will people create nodes of their own?
- will Atlas search feel useful rather than invasive?

A microservice split would create operational ceremony before these questions have earned it.

PostgreSQL is the proposed source of truth for transactional state, full-text search, vector embeddings, and policy joins. The search layer must filter by node participation, corpus class, entitlement, and policy version before it assembles a result.

A dedicated vector system can come later if corpus size or latency proves the need. Authorization cannot be delegated to a stale index.

## Access is capability-granted

The browser is not trusted to enforce a paywall. The API re-evaluates a server-side capability grant for every private read and search request.

A grant records enough context to answer “why can this traveler see this?” including:

- traveler;
- node;
- scope;
- corpus class;
- validity window;
- source of the grant;
- revocation state;
- policy version accepted at grant time.

That is more work than a `paid = true` flag. It is also more useful when something goes wrong.

## The Atlas is an explicit surface

Atlas is semantic search over participating nodes. It is not a side effect of membership and not a synonym for “the whole network.”

A node owner can choose whether to include public entries, whether to include private entries for entitled readers, and whether to expose any broader redacted or snippet-only representation.

Derived embeddings and summaries are not canonical writing. They carry model and version provenance. The canonical entry remains the writer’s versioned source.

## Grounding keeps private thinking connected to reality

The platform does not ask writers to make all thinking public. It asks them to create a clear bridge between ideas and consequences.

A grounding update records:

- what the writer believed or was trying to build;
- what changed in the world or artifact;
- what evidence was observed;
- what they now believe;
- what remains uncertain;
- what would falsify the view;
- what happens next.

That keeps the product from becoming a paid mythology engine. Private thought is not valuable merely because it is private. It becomes valuable when access supports better understanding, decisions, or work.

## Receipts are part of the domain model

Writes, publications, policy changes, membership grants, searches, revocations, imports, and deletion requests should produce append-only receipts.

Edits create new versions. Imports preserve source and transformation. Search results explain visibility. Payment-provider state is reconciled against the platform’s entitlement ledger rather than treated as the ledger itself.

The architecture therefore separates:

```text
canonical content
  + derived search data
  + commerce and entitlements
  + receipts
```

The database is less poetic than the cover page. That is its job.

## What would falsify the design

The architecture should change if:

- readers value public writing but will not pay for private access;
- paid readers do not return;
- members receive node passports but do not create sustained nodes;
- writers price access but do not maintain useful corpora;
- grounding updates become performance rather than evidence;
- Atlas increases surveillance anxiety without meaningful discovery;
- deliberate curation still produces one narrow worldview.

More mythology is not a response to those results. A published miss and a changed design would be.

## Source and related architectures

- [Hitchhiker’s Guide repository](https://github.com/leo-guinan/hitchhikers-guide-to-the-future)
- [Platform charter](https://github.com/leo-guinan/hitchhikers-guide-to-the-future/blob/main/docs/platform-charter.md)
- [Build in Public University thesis](https://github.com/leo-guinan/hitchhikers-guide-to-the-future/blob/main/docs/build-in-public-university.md)
- [[Architecture]]
- [[Architecture/Phoneless]]
- [[Architecture/HumAIn OmarchyPi]]
- [[Architecture/INV site architecture and Chroma]]

This is a launch architecture and working contract, not evidence that the full network is already operating at scale. The first release should optimize for trustworthy access and receipts before network effects.
