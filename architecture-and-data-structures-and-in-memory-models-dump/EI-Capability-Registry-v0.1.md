# EI Internet-Draft (Excerpt)

# EI Capability Registry

**Status:** Draft v0.1\
**Category:** Standards Track (Proposed)

------------------------------------------------------------------------

# 1. Purpose

The EI Capability Registry defines the globally recognised capability
identifiers that may be advertised, negotiated and enabled between EI
protocol peers.

Capabilities provide a forward-compatible mechanism for introducing
optional features without requiring a new protocol version.

Capability identifiers are protocol-level contracts and SHALL remain
stable.

------------------------------------------------------------------------

# 2. Registry Structure

Every registry entry SHALL define:

  Field           Description
  --------------- --------------------------------------------
  Identifier      Globally unique capability name
  Version         Capability revision
  Status          stable, experimental, deprecated, obsolete
  Required        Mandatory or optional
  Depends On      Required prerequisite capabilities
  Specification   Normative specification reference

Capability identifiers are case-sensitive ASCII strings.

Example naming convention:

``` text
workspace-routing
workspace-events
sink-events
cross-device
```

Vendor capabilities MUST use reverse-domain notation.

Example

``` text
com.example.priority-routing
org.browser.fast-resume
```

------------------------------------------------------------------------

# 3. Standard Capability Registry

  ----------------------------------------------------------------------------
  Identifier              Req.        Initial Version      Description
  ---------------------- ------- ------------------------- -------------------
  workspace-routing        Yes               1             Enables
                                                           workspace-aware
                                                           request routing.

  workspace-open           Yes               1             Supports
                                                           WorkspaceOpen
                                                           message.

  workspace-refresh        Yes               1             Supports handle
                                                           rotation.

  workspace-close          No                1             Supports explicit
                                                           workspace
                                                           termination.

  workspace-events         No                1             Enables
                                                           asynchronous
                                                           workspace events.

  sink-events              No                1             Supports Data Sink
                                                           notifications.

  capability-discovery     Yes               1             Supports protocol
                                                           discovery.

  workspace-recovery       No                1             Supports crash
                                                           recovery.

  workspace-migration      No                1             Supports moving
                                                           active workspaces.

  cross-device             No                1             Supports multiple
                                                           devices sharing a
                                                           workspace.

  delegated-access         No                1             Allows delegated
                                                           workspace
                                                           authority.

  audit-events             No                1             Emits audit trail
                                                           events.
  ----------------------------------------------------------------------------

------------------------------------------------------------------------

# 4. Capability Descriptor

Each advertised capability SHALL use the following structure.

  Field          Type                     Required   Description
  -------------- ------------------------ ---------- ---------------------------------------------
  id             string                   Yes        Registry identifier.
  version        uint16                   Yes        Supported capability revision.
  status         enum                     Yes        stable, experimental, deprecated, obsolete.
  required       boolean                  Yes        Whether negotiation is mandatory.
  dependencies   array`<string>`{=html}   No         Capability prerequisites.
  parameters     object                   No         Capability-specific settings.

Example

``` json
{
  "id":"workspace-events",
  "version":2,
  "status":"stable",
  "required":false,
  "dependencies":[
      "workspace-routing"
  ],
  "parameters":{
      "maxPendingEvents":1024
  }
}
```

------------------------------------------------------------------------

# 5. Negotiation Rules

1.  Identifiers SHALL be compared exactly.
2.  The highest mutually supported version SHALL be selected.
3.  Missing required capabilities SHALL terminate negotiation.
4.  Optional capabilities MAY be ignored.
5.  Dependency chains SHALL be validated before activation.
6.  Unknown capabilities MUST be ignored unless marked required.

------------------------------------------------------------------------

# 6. Capability Lifecycle

Capabilities progress through the following stages.

  Status         Meaning
  -------------- -------------------------------------------
  experimental   Behaviour may change incompatibly.
  stable         Suitable for production interoperability.
  deprecated     Supported but discouraged.
  obsolete       SHALL NOT be negotiated.

Clients SHOULD warn when deprecated capabilities are negotiated.

------------------------------------------------------------------------

# 7. Versioning

Capability versions evolve independently of EI protocol versions.

Example

``` text
EI Protocol Version: 2

workspace-events: Version 3
sink-events: Version 1
cross-device: Version 2
```

A protocol implementation MAY support multiple revisions of the same
capability.

------------------------------------------------------------------------

# 8. Registry Update Policy

New standard capability registrations SHALL include:

-   identifier
-   purpose
-   wire semantics
-   interoperability considerations
-   security considerations
-   version history

Existing identifiers SHALL NOT be repurposed.

Breaking semantic changes REQUIRE a new capability version.

------------------------------------------------------------------------

# 9. Security Considerations

Capability advertisements MUST NOT expose:

-   Workspace Identifiers
-   Workspace Handles
-   User identities
-   Session credentials

Servers MAY suppress administrative or implementation-specific
capabilities.

Capability negotiation SHALL occur over authenticated secure transport.

------------------------------------------------------------------------

# 10. IANA Considerations (Proposed)

An "EI Capability Registry" SHOULD be maintained with the following
columns:

  Column
  -------------------------
  Identifier
  Current Version
  Status
  Required
  Dependencies
  Specification Reference
  Registration Date
  Change Controller

Registration policy SHOULD be "Specification Required" with expert
review.

Reserved prefixes:

``` text
ei-
vendor-
experimental-
```

Vendor implementations SHOULD prefer reverse-domain naming to avoid
collisions.
