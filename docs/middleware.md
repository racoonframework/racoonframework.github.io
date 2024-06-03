# Middleware

The middleware can run before processing the response and after the response.

## Example

You need to use `wrap_view!` macro to wrap async middleware view.

```rust
use racoon::core::path::{Path, View};
use racoon::core::request::Request;
use racoon::core::response::{HttpResponse, Response};
use racoon::core::response::status::ResponseStatus;
use racoon::core::server::Server;

use racoon::{view, wrap_view};

async fn middleware(mut request: Request, view: Option<View>) -> Response {
    if request.path == "/old" {
        return HttpResponse::temporary_redirect().location("/new");
    }

    Path::resolve(request, view).await
}

async fn hello_world(request: Request) -> Response {
    HttpResponse::ok().body("Hello World")
}

#[tokio::main]
async fn main() {
    let paths = vec![
        Path::new("/", view!(hello_world))
    ];

    let _ = Server::bind("127.0.0.1:8080")
        .wrap(wrap_view!(middleware)) // Wrap middleware
        .urls(paths)
        .run().await;
}
```
