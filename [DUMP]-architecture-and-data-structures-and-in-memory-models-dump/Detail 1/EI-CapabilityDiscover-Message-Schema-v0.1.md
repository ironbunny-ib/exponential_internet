# EI Internet-Draft (Excerpt)

# Message Schema: `CapabilityDiscover`

**Status:** Draft v0.1\
**Category:** Standards Track (Proposed)

------------------------------------------------------------------------

# 1. Purpose

`CapabilityDiscover` enables an EI-aware client to determine which
protocol versions, message families, extensions, limits and optional
features are supported by a peer before attempting EI operations.

Capability discovery SHALL occur before any optional EI protocol feature
is used.

This message does **not** establish a workspace.

------------------------------------------------------------------------

# 2. Design Goals

-   Version negotiation
-   Extension discovery
-   Transport feature discovery
-   Implementation interoperability
-   Forward compatibility

------------------------------------------------------------------------

# 3. HTTP Mapping

``` text
OPTIONS /.well-known/ei
Accept: application/ei+json
```

Servers MAY also expose the same resource using:

``` text
GET /.well-known/ei
```

for cacheable discovery.

------------------------------------------------------------------------

# 4. Request Schema

  ------------------------------------------------------------------------------------------------
  Field                   Type                     Required             Description
  ----------------------- ------------------------ -------------------- --------------------------
  version                 uint16                   Yes                  Highest EI version
                                                                        supported by the client.

  supportedVersions       array`<uint16>`{=html}   Yes                  Versions understood by the
                                                                        client.

  requestedCapabilities   array`<string>`{=html}   No                   Capabilities of interest.
                                                                        Empty means "all".

  clientImplementation    string                   No                   Implementation identifier.

  extensions              object                   No                   Vendor extension
                                                                        namespace.
  ------------------------------------------------------------------------------------------------

Example

``` json
{
  "version":2,
  "supportedVersions":[1,2],
  "requestedCapabilities":[
    "workspace-routing",
    "sink-events"
  ],
  "clientImplementation":"ei-browser/0.4"
}
```

------------------------------------------------------------------------

# 5. Response Schema

  Field               Type                     Required   Description
  ------------------- ------------------------ ---------- -------------------------------
  negotiatedVersion   uint16                   Yes        Selected protocol version.
  protocolName        string                   Yes        Protocol identifier.
  capabilities        array`<object>`{=html}   Yes        Supported capabilities.
  limits              object                   Yes        Operational limits.
  transports          array`<string>`{=html}   Yes        Supported transport mappings.
  extensions          array`<object>`{=html}   No         Extension descriptors.

Capability object:

  Field      Type      Description
  ---------- --------- -----------------------------------
  id         string    Stable capability identifier.
  version    uint16    Capability revision.
  required   boolean   Mandatory for interoperability.
  status     enum      stable, experimental, deprecated.

Example

``` json
{
  "negotiatedVersion":2,
  "protocolName":"EI",
  "capabilities":[
    {
      "id":"workspace-routing",
      "version":1,
      "required":true,
      "status":"stable"
    },
    {
      "id":"sink-events",
      "version":2,
      "required":false,
      "status":"experimental"
    }
  ],
  "limits":{
    "maxMessageSize":1048576,
    "maxCapabilityCount":128
  },
  "transports":[
    "http3",
    "websocket"
  ]
}
```

------------------------------------------------------------------------

# 6. Negotiation Rules

1.  The server SHALL select the highest mutually supported protocol
    version.
2.  Unknown capability identifiers MUST be ignored.
3.  Required capabilities that cannot be satisfied SHALL cause
    negotiation failure.
4.  Experimental capabilities MUST NOT be assumed unless explicitly
    advertised.
5.  Clients SHOULD cache successful discovery responses.

------------------------------------------------------------------------

# 7. Error Responses

  EI Code   HTTP   Meaning
  --------- ------ ---------------------------------
  EI1200    400    Invalid discovery request
  EI1201    406    No common protocol version
  EI1202    422    Required capability unavailable
  EI1203    429    Discovery rate limit exceeded
  EI1204    503    Discovery service unavailable

Example

``` json
{
  "code":"EI1201",
  "message":"No mutually supported protocol version.",
  "retryable":false
}
```

------------------------------------------------------------------------

# 8. Caching

Discovery responses MAY include:

-   ETag
-   Cache-Control
-   Expires

Clients SHOULD revalidate cached capabilities after software updates or
protocol-version changes.

------------------------------------------------------------------------

# 9. Security Considerations

Capability discovery MUST NOT disclose:

-   permanent Workspace Identifiers
-   active Workspace Handles
-   authenticated user identity

Servers MAY omit sensitive or administrative capabilities based on
policy.

Capability identifiers MUST be globally unique within the EI namespace.

------------------------------------------------------------------------

# 10. IANA Considerations (Proposed)

An "EI Capability Registry" SHOULD define:

-   Capability Identifier
-   Version
-   Stability
-   Specification Reference
-   Required/Optional Classification

Capability identifiers are intended to remain stable across protocol
revisions.
