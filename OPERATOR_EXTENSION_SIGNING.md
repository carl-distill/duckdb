# Operator extension signing

The 1.5.5 release has no configurable extension trust store. This fork accepts
one additional RSA-2048 public key at build time through
`-DDUCKDB_OPERATOR_PUBLIC_KEY_FILE=/path/to/public.pem`. The private key is not
needed to build DuckDB and must not be included in its source or images.

The compiled public key trusts extensions signed by that key. DuckDB's core
and community keys and the existing signature verifier remain in use. Keep
`allow_unsigned_extensions=false`; unsigned, modified, and differently signed
extensions must fail verification. Omitting the build option retains the
upstream key set. Changing the operator key requires rebuilding DuckDB.

Upstream [PR #24777](https://github.com/duckdb/duckdb/pull/24777) adds
repository-specific public keys on the 2.0 release line. This small patch keeps
the 1.5.5 ABI and extension-loading interface until that upgrade is adopted.
