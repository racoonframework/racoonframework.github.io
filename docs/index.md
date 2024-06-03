# Racoon

Racoon is fast, fully customizable lightweight web framework for rust developers.

## Installation

You will need `tokio` runtime to run Racoon. Run `cargo add tokio` to install tokio crate.

```
[dependencies]
racoon = "0.1.1"
```

## Example

```rust
use racoon::core::path::Path;
use racoon::core::request::Request;
use racoon::core::response::{HttpResponse, Response};
use racoon::core::response::status::ResponseStatus;
use racoon::core::server::Server;

use racoon::view;

async fn home(request: Request) -> Response {
    HttpResponse::ok().body("Home")
}

#[tokio::main]
async fn main() {
    let paths = vec![
        Path::new("/", view!(home))
    ];

    let result = Server::bind("127.0.0.1:8080")
        .urls(paths)
        .run().await;

    println!("Failed to run server: {:?}", result);
}
```

For binding to Unix Domain Sockets, use `Server::bind_uds("/path/tmp.sock")` method.

