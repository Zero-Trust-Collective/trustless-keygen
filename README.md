# Trustless Keygen

**Don't use a cold wallet until you see this.**

Every cold wallet generator asks you to trust something you can't verify:
opaque entropy sources, bloated dependency trees, hardware RNGs you'll
never audit. One compromised link in that chain and your keys are
someone else's keys.

Trustless Keygen eliminates the entire chain.

- **Zero dependencies.** The production binary has no external crates.
  The entire codebase is ~35KB of Rust. Small enough to audit in 15 minutes.
- **Physical entropy.** You roll dice. You type the results. No
  `/dev/urandom`, no hardware RNG, no trust required. You *see* the
  randomness.
- **BIP-39 standard.** Generates a 24-word mnemonic compatible with
  MetaMask, Ledger, Trezor, and any wallet that supports BIP-39.

## How It Works

1. Grab a handful of casino-grade dice and start rolling. Discard any
   5s and 6s. Each valid roll (1-4) maps to 2 bits of entropy. You
   need 128 valid rolls to hit 256 bits.
3. The tool computes a SHA-256 checksum, appends 8 checksum bits, and
   splits the resulting 264 bits into 24 groups of 11 bits.
4. Each 11-bit value indexes into the standard BIP-39 English wordlist.
5. You get a 24-word seed phrase. Write it down. Never type it into a
   networked machine.

## Why This Exists

Most key generators have dependency trees with hundreds of packages.
One malicious update to one transitive dependency can silently
exfiltrate every key generated.

Trustless Keygen has no dependency tree. There is nothing to compromise.

The SHA-256 implementation is vendored from
[`jedisct1/rust-hmac-sha256`](https://github.com/jedisct1/rust-hmac-sha256)
at a pinned commit, included directly in the source for full auditability.
The only external crate (`hex`) is a dev-dependency used exclusively in
tests.

## Usage

```bash
cargo build --release
./target/release/trustless-keygen
```

Run on an air-gapped Raspberry Pi. No network, no attack surface.

## Audit It Yourself

That's the point. Six files, no magic:

| File | What it does |
|------|-------------|
| `main.rs` | Collects dice rolls, drives generation |
| `mnemonic.rs` | BIP-39 mnemonic from entropy + checksum |
| `sha256.rs` | Standalone SHA-256 (vendored, auditable) |
| `wordlist.rs` | Standard BIP-39 English wordlist (2048 words) |
| `macros.rs` | Bit-level conversion utilities |
| `test_mnemonic.rs` | BIP-39 test vectors |

Read every line. Verify it does what it claims. That's the security model.
