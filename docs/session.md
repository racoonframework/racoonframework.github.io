# Session

Racoon provides interface to access session values fron the `Request` instance. By default, racoon uses `FileSessionManager` for managing and storing the session data.

`FileSessionManager` uses Sqlite database and it is lazily created at `.cache/session` when for the first time `session.set()` is invoked.

## Example
```rust
use racoon::core::path::Path;
use racoon::core::request::Request;
use racoon::core::response::status::ResponseStatus;
use racoon::core::response::{HttpResponse, Response};

use racoon::core::server::Server;
use racoon::view;

async fn home(request: Request) -> Response {
    let session = request.session;
    let name = session.get("Hello World").await;
    // Reads session value
    println!("name: {:?}", name);

    let _ = session.set("location", "Ktm").await;

    // Removes session value
    let _ = session.remove("name").await;

    // Destory session
    let _ = session.destroy().await;

    HttpResponse::ok().body("Hello World")
}
```

## Custom session manager

To create custom session manager, you need to implement `AbstractSessionManager` trait from the `racoon::core::session` module.

```rust

struct RedisSessionManager;

impl AbstractSessionManager for RedisSessionManager {
        fn set(
        &self,
        session_id: &String,
        name: &str,
        value: &str,
    ) -> SessionResult<std::io::Result<()>> {
        todo!()
    }

    fn get(&self, session_id: &String, name: &str) -> SessionResult<Option<String>> {
        todo!()
    }

    fn remove(&self, session_id: &String, name: &str) -> SessionResult<std::io::Result<()>> {
        todo!()
    }

    fn destroy(&self, session_id: &String) -> SessionResult<std::io::Result<()>> {
        todo!()
    }
}


#[tokio::main]
async fn main() {
    let paths = vec![Path::new("/", view!(home))];

    let custom_session_manager = RedisSessionManager {};
    let result = Server::bind("127.0.0.1:8080")
            .urls(paths)
            .set_session_manager(custom_session_manager) // Set your custom session manager here
            .run()
            .await;
    println("{:?}", result);
}
```
