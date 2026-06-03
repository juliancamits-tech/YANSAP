# Architecture Principles

## Module Structure

Each module is composed of:

```text
*.Module.Contracts
*.Module
```

## Contracts

Responsibilities:

- Public interfaces
- Public DTOs
- Requests
- Responses
- Structs
- Simple self-contained validation attributes

Rules:

- Must not reference any other module
- Must not reference another Contracts project
- Must not contain implementation logic
- Must avoid implementation-oriented dependencies

Contracts are leaf nodes in the dependency graph.

## Module

Responsibilities:

- Features
- Shared components
- Infrastructure
- Endpoint registration
- Contract implementations

Rules:

- References its own Contracts project
- May reference other Contracts projects
- Must not reference another Module project

## Visibility

Everything inside a Module should be internal by default.

The only public entry point is Bootstrap.

## Bootstrap

Bootstrap is the integration point between a module and a host application.

Possible responsibilities:

- Dependency injection registration
- Endpoint registration
- Hosted services registration
- Middleware registration
- Other host-specific integration concerns

No required interface or implementation pattern is imposed.

## Features

A Feature represents a functional capability or use case.

Examples:

```text
GetOrder
GetOrders
CreateOrder
CancelOrder
```

The framework does not impose any internal structure on Features.

## Shared

Shared contains elements reused by multiple Features within the same module.

If multiple Features depend on the same implementation, that implementation should be promoted to Shared.

## Dependency Rules

Allowed:

```text
Module -> Module.Contracts
Module -> Other.Module.Contracts
API -> Module
```

Forbidden:

```text
Module -> Other.Module
Contracts -> Anything
Contracts -> Other.Contracts
```
