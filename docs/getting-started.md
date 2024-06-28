# Getting Started

## Installation

You will need `tokio` runtime to run Racoon. Run `cargo add tokio` to install tokio crate.

```toml
[dependencies]
racoon = "0.1.5"
tokio = "1.18.5"
```

## Hello World

Racoon is fairly simple, you just define the function which takes `Request` and returns the `Response`.
Once you define your function, define your routes, bind the server and you are ready to go.

This is a simple example code which returns response "Hello World".
Click on [http://127.0.0.1:8080](http://127.0.0.1:8080) to see the page.

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

For more binding options such as Unix Domain Sockets or with TLS, checkout [server binding options guide](server-binding-options.md).


Now just run your project.

```bash
cargo run --release
```
