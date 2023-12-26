<h1 align="center">
    tower-sessions-dynamodb-store
</h1>

<p align="center">
    A tower-sessions backend for AWS DynamoDB
</p>

## Overview

This create provides an AWS DynamoDB back-end for the tower-session session management crate, using the Rust AWS-SDK.


### Examples
Counter example (using dynamodb-local via docker)

```
docker compose up dynamodb_store
cargo run --example counter
```


### Building

rustfmt
```
cargo fmt --all --check
```

clippy
```
cargo clippy --workspace --all-targets --all-features -- -D warnings
```

rustdoc
```
RUSTDOCFLAGS="-D rustdoc::broken-intra-doc-links" cargo doc --all-features --no-deps
```

