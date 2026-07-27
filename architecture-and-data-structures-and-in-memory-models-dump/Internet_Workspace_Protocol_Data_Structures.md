# Internet Workspace Protocol --- Data Structure View

See below.

## Overall Object Graph

``` text
WorkspaceAuthority
│
├── Workspace
│   ├── WorkspaceIdentifier
│   ├── Devices[]
│   ├── Applications[]
│   │   ├── WorkspaceMapping
│   │   │   └── DataSink
│   │   ├── Lifecycle
│   │   └── BillingMetadata
│   ├── Policies
│   └── Metadata
│
└── AuditLog
```

## Concepts

``` text
Workspace
├── workspaceId
├── authority
├── metadata
├── devices[]
├── applications[]
└── policies
```

``` text
WorkspaceIdentifier
├── permanentId
├── temporaryHandle
├── accessToken
└── version
```

``` text
WorkspaceAuthority
├── workspaceTable
├── tokenTable
├── deviceTable
├── applicationRegistry
└── auditLog
```

## Requirements

``` text
WorkspaceState
├── Persistence
├── Isolation
├── Concurrency
└── Recoverability
```

## Protocol

``` text
Identity
├── workspaceHandle
├── accessToken
├── deviceId
└── userId
```

``` text
RoutingTable
Workspace
 ├── Application → DataSink
 ├── Application → DataSink
 └── Application → DataSink
```

``` text
Discovery
├── applicationId
├── endpoints
├── capabilities
└── workspaceSupport
```

## Management

``` text
WorkspaceManager
├── create()
├── archive()
├── recover()
├── rename()
├── enumerate()
└── authorizeDevice()
```

## Application Integration

``` text
Application
├── applicationId
├── dataSinks[]
├── workspaceMappings[]
├── lifecycle
└── billing
```

``` text
WorkspaceMapping
WorkspaceId
    ↓
ApplicationId
    ↓
DataSinkId
```

``` text
DataSink
├── sinkId
├── sessionId
├── accountId
├── documentId
└── metadata
```

``` text
Lifecycle

Created
   ↓
Associated
   ↓
Active
   ↓
Archived
   ↓
Recovered
```
