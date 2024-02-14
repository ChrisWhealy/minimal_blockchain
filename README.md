# Minimal Blockchain Demo

This is a bare-bones implementation of a blockchain app in Rust.
It is based on the coding in this [blog from LogRocket](https://blog.logrocket.com/how-to-build-a-blockchain-in-rust/).

## Fix

Certain coding changes were necessary in order get the coding shown in the blog to compile.
Notably, version 1.32 of `libp2p-noise` does not compile due to an error.

```bash
   Compiling libp2p-relay v0.3.0
   Compiling libp2p-ping v0.30.0
error[E0282]: type annotations needed
   --> /Users/chris/.cargo/registry/src/index.crates.io-6f17d22bba15001f/libp2p-noise-0.32.0/src/protocol/x25519.rs:221:45
    |
221 |         curve25519_sk.copy_from_slice(&hash.as_ref()[..32]);
    |                                             ^^^^^^
    |
help: try using a fully qualified path to specify the expected types
    |
221 |         curve25519_sk.copy_from_slice(&<GenericArray<u8, UInt<UInt<UInt<UInt<UInt<UInt<UInt<UTerm, B1>, B0>, B0>, B0>, B0>, B0>, B0>> as AsRef<T>>::as_ref(&hash)[..32]);
    |                                        +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++    ~

For more information about this error, try `rustc --explain E0282`.
error: could not compile `libp2p-noise` (lib) due to 1 previous error
```

Updating to the latest version of `libp2pnoise` introduces different errors; however, changing the code in the local copy of `libp2p-noise-0.32.0/src/protocol/x25519.rs` on line `221` does appear to fix the problem:

Change

```rust
curve25519_sk.copy_from_slice(&hash.as_ref()[..32]);
```

to that shown here <https://docs.rs/libp2p-noise/0.35.0/src/libp2p_noise/protocol/x25519.rs.html#222>

```rust
curve25519_sk.copy_from_slice(&hash[..32]);
```

## Execution

After cloning this repo into a local directory, open at least two different terminal windows into this directory.

In the first, enter `RUST_LOG=info cargo run`

When it has compiled, the first blockchain node will start.

In the second (and subsequent) terminal window, enter the same command and the nodes will start to communicate with each other.

### Commands

| Command                 | Action
|-------------------------|---|
| `ls b`                  | List all blocks in the chain
| `ls c`                  | List block zero (the "Genesis" block)
| `ls p`                  | List known peers
| `create b <some value>` | Create a block containing `<some value>` 
