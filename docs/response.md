# Response

Racoon supports `text/html` and `application/json` with `HttpResponse` and
`JsonResponse` structs.


## HTTP Response

```rust
use racoon::core::request::Request;
use racoon::core::response::{HttpResponse, Response};

async fn hello_world(request: Request) -> Response {
    HttpResponse::ok().body("Hello World")
}
```

## JSON Response

To use Json Response, you need to add `serde_json` crate.

```rust
use racoon::core::request::Request;
use racoon::core::request::Request;
use racoon::core::response::{JsonResponse, Response};

async fn hello_world(request: Request) -> Response {
    JsonResponse::ok().body(json!({
        "content": "Hello World"
    }))
}
```

## Custom Response

You can write your own custom Response type by implementing `AbstractResponse` trait.

```rust
struct CustomResponse;

impl AbstractResponse for CustomResponse {
    fn status(&self) -> (u32, String) {
        todo!()
    }

    fn serve_default(&mut self) -> bool {
        todo!()
    }

    fn get_headers(&mut self) -> &mut Headers {
        todo!()
    }

    fn get_body(&mut self) -> &mut Vec<u8> {
        todo!()
    }

    fn should_close(&mut self) -> bool {
        todo!()
    }
}
```
