# Getting Started

## Installation

You will need `tokio` runtime to run Racoon. Run `cargo add tokio` to install tokio crate.

```
[dependencies]
racoon = { git = "https://github.com/racoonframework/racoon", version = "0.1.1" }
tokio = "1.18.5"
```

## Hello World

```rust
use racoon::core::path::Path;
use racoon::core::request::Request;
use racoon::core::response::{HttpResponse, Response};
use racoon::core::response::status::ResponseStatus;
use racoon::core::server::Server;

use racoon::view;

async fn hello_world(request: Request) -> Response {
    HttpResponse::ok().body("Hello World")
}

#[tokio::main]
async fn main() {
    let paths = vec![
        Path::new("/", view!(hello_world))
    ];

    let result = Server::bind("127.0.0.1:8080")
        .urls(paths)
        .run().await;

    println!("Failed to run server: {:?}", result);
}
```

For binding to Unix Domain Sockets, use `Server::bind_uds("/path/tmp.sock")` method.

