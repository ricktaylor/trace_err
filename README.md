# Trace_Err
A small extension to the [tracing](https://crates.io/crates/tracing) crate, which provides a single method for `core::result::Result<T, E>` and `core::option::Option<T>`.

Adds `trace_expect` to `Result`, which invoke the `tracing::error!` macro (in case of `Result::Err`) in _addition_ to unwrapping/expecting the `Result`.

Adds `trace_expect` to `Option`, which invoke the `tracing::error!` macro (in case of `Option::None`) in _addition_ to unwrapping/expecting the `Option`.

Shamelessly derived from the [Log_Err](https://crates.io/crates/log_err) crate.

Shorthand for:

```rust
use tracing::error;

fn something() -> Result<(), &'static str> {Err("there was some problem")}

let msg = "Some message";
something().map_err(|e| tracing::error!("{}: {:?}", msg, e)).expect(msg)
```

Example:

```rust
use std::fs::File;
use trace_err::*;

let mut file = File::open("foo.txt").trace_expect("Error creating file");
```
```text
# Error will be traced with the error! macro
2024-06-12T09:31:23.933299Z ERROR expect: trace-err/lib.rs:87:39: Error creating file: Os { code: 2, kind: NotFound, message: "No such file or directory" }

# Main program panic'ing with same message
thread 'main' panicked at trace-err/lib.rs:87:39:
Error creating file: Os { code: 2, kind: NotFound, message: "No such file or directory" }
```
