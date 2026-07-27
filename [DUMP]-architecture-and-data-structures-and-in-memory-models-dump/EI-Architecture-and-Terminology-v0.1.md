# EI Architecture and Terminology

**Internet-Draft (Informational)**\
**Status:** Draft v0.1

------------------------------------------------------------------------

# Abstract

This document defines the architectural model and normative terminology
for the Execution Internet (EI). It does not specify wire formats.
Instead, it establishes the vocabulary, actors, trust boundaries, and
architectural principles upon which all EI protocol specifications are
built.

Normative keywords such as MUST, SHOULD, and MAY are to be interpreted
as described in RFC 2119 and RFC 8174.

------------------------------------------------------------------------

# 1. Introduction

EI extends the Internet from transporting requests between endpoints to
transporting *execution context*.

Where HTTP standardises resource access, EI standardises workspace-aware
execution across applications, origins, devices, and runtimes.

EI is intentionally designed as an extension layer that can coexist with
today's Internet infrastructure.

------------------------------------------------------------------------

# 2. Design Goals

An EI implementation SHALL strive to provide:

1.  Persistent execution context.
2.  Cross-application workspace continuity.
3.  Runtime-managed context routing.
4.  Minimal exposure of permanent identifiers.
5.  Backwards compatibility with existing Internet protocols.
6.  Capability-based protocol evolution.

------------------------------------------------------------------------

# 3. Architectural Principles

## 3.1 Separation of Identity and Routing

A permanent Workspace Identifier identifies a workspace.

A temporary Workspace Handle routes protocol messages.

The permanent identifier SHALL NOT appear on the network during ordinary
operation.

## 3.2 Runtime Ownership

Applications consume workspace context.

They do not own it.

The browser, operating system, or equivalent runtime owns routing
decisions.

## 3.3 Capability Negotiation

Optional behaviour SHALL be negotiated explicitly.

Protocol versions alone MUST NOT imply support for optional features.

------------------------------------------------------------------------

# 4. Actors

## Client Runtime

The software responsible for executing EI-aware applications and
protocol logic.

Examples include:

-   Web browsers
-   Native application runtimes
-   Enterprise gateways

Responsibilities:

-   Handle management
-   Capability negotiation
-   Event dispatch
-   Workspace routing

## Workspace Authority

The trusted authority that maps Workspace Handles to Workspace
Identifiers.

Responsibilities:

-   Workspace creation
-   Handle validation
-   Handle rotation
-   Policy enforcement

## EI-aware Application

An application capable of participating in EI protocols.

Applications SHOULD consume workspace context but SHOULD NOT infer
workspace identity.

## Peer

Any endpoint implementing one or more EI capabilities.

------------------------------------------------------------------------

# 5. Core Concepts

## Workspace

A logical execution environment representing a coherent unit of work.

A Workspace MAY span:

-   applications
-   origins
-   browser sessions
-   transport connections
-   devices

## Workspace Identifier

A globally unique, persistent identifier.

Properties:

-   immutable
-   opaque
-   globally unique
-   never reused

## Workspace Handle

A temporary routing credential.

Properties:

-   opaque
-   revocable
-   rotatable
-   time-limited

## Workspace Context

The runtime-visible state associated with a workspace.

Examples include routing metadata, negotiated capabilities, permissions
and active subscriptions.

## Data Sink

A destination that receives workspace events or state changes.

A Data Sink may represent a queue, service, local runtime component or
another application.

## Workspace Event

A protocol-defined occurrence affecting a workspace.

Examples:

-   workspace opened
-   handle rotated
-   sink updated
-   migration completed

------------------------------------------------------------------------

# 6. Trust Boundaries

EI distinguishes three trust domains.

### Runtime

Trusted to manage routing and workspace context.

### Applications

Trusted only within their granted permissions.

### Network

Untrusted except where protected by authenticated secure transport.

Implementations SHOULD minimise information crossing trust boundaries.

------------------------------------------------------------------------

# 7. Ownership Model

  Object                 Owner
  ---------------------- ------------------------
  Workspace Identifier   Workspace Authority
  Workspace Handle       Workspace Authority
  Workspace Context      Runtime
  Application State      Application
  Capability Registry    Protocol Specification

Ownership determines which component may create, modify or revoke an
object.

------------------------------------------------------------------------

# 8. Lifecycle Overview

A typical lifecycle is:

1.  Capability discovery.
2.  Workspace establishment.
3.  Handle allocation.
4.  Application execution.
5.  Event propagation.
6.  Handle rotation.
7.  Workspace suspension or termination.

------------------------------------------------------------------------

# 9. Extensibility

EI evolves through:

-   capability registration
-   message definitions
-   protocol version negotiation
-   vendor extensions

Extensions SHALL NOT redefine existing standard semantics.

------------------------------------------------------------------------

# 10. Security Model

EI assumes:

-   authenticated secure transport
-   cryptographically strong handles
-   least-privilege applications
-   explicit capability negotiation

Workspace Identifiers SHALL remain hidden from ordinary protocol
exchanges.

------------------------------------------------------------------------

# 11. Relationship to Existing Internet Protocols

EI complements rather than replaces existing protocols.

  Existing Protocol   EI Contribution
  ------------------- ------------------------------------
  HTTP                Workspace-aware execution context
  WebSocket           Context-aware messaging
  QUIC                Transport substrate
  TLS                 Confidentiality and authentication

------------------------------------------------------------------------

# 12. Future Specifications

This document is intended to be referenced by future EI specifications,
including:

-   Workspace Context Establishment Protocol
-   Capability Discovery Protocol
-   Workspace Event Protocol
-   Data Sink Protocol
-   Security Architecture
-   Browser Implementation Considerations

Those documents define protocol behaviour; this document defines the
architectural vocabulary shared between them.
