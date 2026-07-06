---
id: rust
layer: language
extends: []
---

# Rust

## Purpose

Rust guarantees memory safety without garbage collection through its ownership system, but those guarantees only hold when the code respects the borrowing rules the compiler enforces and when `unsafe` blocks are used deliberately with documented invariants. Beyond safety, Rust's toolchain (`cargo`, `rustfmt`, `clippy`, `rust-analyzer`) provides opinionated defaults that eliminate formatting debates and catch real defects before CI. This spec pins the language version, edition, formatting, linting, idiomatic patterns, and the boundary between safe and `unsafe` code so every contributor can rely on consistent semantics, reproducible builds, and a compiler that fails fast — not silently.

## References

- **external** `https://www.rust-lang.org/` — Rust language home
- **external** `https://doc.rust-lang.org/book/` — The Rust Programming Language Book
- **external** `https://doc.rust-lang.org/nomicon/` — The Rustonomicon (unsafe Rust)
- **external** `https://doc.rust-lang.org/reference/` — The Rust Reference
- **external** `https://doc.rust-lang.org/cargo/` — Cargo Book
- **external** `https://doc.rust-lang.org/rustfmt/` — rustfmt reference
- **external** `https://doc.rust-lang.org/clippy/` — Clippy linter
- **external** `https://rust-lang.github.io/api-guidelines/` — Rust API Guidelines
- **external** `https://rust-lang.github.io/rust-clippy/master/index.html` — Clippy lint list
- **external** `https://doc.rust-lang.org/std/result/` — Result type documentation
- **external** `https://doc.rust-lang.org/std/option/` — Option type documentation

## Rules

1. Declare the Rust edition in `Cargo.toml` (e.g., `edition = "2021"`); keep it within the two most recent stable editions supported by the Rust team.
2. Declare the Minimum Supported Rust Version (MSRV) in `Cargo.toml` when the crate is a library or application intended for broader use; run CI against the MSRV to prevent accidental breaking changes.
3. Commit `Cargo.lock` for binaries and applications; exclude it from version control for libraries published to crates.io.
4. Run `cargo fmt --check` in CI; do not merge pull requests with unformatted code.
5. Run `cargo clippy --all-targets --all-features -- -D warnings` in CI; treat every Clippy warning as a build failure unless explicitly allowed with a documented `#[allow(clippy::...)]` annotation.
6. Use `Result<T, E>` for operations that can fail; do not use `panic!` or `unwrap()` for expected failure modes in library code.
7. Use `Option<T>` for values that may be absent; do not use sentinel values, null pointers, or magic constants to represent "no value."
8. Handle errors explicitly at the call site or propagate them with `?`; do not silence errors with `.ok()` or `.unwrap_or_default()` without a comment explaining why the error is expected and acceptable.
9. Reserve `panic!` for truly unrecoverable programmer errors (index out of bounds, invariant violation); do not panic on expected runtime failures (file not found, network timeout).
10. Document every `unsafe` block with a `// SAFETY:` comment explaining why the code is sound, what invariants it relies on, and why the surrounding safe API preserves safety.
11. Prefer safe abstractions over raw `unsafe`; encapsulate `unsafe` blocks in modules with safe public APIs.
12. Do not use `unwrap()` or `expect()` in library code for errors that callers can meaningfully handle; return a `Result` instead.
13. Use `thiserror` or `anyhow` for error types; do not define custom error enums with `impl Display` by hand unless the crate has zero dependencies as a design constraint.
14. Name modules, functions, and variables in `snake_case`; name types, traits, and enums in `PascalCase`; name constants in `UPPER_SNAKE_CASE`. (refs: https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html)
15. Use `_` prefix for unused bindings (e.g., `_result`) to signal intentional discard; do not use `_` alone for side-effect expressions that may panic.
16. Prefer borrowing over owning (`&str` over `String`, `&[T]` over `Vec<T>`) in function signatures; accept owned types only when the caller must transfer ownership or when cloning would be expensive.
17. Use `#[derive]` for `Clone`, `Debug`, `PartialEq`, and `Eq` on data types where the derived implementation is correct; do not implement these traits by hand unless custom behavior is required.
18. Implement `From` and `Into` for lossless conversions; implement `TryFrom` and `TryInto` for fallible conversions.
19. Use `Deref` and `DerefMut` only for smart pointer types; do not implement `Deref` as a way to emulate inheritance.
20. Place unit tests in the same file as the code under test using `#[cfg(test)] mod tests { ... }`; place integration tests under `tests/` directory.
21. Use `#[test]` for unit tests and `#[tokio::test]` for async tests; do not run async tests without a runtime.
22. Use `assert!`, `assert_eq!`, and `assert_ne!` for assertions in tests; use `debug_assert!` family for assertions that should compile out in release.
23. Document every public item (`pub`) with a doc comment (`///` or `//!`) explaining what it does, what it returns, and what errors it can raise.
24. Include a `README.md` at the crate root with a usage example; do not leave the crate description empty in `Cargo.toml`.
25. Use `cargo add <crate>` to add dependencies; pin versions in `Cargo.toml` with explicit version numbers (avoid `*` wildcards).
26. Run `cargo outdated` or `cargo update --dry-run` periodically to audit dependency freshness; do not let dependencies drift unattended.
27. Audit third-party crates for maintenance status, license, and security advisories (`cargo audit`); do not add unmaintained crates as dependencies.
28. Use `#[must_use]` on types and functions where ignoring the return value is likely a bug (e.g., `Result`, `Option`, `Iterator`).
29. Prefer `match` over `if let` when all variants must be handled explicitly; use `if let` only when the unmatched variants are intentionally ignored.
30. Use `cargo doc --no-deps --open` to generate and review documentation; ensure doc links resolve and examples compile with `#![warn(rustdoc::broken_intra_doc_links)]`.