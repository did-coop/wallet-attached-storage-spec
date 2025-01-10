## Introduction

### Overview

## Goals and Requirements

This storage specification is intended to support the following goals and
requirements.

### Local-first and Offline capable storage

Users and apps need to be able to use (provision, set up, and start reading and
writing to) storage spaces without being connected to the internet.

### Storage and sharing of public, permissioned, and private encrypted data

Although the local-first offline functionality is necessary, writing data to
stable internet-accessible URIs for the purposes of sharing them is one of the
primary use cases of this specification.

* A user needs to be able to write data (that is intended to be world-readable)
  to a cloud-accessible URI, and be able to send that URI to intended recipients
  via any out of band mechanism such as email, chat, and so on.

* User needs to be able to change or revoke permissions at any point after
  sharing. Note that changed permissions apply only to subsequent operations
  (this spec is not intended to solve the general problem of DRM).

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

### Stored data is opaque to the storage provider

* The spec needs to support (though not require) end-to-end client side 
  encryption of the space. For plausible deniability, this might need to include
  all all data (even marked as public-readable) is encrypted at rest

### Replication to user-controlled local and cloud servers

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

### Serve as a General Purpose application storage backend

Intended to serve as storage backend to client-side (Single Page Applications),
server side, desktop, and mobile apps and services.

### Data Portability

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

### A Plurality of Data Formats and Protocols

* Spec needs to support the storage of any kind of data -- binary files and
  objects, structured documents such as JSON or CBOR, contents of relational
  database tables, graphs, and anything else, all using the same unified metadata,
  sharing and permission mechanisms.

* Storage-side schema enforcement is available but not required.

* Spec needs to be able to support multiple protocols and APIs, such as HTTP,
  JSON-RPC, DIDComm, local client APIs, and more.

### Permissioned Query and Search functionality

* Where appropriate (such as for unstructured text, structured documents, RDBMs
etc), storage needs to be queryable or searchable

* Any query/search mechanism needs to work well with the sharing/permission and
  replication requirements

### Upgradeable and legislation-compliant cryptography

All cryptography has a half-life.

* Any cryptographic operations (such as hashing, signatures, and encryption)
  used in this specification must be able to be obsoleted or upgraded, as
  techniques and algorithms break. To put it another way, the spec cannot
  "hardcode" any given algorithm (although it can recommend current best 
  practices)

* Implementations of this spec need to be usable in government and enterprise
  environments, which means all of the spec requirements must be able to be
  fulfilled using legislation-approved (for example, FIPS-compliant) in most
  jurisdictions

### Anti-Goals

#### Use cases do not include "zero trust" environments

This storage specification is intentionally positioned to not be used in
"zero trust" environments, which in practice means the usage of untrusted sync
and replication nodes while solely relying on encryption as the authorization
mechanism.

To put it a different way -- all encryption has an unpredictable half life, and
some use cases do not permit relying on encryption only for access control.
Instead, a _combination_ of encryption and authorization enforcement by
minimally trusted storage servers is required.

## Terminology

<dl class="termlist definitions" data-sort="ascending">
</dl>

## Authorization

## Spaces

### Space Data Model

`Space` properties:

* `id` - Deterministically set by server if not provided.
* `type` - A sorted array of strings, MUST include the type `Space`.
* `name` (optional) - An arbitrary human-readable name for the space. Does not
  have to be unique.
* `controller` - Determined by server based on who creates the space originally.

#### Space JSON Representation

### Create Space operation

To create a Space:

* Perform an authenticated Create Space operation that includes a Proof of
  (cryptographic material) Possession via a mechanism such as HTTP Signatures.
* The signing DID (from the proof of possession signature) is set as the 
  Space's `controller`

* If an `id` is provided in the Create Space request, it must start with `urn:uuid`.
  If no `id` is provided, it will be generated by the storage server.

* (Optional, out of scope) A given storage provider MAY impose additional
  requirements in order to create a Space for a given controller, such as:
  - a Verifiable Credential representing a pre-arranged onboarding coupon
  - a proof of payment
  - a proof of membership in an organization

#### (HTTP API) POST `/spaces/`

To create a space via HTTP API:

```http
POST /spaces/ HTTP/1.1
Host: example.com
Accept: application/json
Content-type: application/json
Authorization: ...

{
  "name": "Example space #1"
}
```

Example success response:

```http
HTTP/1.1 201 Created
Content-type: application/json
Location: https://example.com/space/81246131-69a4-45ab-9bff-9c946b59cf2e

{
  "id": "81246131-69a4-45ab-9bff-9c946b59cf2e",
  "type": ["Space"],
  "name": "Example space #1",
  "controller": "did:key:z6MkpBMbMaRSv5nsgifRAwEKvHHoiKDMhiAHShTFNmkJNdVW"
}
```

Example error response (missing Proof of Possession signature, unable to 
determine controller):

Example error response (invalid authorization):

Example error response (missing or insufficient onboarding material provided):

Example error response (invalid `id` provided):

Example error response (a space with the specified `id` already exists):

### Read Space operation

* Requires appropriate authorization (root zcap invoked by the space's controller,
  or a zcap granting permission to read a particular space)
* Returns the details for the specified space `id`

#### (HTTP API) GET `/space/{space_id}`

Example request:

```http
GET /space/81246131-69a4-45ab-9bff-9c946b59cf2e HTTP/1.1
Host: example.com
Accept: application/json
Authorization: Signature keyId="did:key:z6MkpBMbMaRSv5nsgifRAwEKvHHoiKDMhiAHShTFNmkJNdVW#z6MkpBMbMaRSv5nsgifRAwEKvHHoiKDMhiAHShTFNmkJNdVW" ...
```

Example success response:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "id": "81246131-69a4-45ab-9bff-9c946b59cf2e",
  "type": ["Space"],
  "name": "Example space #1",
  "controller": "did:key:z6MkpBMbMaRSv5nsgifRAwEKvHHoiKDMhiAHShTFNmkJNdVW"
}
```

Example error response (missing or insufficient authorization):

Example error response (space id not found):

### List Spaces Operation

* Requires appropriate authorization (root zcap invoked by the controller of one
  or more spaces, or a zcap granting permission to read a one or more spaces)

* Lists only the spaces the requester is authorized to see

#### (HTTP API) GET `/spaces/`

Example request:

```http
GET /spaces/ HTTP/1.1
Host: example.com
Accept: application/json
Authorization: ...
```

Example success response (requester has read access to at least one space):

```http
HTTP/1.1 200 OK
Content-type: application/json

...
```

Example success response (requester does NOT have access to any spaces):

```http
HTTP/1.1 200 OK
Content-type: application/json

...
```

Example error response (missing authorization):

### Update Space operation

* Requires appropriate authorization (root zcap invoked by the space's controller,
  or a zcap granting permission to write to a particular space)
* Allows to update the following fields:
  - `name`
  - `controller`

#### (HTTP API) PUT `/space/{space_id}`

Note that this is a _full_ update (partial updates via http `PATCH` verb might
be supported later). However, some fields may not be updated (like `id`) and so
may be omitted from the request payload.

Note that this operation is idempotent.

Example request (updating the name of a space):

```http
PUT /space/81246131-69a4-45ab-9bff-9c946b59cf2e HTTP/1.1
Host: example.com
Content-type: application/json
Accept: application/json
Authorization: ...

{
  "id": "81246131-69a4-45ab-9bff-9c946b59cf2e",
  "name": "Newly renamed space #1",
  "controller": "did:key:z6MkpBMbMaRSv5nsgifRAwEKvHHoiKDMhiAHShTFNmkJNdVW"
}
```

Example success response:

```http
HTTP/1.1 204 No Content
```

Example error response (missing or invalid authorization):

Example error response (invalid `id` provided, the space does not exist):

Example error response (client is attempting to change an immutable field like
the space `id`):

### Delete Space operation

* Requires appropriate authorization (root zcap invoked by the space's controller,
  or a zcap granting permission to write to a particular space)
* Deletes the space and all of the data (collections and resources) contained
  in it
* This operation is idempotent

#### (HTTP API) DELETE `/space/{space_id}`

Example request (no request body):

```http
DELETE /space/81246131-69a4-45ab-9bff-9c946b59cf2e HTTP/1.1
Host: example.com
Accept: application/json
Authorization: ...
```

Example success response:

```http
HTTP/1.1 204 No Content
```

TODO: Decide whether subsequent GET requests to the same space should result in
a 410 Gone, or the usual 404.

Example error response (missing or invalid authorization):

Example error response (invalid `id` provided):

## Collections

A collection is a namespace for Resources, and a unit of configuration, within
a space.

In other storage systems, a collection is also known as: Directory, Folder,
RDBMS Table, Document Collection, Graph, WebAPI FileList, Bucket, Solid
Container, EDV Vault, DWN Collection, and so on.

The use of collections is optional -- when a Space is created, the default `/`
collection is automatically created within that Space.

### Collection Data Model

Collection properties:

* `id`
* `name` - human readable shortname, url slug
* `type` - A sorted array of strings, MUST include the type `Collection`.
* `contents`
* (tbd) a link to the **system meta** auxiliary object
* (tbd) one or more links to controller-modifiable **custom meta** auxiliary
  objects

#### Collection JSON Representation (Activity Streams 2 profile)

Example empty collection, in AS2 format:

```js
{
  // id?
  // name?
  "type": ["Collection"],
  "totalItems": 0,
  "items": []
}
```

### Collection Operations

## Resources and Blobs

### Blob Data Model

A unit of data, in transit or at rest.
(As described in the [W3c FileAPI: Blob Interface](https://w3c.github.io/FileAPI/#blob-section)).

Blob properties:

* byte stream
* `size` - length of the byte stream in bytes
  - Note: Although size can be derived from bytes, it's useful to be able to
    have it up front (for the receiving system to decide to reject an upload
    based on quota / exceeding max size, etc).
* `type` (from the IANA mime type registry) - If not specified, defaults to
  `application/octet-stream`

### Resource Data Model

### Resource Operations

#### Resource HTTP API

## Security and Privacy Considerations

## Internationalization Considerations

### Language and Base Direction

<section class="appendix informative">

## IANA Considerations

This section will be submitted to the Internet Engineering Steering Group (IESG)
for review, approval, and registration with IANA.
</section>

