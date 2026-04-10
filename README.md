# LSH Protocol

Single source of truth for the LSH wire protocol.

This repository contains the compact protocol specification shared by:

- `lsh-core`
- `lsh-esp`
- `node-red-contrib-lsh-logic`

It is responsible for keeping the protocol contract explicit, versioned, and reproducible across firmware and application repositories.

## Contents

- `shared/lsh_protocol.json`
  Compact protocol spec and metadata.
- `shared/lsh_protocol_golden_payloads.json`
  Golden examples used to validate generated artifacts and codec behavior.
- `shared/lsh_protocol.md`
  Human-readable reference generated from the shared spec.
- `tools/generate_lsh_protocol.py`
  Generator for shared protocol artifacts consumed by the LSH repositories.

## Scope

This repo is the source of truth for:

- wire command IDs
- compact JSON keys
- click type IDs
- protocol compatibility metadata
- golden payload examples
- generated protocol documentation

This repo is not responsible for:

- firmware logic
- MQTT bridging logic
- Node-RED business logic
- hardware configuration

## Design Goals

- compact on-wire representation
- deterministic generated artifacts
- explicit compatibility rules
- simple maintenance across separate git repositories
- no ambiguity about protocol ownership

## Trusted Environment

The LSH protocol assumes a trusted environment and a cooperative broker. It does not embed authentication, integrity, or confidentiality mechanisms in the payload format itself. That constraint is intentional and must stay documented in all consumer repositories.

## Workflow

1. edit the shared spec or golden payloads
2. run the generator for the targets you want to update
3. commit the updated generated artifacts
4. propagate changes to consumer repositories

## Generator Usage

Generate only the human-readable reference inside this repository:

```bash
python3 tools/generate_lsh_protocol.py
```

Check only:

```bash
python3 tools/generate_lsh_protocol.py --check
```

Generate outputs for consumer repositories explicitly:

```bash
python3 tools/generate_lsh_protocol.py \
  --target shared-doc \
  --target core \
  --target esp \
  --target node-red \
  --core-root /path/to/lsh-core \
  --esp-root /path/to/lsh-esp \
  --node-red-root /path/to/node-red-contrib-lsh-logic
```

This repository is intentionally standalone:

- it does not assume a monorepo layout
- consumer outputs are emitted only when their target roots are passed explicitly
- `shared-doc` is the default target when no `--target` is provided

## Versioning

Keep these concepts distinct:

- `wireProtocolMajor`
  Used only for wire compatibility decisions.
- `specRevision`
  Internal protocol contract revision.
- git tags
  Repository release milestones.

## Status

This repository is intended to become the canonical protocol authority for the LSH system.
