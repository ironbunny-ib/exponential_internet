# Internet Workspace Protocol --- Architectural Design Notes (v0.1)

> This document expands the conceptual model behind the Internet
> Workspace Protocol (IWP). It is intentionally implementation-oriented
> and precedes the wire protocol specification.

------------------------------------------------------------------------

# Workspace

## Fields

``` text
Workspace
├── workspaceId
├── authorityId
├── metadata
├── owner
├── devices[]
├── members[]
├── applications[]
├── routingTable
├── mappingTable
├── eventLog
├── policies
└── state
```

## Invariants

-   Every Workspace has exactly one permanent Workspace Identifier.
-   A Workspace belongs to exactly one Workspace Authority.
-   Workspace identifiers are immutable.
-   A Workspace may exist without applications.
-   Every WorkspaceMapping references exactly one Workspace.
-   Archived workspaces are immutable except for recovery metadata.

## Ownership

  Object              Owner
  ------------------- ---------------------
  Workspace           Workspace Authority
  Metadata            Workspace Authority
  Routing Table       Workspace Authority
  Mapping Table       Workspace Authority
  Policies            Workspace Authority
  Application State   Application

## Lifecycle

``` text
Created
    ↓
Active
    ↓
Archived
    ↓
Recovered
    ↓
Deleted
```

------------------------------------------------------------------------

# Workspace Authority

## Fields

``` text
WorkspaceAuthority
├── authorityId
├── workspaceRegistry
├── tokenRegistry
├── deviceRegistry
├── applicationRegistry
├── routingEngine
├── synchronizer
└── auditLog
```

## Indexes

``` text
workspaceId → Workspace
deviceId → Device
applicationId → Application
token → Workspace
workspaceHandle → Workspace
```

## Lookup Tables

``` text
Workspace
    ↓
Applications

Application
    ↓
Data Sinks

Device
    ↓
Authorized Workspaces
```

## Algorithms

-   Create Workspace
-   Resolve Workspace Handle
-   Validate Workspace Token
-   Associate Data Sink
-   Compute Mapping Differences
-   Synchronize Devices
-   Recover Workspace
-   Garbage Collect Expired Tokens

------------------------------------------------------------------------

# Workspace Mapping

## Fields

``` text
WorkspaceMapping
├── workspaceId
├── applicationId
├── dataSinkId
├── priority
├── lastAccessed
└── metadata
```

## State Machine

``` text
Unmapped
    ↓
Associated
    ↓
Active
    ↓
Suspended
    ↓
Removed
```

## Event Transitions

``` text
Associate
Change Sink
Suspend
Resume
Remove
Recover
```

------------------------------------------------------------------------

# Data Sink

## Fields

``` text
DataSink
├── sinkId
├── applicationId
├── sessionId
├── accountId
├── resourceId
├── resourceType
└── metadata
```

## Relationships

``` text
Workspace
    ↓
Application
    ↓
Data Sink
        ├── Session
        ├── Account
        ├── Document
        ├── Organisation
        ├── Repository
        └── Mailbox
```

Applications decide what a Data Sink represents.

------------------------------------------------------------------------

# Protocol Messages

## Workspace Operations

``` text
CREATE_WORKSPACE
OPEN_WORKSPACE
ARCHIVE_WORKSPACE
RECOVER_WORKSPACE
LIST_WORKSPACES
```

## Application Operations

``` text
ASSOCIATE
STATE_UPDATE
DISASSOCIATE
```

## Authority Operations

``` text
AUTHORIZE_DEVICE
DISCOVER
RESOLVE
REFRESH_TOKEN
```

## Generic Request

``` json
{
  "workspaceHandle": "...",
  "applicationId": "...",
  "payload": {}
}
```

## Generic Response

``` json
{
  "status": "ok",
  "version": 1,
  "payload": {}
}
```

## Example Wire Format (HTTP)

``` http
POST /workspace/state
Content-Type: application/json

{
    "workspaceHandle":"abc",
    "applicationId":"github.com",
    "activeContexts":[]
}
```

------------------------------------------------------------------------

# State Machines

## Workspace

``` text
Create
    ↓
Open
    ↓
Active
    ↓
Archive
    ↓
Recover
```

## Mapping

``` text
Associate
    ↓
Change Sink
    ↓
Synchronize
    ↓
Disassociate
```

## Device

``` text
Unauthorized
      ↓
Authorized
      ↓
Revoked
```

------------------------------------------------------------------------

# Sequence Diagrams

## Create Workspace

``` text
Browser
    │
    │ Create
    ▼
Workspace Authority
    │
Generate Workspace
    │
Return Handle
```

------------------------------------------------------------------------

## Associate Application

``` text
Browser
      │
      ▼
Application
      │
Associate Sink
      ▼
Workspace Authority
      │
Persist Mapping
      ▼
Success
```

------------------------------------------------------------------------

## Switch Data Sink

``` text
Browser
      │
      ▼
Application
      │
Active Context Changed
      ▼
Workspace Authority
      │
Update Mapping
      ▼
Workspace Updated
```

------------------------------------------------------------------------

## Recover Workspace

``` text
Browser
      │
Open Workspace
      ▼
Workspace Authority
      │
Lookup Mappings
      ▼
Applications
      │
Restore Context
      ▼
Browser
```

------------------------------------------------------------------------

## Overall Architecture

``` text
Browser
      │
      ▼
Workspace Authority
      │
      ├── Workspace Registry
      ├── Routing Engine
      ├── Mapping Table
      ├── Device Registry
      └── Token Registry
             │
             ▼
Applications
      │
      ▼
Data Sinks
```
