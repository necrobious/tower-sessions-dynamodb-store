<h1 align="center">
    tower-sessions-dynamodb-store
</h1>

<p align="center">
    A tower-sessions backend for AWS DynamoDB
</p>

## Overview

This create provides an AWS DynamoDB back-end for the tower-session session management crate, using the Rust AWS-SDK.

## Usage

Setting a `DynamoDBStore` requires two parameters:
 - An `aws_sdk_dynamodb::Client` instance, for connecting to the AWS DynamoDB service.
 - A `DynamoDBStoreProps` instance, to tell the Session Store where in dynamo to find/save things.

### `aws_sdk_dynamodb::Client`
There are many possibilities for Setting up a `aws_sdk_dynamodb::Client` but all will require an instance of
`aws_config::Config`. A most basic example of how to obtain an `aws_sdk_dynamodb::Client` instance can be found
from the [aws_config rustdocs](https://docs.rs/aws-config/latest/aws_config/) along with many other examples:

```rust
use aws_config;
use aws_sdk_dynamodb;
let config = aws_config::load_defaults(aws_config::BehaviorVersion::v2023_11_09()).await;
let client = aws_sdk_dynamodb::Client::new(&config);
```

### `DynamoDBStoreProps`
`DynamoDBStoreProps` implements the Default trait, so a minimal instance that makes several assumptions about the
DynamoDB table and property names can be obtained using `DynamoDBStoreProps::default()`; however it is far more likely you
will need to overwrite one or more of these settings; a more complex example:

```rust
let store_props = DynamoDBStoreProps {
    table_name: "MyTowerSessions".to_string(),
    sort_key: Some(DynamoDBStoreKey {
        name: "sort_key".to_string(),
        prefix: Some("TOWER_SESSIONS::".to_string()),
        suffix: None,
    }),
    ..Default::default()
};
```

Will create a store props instance that:
 - uses the defined DynamoDB Table named `MyTowerSession` .
 - uses the default `partition_key` name of `session_id`, and prepends the prefix of `SESSIONS::TOWER::` to all tower session IDs before inserting into `session_id` partition key property.
 - uses the defined `sort_key` with the same property name of `sort_key`, and prepends the prefix of `TOWER_SESSIONS::` to the session_id before inserting into `sort_key` range key property.
 - uses the default `expirey_name` property name of `expire_at` to hold the unix timestamp for when the tower-sessions session will expire.
 - uses the default `data_name` property name of `data` to hold the binary serialization of the tower-sessions key-value session data.

All items can be overwritten safely, with the only current assumption of your DynamoDB table is that the Partition Key and Range Key properties are both of type S (String).

### `DynamoDBStore`

Once you have instances of `aws_sdk_dynamodb::Client` and `DynamoDBStoreProps` created, a `DynamoDBStore` instance
can be configured and passed to the tower-sessions session manager:

```rust
use axum::{response::IntoResponse, routing::get, Router};
use time::Duration;
use tower_sessions::{Expiry, SessionManagerLayer};
use tower_sessions_dynamodb_store::DynamoDBStore;

let session_store = DynamoDBStore::new(client, store_props);
let session_layer = SessionManagerLayer::new(session_store)
    .with_secure(false) // only for local testing !!
    .with_expiry(Expiry::OnInactivity(Duration::seconds(10)));

let app = Router::new().route("/", get(handler)).layer(session_layer);
```

You can find a complete [example](examples/counter.rs) in the [example directory](examples).


## Examples

### Counter
Counter example (using [dynamodb-local](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/DynamoDBLocal.DownloadingAndRunning.html) via docker)

```
docker compose up dynamodb_store
cargo run --example counter
```

Once dynamodb-local is running open a browser to http://localhost:3000/ if working correctly, the counter should increment,
and a cookie should be visible in the browsers cookie store.

### CloudFormation

There is an example AWS CloudFormation template for creating an AWS DynamoDB Table configured to work using the `DynamoDBStoreProps::default()` settings.

```bash
aws cloudformation create-stack --stack-name ExampleTowerSessionsStack --template-body file:///$PWD/examples/example-table-cfn.yml
```

Additionally this template illustrates the use of the `TimeToLiveSpecification` setting on the `expire_at` property,
configuring DynamoDB to remove expired sessions automatically.

## Building

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

