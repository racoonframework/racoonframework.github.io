# Cookies

Racoon provides interface to access cookies from the `Request` instance. To set cookie, call `set_cookie()` method of the `Response` instance.

The `set_cookie` method sets `HttpOnly` cookie with the `path=/`.

## Example
```rust
use racoon::core::request::Request;
use racoon::core::response::status::ResponseStatus;
use racoon::core::response::{HttpResponse, Response};

use racoon::core::shortcuts::SingleText;

async fn home(request: Request) -> Response {
    // Reads cookie from request
    let cookies = request.cookies;
    println!("{:?}", cookies.value("sessionid"));

    let mut response = HttpResponse::ok().body("Hello World");
    // Sets new sessionid cookie
    response.set_cookie("sessionid", "123", Duration::from_secs(86400));

    // To remove cookie uncomment
    // response.remove_cookie("sessionid");
    response
}
```

`request.cookies` is a `HashMap` and implements `SingleText` trait.

