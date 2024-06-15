# Server binding options

Racoon provides multiple ways to bind server which allows you to have low level controls.

## Binding to host port number

```rust
#[tokio::main]
async fn main() {
    let result = Server::bind("127.0.0.1:8080").urls(...).run().await;
    println!("Failed to run server: {:?}", result);
}
```

## Binding to Unix Domain Socket

```rust
#[tokio::main]
async fn main() {
    let result = Server::bind_uds("tmp.sock").urls.run().await;
    println!("Failed to run server: {:?}", result);
}
```

## Binding to TLS

```rust
#[tokio::main]
async fn main() {
    let result = Server::bind_tls("0.0.0.0:443", "../cert.pem", "../key.pem")
        .unwrap()
        .urls(paths)
        .run()
        .await;
    println!("Failed to run server: {:?}", result);
}
```

## Custom binding to TLS
```rust
use racoon::core::server::utils;

#[tokio::main]
async fn main() {
    let tls_acceptor = utils::tls_acceptor_from_path("../cert.pem", "../key.pem").unwrap();

    let tcp_listener = TcpListener::bind("0.0.0.0:443").await.unwrap();
    let result = Server::bind_tls_custom(tcp_listener, tls_acceptor)
        .urls(...)
        .run()
        .await;
    println!("Failed to run server: {:?}", result);
}
```

## Binding from TcpListener

```rust
#[tokio::main]
async fn main() {
    let tcp_listener = TcpListener::bind("0.0.0.0:443").await.unwrap();
    let result = Server::from_tcp_listener(tcp_listener)
            .urls(...)
            .run()
            .await;
    println!("Failed to run server: {:?}", result);
```

## Binding from UnixListener

```rust
#[tokio::main]
async fn main() {
    let unix_listener = UnixListener::bind("tmp.sock").unwrap();
    let result = Server::from_unix_listener(unix_listener)
            .urls(paths)
            .run()
            .await;
    println!("Failed to run server: {:?}", result);
}
```

