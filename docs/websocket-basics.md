# WebSocket

Using websocket with Racoon is easy. You need to create `WebSocket` instance, loop incoming message and return the
instance response.
To clone websocket instance for group chats, use `websocket.clone()`.

## Example

```rust
use racoon::core::path::Path;
use racoon::core::request::Request;
use racoon::core::response::Response;
use racoon::core::server::Server;
use racoon::core::websocket::{Message, Websocket};

use racoon::view;

async fn ws(request: Request) -> Response {
    let (mut websocket, connected) = Websocket::from(&request).await;
    if !connected {
        // WebSocket connection didn't success
        return websocket.response();
    }

    println!("WebSocket client connected.");

    // Receive incoming messages
    while let Some(message) = websocket.message().await {
        match message {
            Message::Text(text) => {
                println!("Message: {}", text);

                // Sends received message back
                let _ = websocket.send_text(text.as_str()).await;
            }
            _ => {}
        }
    }
    websocket.response()
}

#[tokio::main]
async fn main() {
    let paths = vec![
        Path::new("/ws/", view!(ws))
    ];

    let _ = Server::bind("127.0.0.1:8080")
        .urls(paths)
        .run().await;
}
```

You can also send directly JSON text with `websocket.send_json(&json!({"content": "123"})).await`.
For this, you will need to add `serde_json` crate in `Cargo.toml`.
