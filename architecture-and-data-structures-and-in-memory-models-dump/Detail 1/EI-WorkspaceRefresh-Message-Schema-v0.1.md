# EI Internet-Draft (Excerpt)

# Message Schema: `WorkspaceRefresh`

**Status:** Draft v0.1\
**Category:** Standards Track (Proposed)

------------------------------------------------------------------------

# 1. Purpose

`WorkspaceRefresh` renews an expiring Workspace Handle without changing
the underlying Workspace Context. It allows clients to continue using
the same workspace while rotating bearer credentials.

A successful refresh MUST invalidate the old handle after a configurable
overlap period.

------------------------------------------------------------------------

# 2. Exchange

``` text
Client                         Workspace Authority

WorkspaceRefresh ----------->
                    Validate Refresh Handle
                    Issue New Workspace Handle
WorkspaceRefreshed <-----------
```

------------------------------------------------------------------------

# 3. HTTP Mapping

``` text
POST /.well-known/ei/workspace/refresh
Content-Type: application/ei+json
```

------------------------------------------------------------------------

# 4. Request Schema

  Field                    Type           Required   Description
  ------------------------ -------------- ---------- --------------------------------
  refreshHandle            string         Yes        Opaque refresh credential.
  currentWorkspaceHandle   string         Yes        Currently active handle.
  nonce                    bytes(16-32)   Yes        Replay protection nonce.
  requestedLifetime        uint32         No         Preferred lifetime in seconds.
  extensions               object         No         Vendor extensions.

Example:

``` json
{
  "refreshHandle":"rh_hF92A7...",
  "currentWorkspaceHandle":"wh_G8Ks...",
  "nonce":"09c8e8b45c16493cbdbd6ef6c9d0c8d1",
  "requestedLifetime":3600
}
```

------------------------------------------------------------------------

# 5. Response Schema

  --------------------------------------------------------------------------------
  Field             Type           Required             Description
  ----------------- -------------- -------------------- --------------------------
  workspaceHandle   string         Yes                  Newly issued handle.

  expiresIn         uint32         Yes                  Lifetime in seconds.

  overlapPeriod     uint32         Yes                  Seconds during which both
                                                        handles remain valid.

  serverNonce       bytes(16-32)   Yes                  Server-generated nonce.

  issuedAt          timestamp      Yes                  UTC issuance time.
  --------------------------------------------------------------------------------

Example:

``` json
{
  "workspaceHandle":"wh_t2Q9...",
  "expiresIn":3600,
  "overlapPeriod":30,
  "serverNonce":"fe881f5c2b0e...",
  "issuedAt":"2026-07-27T08:15:32Z"
}
```

------------------------------------------------------------------------

# 6. Processing Rules

The Workspace Authority MUST:

1.  Validate the refresh handle.
2.  Verify the current workspace handle belongs to the same workspace.
3.  Reject replayed nonces.
4.  Generate a fresh workspace handle.
5.  Preserve the Workspace Context.
6.  Return the new handle.

The client MUST:

1.  Begin using the new handle immediately.
2.  Discard the previous handle after the overlap period expires.
3.  Never attempt to derive workspace identity from either handle.

------------------------------------------------------------------------

# 7. Error Responses

  EI Code   HTTP   Meaning
  --------- ------ ---------------------------------
  EI1100    400    Malformed request
  EI1101    401    Invalid refresh handle
  EI1102    401    Expired refresh handle
  EI1103    409    Handle mismatch
  EI1104    429    Refresh rate limit exceeded
  EI1105    503    Workspace Authority unavailable

Example:

``` json
{
  "code":"EI1103",
  "message":"Current workspace handle does not match refresh handle.",
  "retryable":false
}
```

------------------------------------------------------------------------

# 8. Security Considerations

-   Refresh handles SHOULD have a longer lifetime than workspace
    handles.
-   Refresh handles MUST NOT be accepted after explicit revocation.
-   Replay detection SHOULD combine nonce tracking with expiration
    windows.
-   New workspace handles MUST be cryptographically independent of
    previous handles.
-   Rotation MUST NOT reveal or alter the permanent Workspace
    Identifier.

------------------------------------------------------------------------

# 9. Interoperability Notes

Clients that do not support handle rotation SHOULD establish a new
workspace using `WorkspaceOpen`.

Servers MAY refuse refresh requests when protocol versions are
incompatible or policy requires complete re-authentication.
