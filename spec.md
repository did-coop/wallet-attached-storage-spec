## Introduction

### Overview

### Goals

### Anti-Goals

#### Use cases do not include "zero trust" environments

This storage specification is intentionally positioned to not be used in
"zero trust" environments, which in practice means the usage of untrusted sync
and replication nodes while solely relying on encryption as the authorization
mechanism.

To put it a different way -- all encryption has an unpredictable half life, and 
some use cases do not permit relying on encryption only for access control.
Instead a _combination_ of encryption and authorization enforcement by minimally
trusted storage servers is required.

### Core Concepts

## Terminology

<dl class="termlist definitions" data-sort="ascending">
</dl>

## Authorization

## Spaces

### Space Data Model

#### Space JSON Representation

#### Space CBOR Representation

### Space Operations

#### Space HTTP API

#### Space Javascript Client API

## Collections

### Collection Data Model

#### Collection JSON Representation (ActivityPub profile)

#### Collection CBOR Representation

### Collection Operations

#### Collection HTTP API

#### Collection Javascript Client API

## Resources and Blobs

### Blob Data Model

### Resource Data Model

### Resource Operations

#### Resource HTTP API

#### Resource Javascript Client API

## Security and Privacy Considerations

## Internationalization Considerations

### Language and Base Direction

<section class="appendix informative">

## IANA Considerations

This section will be submitted to the Internet Engineering Steering Group (IESG)
for review, approval, and registration with IANA.
</section>

