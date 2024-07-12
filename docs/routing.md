# Routing

Racoon uses [matchit](https://docs.rs/matchit/latest/matchit/) crate internally to handle routing and
extract path parts. 

You can access the `query_params` and `path_params` from the request instance.

## Example

You need to use `view!` macro to wrap your async views.

```rust
use racoon::core::path::Path;
use racoon::core::request::Request;
use racoon::core::response::{HttpResponse, Response};
use racoon::core::response::status::ResponseStatus;
use racoon::core::server::Server;
use racoon::core::shortcuts::SingleText;

use racoon::view;

async fn hello_world(request: Request) -> Response {
    println!("{:?}", request.query_params.value("name"));
    println!("{:?}", request.path_params.value("id"));
    HttpResponse::ok().body("Hello World")
}

#[tokio::main]
async fn main() {
    let paths = vec![
        Path::new("/", view!(hello_world)),
        Path::new("/{id}/", view!(hello_world)),
    ];

    let _ = Server::bind("127.0.0.1:8080")
        .urls(paths) // Pass urls here
        .run().await;
}
```
To know more about pattern matching, please read the [matchit](https://docs.rs/matchit/latest/matchit/) documentation.

