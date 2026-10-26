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

## Versioning

Keep changes to existing field numbers and meanings backward-compatible. Do not reuse removed field numbers. Review contract changes before updating consuming services. Generated Java protobuf messages and gRPC stubs will be packaged as a versioned dependency for the services and BFF to consume.
