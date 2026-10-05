# Local fork patch

Base: `GuidoJeuken-6512/lambda_heat_pumps`, tag `V2.8.6`.

The local patch normalizes the configuration-entry name before cycling and
energy entity lookups. The coordinator passes the user-facing name
(`Lambda EU15L`), while registered unique IDs use the normalized prefix
(`lambdaeu15l`). Without normalization, counters could be written to a
detached fallback state instead of the registered entity.

The fork intentionally keeps the upstream Modbus register handling and entity
IDs unchanged.
