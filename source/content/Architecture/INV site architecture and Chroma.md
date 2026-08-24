---
title: The architecture of the Idea Nexus site and its Chroma index
description: Why INV is a static site with a native Marvin guide, a public ElevenLabs agent, and a server-side Chroma Cloud retrieval boundary.
tags:
  - architecture
  - Idea Nexus Ventures
  - Chroma
  - Marvin
  - building in public
---

# The architecture of the Idea Nexus site and its Chroma index

The Idea Nexus Ventures site is deliberately smaller than the story it tells.

That is not a limitation we are trying to hide. It is the first architectural decision.

The site presents three related kinds of work — HumAIn products, organizational AI transformation, and network-informed market intelligence — while the machinery underneath is a restrained static publishing system with a guide agent and a retrieval index.

The point of the architecture is not to make the site look more intelligent than it is. The point is to make the useful intelligence inspectable.

## The shape of the system

At the time of writing, the system has five relevant layers:

```text
Markdown page sources
        |
        v
Deterministic site build
        |
        +--> clean static public/ artifact --> Cloudflare Pages
        |
        +--> Marvin public knowledge corpus
                         |
                         v
                 Chroma Cloud collection
```

Marvin has a second path for conversation:

```text
Visitor
  |
  +--> native text guide --> bounded public fallback
  |
  +--> native voice control --> ElevenLabs Convai agent
```

The two paths are intentionally not collapsed into one opaque chatbot. A visitor should be able to read the site and ask a bounded question without being forced into a remote model session. Voice is opt-in. Retrieval credentials never enter the browser.

## 1. The source is Markdown, not generated HTML

The canonical content lives in `site/pages/*.md` in the public Idea Nexus repository. Each page has explicit frontmatter for its title, route, kind, pillar, accent, and summary.

A small Python builder parses those files and writes a clean `public/` directory. It deletes the old output before rebuilding, which is an unglamorous but important choice: stale files should not survive merely because a previous version once had them.

The generated artifact contains:

- page HTML;
- the Quiet Signal stylesheet;
- Marvin’s browser code;
- the public Marvin knowledge manifest;
- no credentials;
- no Chroma database;
- no raw private notes.

This lets the site remain cheap to host, easy to inspect, and hard to accidentally couple to a hidden application server.

## 2. Marvin is a product surface, not a vendor badge

The site has a Marvin guide on every page. The first version was a bounded local guide: it matches public questions against a curated knowledge manifest and links the answer back to a source page.

That fallback matters. If the remote agent is unavailable, the page does not become an empty rectangle labelled “AI.” It still has a useful, inspectable guide.

The current text answer for “How do you measure human capability?” is deliberately specific. It routes to the Humanpower page and names six dimensions:

- range of action;
- decision quality;
- coordination latency;
- context retention;
- trust and agency;
- learning rate.

There is no single humanpower score. A single score would be a convenient way to hide the argument.

## 3. ElevenLabs supplies voice, not the interface

The INV guide agent is public and runs on ElevenLabs Convai. The site does not mount the hosted ElevenLabs widget.

The native “Talk with Marvin” control loads the ElevenLabs client only after a visitor clicks it. The session then connects directly to the public agent through the client SDK and writes agent messages into the existing Marvin transcript.

That distinction is architectural, not cosmetic. A hosted widget would introduce a second visual product inside the Idea Nexus product. It would own the chrome, the state language, the controls, and eventually the visitor’s understanding of what Marvin is.

The native surface keeps those decisions in the site:

- text and voice share one transcript;
- voice is explicitly opt-in;
- microphone permission is requested after a user gesture;
- “End voice session” is visible;
- text remains available if voice fails;
- the agent is identified as an AI rather than presented as Leo.

The model provider is replaceable. The boundary is ours.

## 4. Chroma is the retrieval substrate

We were not using Chroma in the first Marvin implementation. That version used keyword matching against `marvin.json`.

We now sync every public Markdown page into the Chroma Cloud collection `inv_public_site_pages`.

The sync script:

1. walks the current page source directory;
2. parses frontmatter and body content;
3. chunks long pages under the provider’s document-size boundary;
4. assigns stable IDs from route and chunk index;
5. computes local ONNX embeddings;
6. upserts documents and metadata into Chroma Cloud;
7. deletes records whose source pages no longer exist;
8. runs a semantic verification query.

Each indexed chunk carries metadata such as:

- route;
- title;
- page kind;
- page slug;
- chunk index;
- content hash;
- corpus identifier.

For the test query `how do you measure human capability?`, Chroma returns `/humanpower/` as the top result. That is the kind of receipt we want: not “the retrieval feels better,” but a query, a collection, a result, and a distance.

## 5. New pages have an ingestion path

The page source and the retrieval index are separate artifacts, so they need a synchronization contract.

The repository includes a GitHub Actions workflow that runs when `site/pages/**` changes. It installs the Chroma client and runs the idempotent sync script using encrypted repository secrets:

- `INV_CHROMA_API_KEY`;
- `INV_CHROMA_TENANT`;
- `INV_CHROMA_DATABASE`.

The credentials are not in Markdown, generated HTML, the Chroma receipt, or browser JavaScript. They exist at the local operator boundary and the CI secret boundary only.

This creates a simple rule for future work:

> A new public page is not fully shipped until its static artifact and its retrieval record agree about what exists.

## What is not connected yet

The public browser does not query Chroma directly. That would require exposing a Chroma credential or placing a privileged proxy in front of it, neither of which is acceptable as a shortcut.

The current state is therefore staged:

- public page content is live;
- Marvin has a bounded browser fallback;
- Chroma Cloud contains the page corpus;
- CI keeps the corpus synchronized;
- a server-side retrieval endpoint is the next boundary to build.

That endpoint should return source-linked context, not a raw vector result. It should also enforce rate limits, preserve the distinction between public and private material, and fail back to the bounded guide when retrieval is unavailable.

A system that cannot explain its fallback is not robust. It is merely optimistic.

## Why this is enough architecture for now

The site does not need a large application stack to prove the Idea Nexus thesis. It needs:

- a source a person can inspect;
- a build another person can run;
- a guide that does not invent certainty;
- a retrieval layer that preserves provenance;
- an ingestion flow that notices new pages;
- a receipt for what actually happened;
- a clear place where the next risk lives.

That is the architecture.

Not the number of services. The number of boundaries that remain visible.

## Receipts

- [Idea Nexus Ventures](https://ideanexusventures.com/)
- [Humanpower](https://ideanexusventures.com/humanpower/)
- [Idea Nexus source repository](https://github.com/leo-guinan/idea-nexus-site)
- [Chroma sync workflow](https://github.com/leo-guinan/idea-nexus-site/actions/workflows/chroma-sync.yml)

The public site is the artifact. The architecture note is the admission that the artifact has machinery behind it, and that the machinery should be available for inspection.
