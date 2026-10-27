# Assignment 4 - Central gRPC Contracts

This repository is the source of truth for the Protocol Buffer contracts shared by the backend microservices. It contains API definitions only; it is not a running service and does not store application data.

## Contracts

- `src/main/proto/user/v1/user_service.proto`: create, fetch, and list users.
- `src/main/proto/product/v1/product_service.proto`: create, fetch, and list products; update stock; reserve and release stock for order processing.
- `src/main/proto/order/v1/order_service.proto`: create, fetch, list all orders, and list orders for a user.

## Contract conventions

- Database identifiers use `int64` to match Java `Long`.
- Quantities use `int32`.
- Product prices are decimal strings (for example, `"19.99"`) so Java `BigDecimal` precision is preserved.
- Timestamps use `google.protobuf.Timestamp`; service implementations convert to/from their Java date-time types.
- Product `UpdateStock` sets the exact stock quantity; it is not an increment/decrement operation.
- `ReserveStock` and `ReleaseStock` are separate operations intended for internal order processing.
- User creation does not accept a role from the caller. The service assigns the role, defaulting to `GUEST`; callers must not be able to self-assign `ADMIN`.
- The first enum value is `0`, as required by proto3. Here, `GUEST` and `PENDING` are the default enum values.

## Build generated Java and gRPC code

The Gradle build uses Java 17, Protocol Buffers, and the gRPC Java code-generation plugin. Generated sources are produced during the normal Gradle build; do not edit generated files by hand.

From the repository root, run:

```bash
gradle clean build
```

To install the generated library in your local Maven repository for the services and BFF to consume:

```bash
gradle publishToMavenLocal
```

The published coordinates are:

```groovy
implementation 'com.assignment:assignment4-protos:0.1.0-SNAPSHOT'
```

The consuming Gradle projects must include `mavenLocal()` before `mavenCentral()` in their repositories while using this local snapshot. The artifact is not published to a remote Maven repository by these tasks.

## Versioning

Keep changes to existing field numbers and meanings backward-compatible. Do not reuse removed field numbers. Review contract changes before updating consuming services. Generated Java protobuf messages and gRPC stubs are packaged as a versioned dependency for the services and BFF to consume.
