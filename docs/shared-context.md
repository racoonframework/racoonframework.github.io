# Shared Context

Shared context is useful when you need to access the states, instances such as database connection, 
websocket clients etc. in views.

## Example

```rust
use racoon::core::path::Path;
use racoon::core::request::Request;
use racoon::core::response::{HttpResponse, Response};
use racoon::core::response::status::ResponseStatus;
use racoon::core::server::Server;

use racoon::view;

pub struct SharedContext {
    pub max_connections: u32,
}


async fn hello_world(mut request: Request) -> Response {
    // Access shared context
    let context = request.context::<SharedContext>().unwrap();
    println!("Max connections: {}", context.max_connections);
    HttpResponse::ok().body("Hello World")
}

#[tokio::main]
async fn main() {
    let paths = vec![
        Path::new("/", view!(hello_world))
    ];

    let _ = Server::bind("127.0.0.1:8080")
        .context(SharedContext { max_connections: 10 })
        .urls(paths)
        .run().await;
}
```
