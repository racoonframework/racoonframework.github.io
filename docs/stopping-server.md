# Stopping server

Racoon uses Mutex and CondVar to hold the shutdown lock. 
To release the shutdown lock, clone the mutex from `Server` instance.

## Example

```rust
let mut server = Server::bind("127.0.0.1:8080");
let lock = server.shutdown_lock();

// Automatically shutdown after 1 minutes
tokio::spawn(async move {
    let (_, condvar) = &*lock;
    condvar.notify_all(); // Releases shutdown lock
});

_ = server.run().await;
```
