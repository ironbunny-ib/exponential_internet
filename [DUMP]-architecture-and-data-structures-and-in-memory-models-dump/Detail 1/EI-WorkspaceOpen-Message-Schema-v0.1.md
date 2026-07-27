# EI Internet-Draft (Excerpt)

# Message Schema: `WorkspaceOpen`

**Status:** Draft v0.1\
**Category:** Standards Track (Proposed)

------------------------------------------------------------------------

## 1. Purpose

The `WorkspaceOpen` message establishes a new Workspace Context between
an EI-aware client and a Workspace Authority. It requests the creation
(or resumption) of a workspace routing context and returns an opaque
Workspace Handle.

This message MUST NOT expose the permanent Workspace Identifier.

------------------------------------------------------------------------

## 2. Message Exchange

``` text
Client                      Workspace Authority

WorkspaceOpen   ---------->
                 Validate
                 Create / Resume Workspace
                 Allocate Handle
WorkspaceOpened <----------
```

------------------------------------------------------------------------

## 3. HTTP Mapping

Method:

``` text
POST /.well-known/ei/workspace/open
```

Media Type:

``` text
application/ei+json
```

------------------------------------------------------------------------

## 4. Request Schema

  -------------------------------------------------------------------------------------------
  Field              Type                     Required             Description
  ------------------ ------------------------ -------------------- --------------------------
  version            uint16                   Yes                  EI protocol version
                                                                   requested.

  nonce              bytes(16-32)             Yes                  Cryptographically random
                                                                   request nonce.

  capabilities       array`<string>`{=html}   Yes                  Requested optional
                                                                   protocol capabilities.

  clientInstanceId   uuid                     Yes                  Stable identifier for this
                                                                   runtime instance.

  resumeToken        string                   No                   Token requesting
                                                                   restoration of an existing
                                                                   workspace.

  extensions         object                   No                   Vendor-specific extension
                                                                   namespace.
  -------------------------------------------------------------------------------------------

Example:

``` json
{
  "version": 1,
  "nonce": "fcb0cb8a51d84c22a45cb3d0d7731ea7",
  "clientInstanceId": "4cdb71fa-7e8f-4e88-8a8c-78efdb6d4a82",
  "capabilities": [
    "workspace-routing",
    "sink-events"
  ]
}
```

------------------------------------------------------------------------

## 5. Response Schema

  -----------------------------------------------------------------------------------------------
  Field                  Type                     Required             Description
  ---------------------- ------------------------ -------------------- --------------------------
  workspaceHandle        string                   Yes                  Opaque temporary routing
                                                                       handle.

  expiresIn              uint32                   Yes                  Lifetime (seconds).

  refreshHandle          string                   Yes                  Handle used for rotation.

  negotiatedVersion      uint16                   Yes                  Agreed protocol version.

  acceptedCapabilities   array`<string>`{=html}   Yes                  Capabilities enabled for
                                                                       this session.

  serverNonce            bytes(16-32)             Yes                  Fresh server nonce.
  -----------------------------------------------------------------------------------------------

Example:

``` json
{
  "workspaceHandle": "wh_A6Qf8kN4...",
  "expiresIn": 3600,
  "refreshHandle": "rh_H91z...",
  "negotiatedVersion": 1,
  "acceptedCapabilities": [
    "workspace-routing",
    "sink-events"
  ],
  "serverNonce": "d0b8f2d53c6c..."
}
```

------------------------------------------------------------------------

## 6. Validation Rules

1.  `version` MUST be supported by both peers.
2.  `nonce` MUST be unique for every request.
3.  Unknown mandatory fields MUST cause rejection.
4.  Unknown extension fields MUST be ignored unless defined by a
    negotiated extension.
5.  `workspaceHandle` MUST be treated as opaque by clients.

------------------------------------------------------------------------

## 7. Error Responses

  EI Code   HTTP   Meaning
  --------- ------ ---------------------------------
  EI1000    400    Malformed message
  EI1001    401    Invalid resume token
  EI1002    406    Unsupported protocol version
  EI1003    422    Capability negotiation failed
  EI1004    503    Workspace Authority unavailable

Error body:

``` json
{
  "code": "EI1002",
  "message": "Unsupported protocol version",
  "retryable": false
}
```

------------------------------------------------------------------------

## 8. Security Considerations

-   Clients MUST generate a fresh nonce for every request.
-   Workspace Handles MUST contain at least 128 bits of entropy.
-   Permanent Workspace Identifiers MUST NOT appear in request or
    response bodies.
-   All exchanges MUST occur over an authenticated secure transport.
-   Replay detection SHOULD be implemented using nonce tracking.

------------------------------------------------------------------------

## 9. Future Extensions

Reserved extension points include:

-   Authentication binding
-   Workspace migration
-   Multi-device synchronisation
-   Delegated workspace ownership
-   Vendor-specific capability negotiation
