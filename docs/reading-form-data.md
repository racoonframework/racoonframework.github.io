# Handling forms

Racoon currently supports `multipart/form-data` and `application/x-www-form-urlencoded`.
If you require to read the body of another content type you can use `request.stream` to read the 
request body manually.


## Basic Usage

The `FormData` and `File` are the HashMap instances.

```rust
use racoon::core::path::{Path, View};
use racoon::core::request::Request;
use racoon::core::response::{HttpResponse, Response};
use racoon::core::response::status::ResponseStatus;
use racoon::core::server::Server;
use racoon::core::forms::FileField;
use racoon::core::shortcuts::SingleText;

use racoon::{view, wrap_view};


async fn form(mut request: Request) -> Response {
    if request.method == "POST" {
        // Parses request body
        let (mut form_data, mut files) = request.parse().await;
        println!("Name: {:?}", form_data.value("name"));

        let file = files.value("file");
        println!("File: {:?}", file);
        return HttpResponse::ok().body("Uploaded");
    }

    HttpResponse::bad_request().body("Failed")
}

#[tokio::main]
async fn main() {
    let paths = vec![
        Path::new("/form/", view!(form))
    ];

    let _ = Server::bind("127.0.0.1:8080")
        .urls(paths)
        .run().await;
}
```
