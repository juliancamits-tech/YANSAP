# Modular Architecture

A modular monolith architecture focused on strong encapsulation, explicit contracts, and easy evolution toward microservices.

## Goals

- Strong module boundaries
- Explicit public contracts
- Internal implementation details hidden by default
- Easy extraction of modules into microservices
- Minimal architectural constraints inside modules

## Core Concepts

Each module is composed of two projects:

```text
Orders.Module.Contracts
Orders.Module
```

See the Architecture Principles document for full details.
[Click here](docs/ARCHITECTURE_PRINCIPLES.md)