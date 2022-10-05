---
sidebar_label: Data encoding/decoding
sidebar_position: 4
---

# Data encoding/decoding

To optimize how data is sent and received over the network, Gear uses the `parity-scale-codec` - a Rust implementation of the SCALE Codec. This codec is used by the Substrate nodes' internal runtime. SCALE is a lightweight format that allows to serialize and deserialize data. Encoding (and decoding) data using SCALE makes it highly suitable for resource-constrained execution environments like blockchain runtimes and low-power/low-memory devices.

To use SCALE codec in your program, you should add it in `Cargo.toml`:

```toml
[dependencies]

// ...
codec = { package = "parity-scale-codec", version = "3.1.2", default-features = false, features = ["derive", "full"] }
```

```rust
use codec::{Decode, Encode};

#[derive(Encode, Decode)]
enum MyType {
    MyStruct { field: ... },
    ...
}
```

:::info
We only need the Encode and Decode trait when using wrapped methods from `gstd`, such as: `msg::send`, `msg::reply`, `send_for_reply` etc. 
In methods like `send_byte` or `reply_bytes` we operate with a set of bytes, so nothing needs to be decoded/encoded.
:::

[Learn more about SCALE Codec](https://github.com/paritytech/parity-scale-codec)

## `scale-info`

`scale-info` is a library to describe Rust types, intended for providing information about the structure of encodable SCALE types.

The definitions provide third party tools (e.g. a UI client) with information about how they are able to decode types agnostic of language. The interface that uses `scale-info` for Gear programs is called the `metadata` macro. It defines incoming and outgoing types for all necessary entry points and allows contracts and the client part to understand each other.

To use metadata in contract:

```toml
[dependencies]

// ...
scale-info = { version = "2.2.0", default-features = false, features = ["derive"] }
```

```
// We define all incoming and outgoing data types in advance

gstd::metadata! {
    title: "gear program",
    init:
        input: String,
    handle:
        input: MyAction,
        output: Vec<u8>,
    state:
        input: StateQuery,
        output: StateReply,
}
```

[Learn more about `scale-info`](https://github.com/paritytech/scale-info)