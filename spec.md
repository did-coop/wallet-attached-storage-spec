## Introduction

### Overview

### Goals and Requirements

This storage specification is intended to support the following goals and 
requirements.

#### Local-first and Offline capable storage

Users and apps need to be able to use (provision, set up, and start reading and 
writing to) storage spaces without being connected to the internet.

#### Storage and sharing of public, permissioned, and private encrypted data

Although the local-first offline functionality is necessary, writing data to 
stable internet-accessible URIs for the purposes of sharing them is one of the 
primary use cases of this specification.

* A user needs to be able to write data (that is intended to be world-readable) 
  to a cloud-accessible URI, and be able to send that URI to intended recipients
  via any out of band mechanism such as email, chat, and so on.

* User needs to be able to change or revoke permissions at any point after sharing

* The sharing and permission system needs to be primarily based on authorization
  capabilities (zcaps), but it needs to also support storage-side access control 
  list or similar functionality (even if only as a way for an authorized client 
  to receive an appropriate zcap)

* The sharing mechanism needs to be flexible and granular. For example, a given
  data resource needs to be: world-readable, or readable by groups or categories, 
  or by only those possessing the required authorization capabilities, or by 
  noone except the author or controller, etc

* Advanced sharing conditions are also desireable (such as "this share expires
  after X amount of time" or "this is a one-time share, and will expire after
  the first successful read request")

#### Stored data is opaque to the storage provider

* Stored data needs to be opaque to the storage provider. That is, unless a user 
explicitly authorizes it (via the sharing and permission mechanisms), storage 
providers need to be unable to read the data stored on their servers in an 
un-encrypted format. 

* For plausible deniability, the spec needs to be able to support situations
  where all data (even marked as public-readable) is encrypted at rest

#### Replication to user-controlled local and cloud servers

* Replication reconciles the first two requirements (data reads and writes must
  be offline-capable, but the data must eventually be able to be shared on the
  web via traditional URIs)

* Replication also provides critical availability and disaster recovery 
  functionality

* Replication needs to be multi-primary (to reflect the multi-device and multi-
  client user environment)

* Multi-primary replication requires support for a versioning or conflict
  resolution mechanism

* Data, metadata, and permissions all need to be replicated

* Authorship and data provenance (the ability to tell which user or service
  created or edited a given set of data) must work in this permissioned multi-
  primary write environment

#### Serve as a General Purpose application storage backend

Intended to serve as storage backend to client-side (Single Page Applications),
server side, desktop, and mobile apps and services.

#### Data Portability

Data written to storage spaces using this specification needs to be portable:

* Authorized agents need to be able to export or backup all of the data written,
  including all corresponding metadata and permissions

* The sharing and storage system needs to be able to support web domain 
  independent identifiers. That is, a user must be able to share data at a given
  URI, then be able to migrate to a different storage service provider 
  (potentially operating on a different web domain than the previous one), and
  the shared permissions to that data must not break after service migration

* While portability (and the not breaking of URIs) is relatively easy to achieve
  via redirect mechanisms (such as HTTP 301 and 302 redirect codes), this 
  requires the previous service provider to be alive, available and cooperative.
  However, this is true only of public-readable URIs, and the moment permissions
  are involved, cross-domain redirects become very difficult.
  In addition, portability from "dead servers" is also required. That is, if a 
  cloud based service provider disappears (or is otherwise unavailable), but a 
  user still has a backup/export available, they should be able to set up another
  storage server (on another web domain or network address), and import/restore
  the data from backup, without shares and permissions breaking (agents that
  the data was previously shared with must still be able to find the data at
  the new storage server location, and their permissions must still work) 

#### A Plurality of Data Formats and Protocols

* Spec needs to support the storage of any kind of data -- binary files and
  objects, structured documents such as JSON or CBOR, contents of relational
  database tables, graphs, and anything else, all using the same unified metadata,
  sharing and permission mechanisms.

* Storage-side schema enforcement is available but not required.

* Spec needs to be able to support multiple protocols and APIs, such as HTTP,
  JSON-RPC, DIDComm, local client APIs, and more.

### Anti-Goals

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

