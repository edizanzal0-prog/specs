---
id: rust
layer: language
extends: []
---

# Rust

## Purpose

Rust guarantees memory safety without garbage collection through its ownership system, but those guarantees only hold when the code respects the borrowing rules the compiler enforces and when `unsafe` blocks are used deliberately with documented invariants. Beyond safety, Rust's toolchain (`cargo`, `rustfmt`, `clippy`, `rust-analyzer`) provides opinionated defaults that eliminate formatting debates and catch real defects before CI. This spec pins the language version, edition, formatting, linting, idiomatic patterns, and the boundary between safe and `unsafe` code so every contributor can rely on consistent semantics, reproducible builds, and a compiler that fails fast — not silently.

## References

### Language & Documentation

- **external** `https://www.rust-lang.org/` — Rust language home
- **external** `https://doc.rust-lang.org/book/` — The Rust Programming Language Book
- **external** `https://doc.rust-lang.org/nomicon/` — The Rustonomicon (unsafe Rust)
- **external** `https://doc.rust-lang.org/reference/` — The Rust Reference
- **external** `https://doc.rust-lang.org/cargo/` — Cargo Book
- **external** `https://rust-lang.github.io/api-guidelines/` — Rust API Guidelines

### Standard Tooling

- **external** `https://doc.rust-lang.org/rustfmt/` — rustfmt reference
- **external** `https://doc.rust-lang.org/clippy/` — Clippy linter
- **external** `https://rust-lang.github.io/rust-clippy/master/index.html` — Clippy lint list
- **external** `https://doc.rust-lang.org/std/result/` — Result type documentation
- **external** `https://doc.rust-lang.org/std/option/` — Option type documentation

### Testing Tools

- **external** `https://nexte.st/` — cargo-nextest (faster test runner)
- **external** `https://github.com/taiki-e/cargo-llvm-cov` — LLVM coverage
- **external** `https://github.com/xd009642/tarpaulin` — cargo-tarpaulin (coverage)
- **external** `https://github.com/proptest-rs/proptest` — proptest (property-based testing)
- **external** `https://github.com/BurntSushi/quickcheck` — quickcheck (property-based testing)
- **external** `https://rust-fuzz.github.io/book/` — cargo-fuzz (fuzz testing)
- **external** `https://github.com/tokio-rs/loom` — loom (concurrency testing)
- **external** `https://bheisler.github.io/criterion.rs/book/` — criterion (benchmarking)

### Quality Tools

- **external** `https://github.com/obi1kenobi/cargo-semver-checks` — API compatibility checks
- **external** `https://pre-commit.com/` — pre-commit framework

### Security Tools

- **external** `https://github.com/rustsec/rustsec-cargo-audit` — cargo-audit (vulnerability advisories)
- **external** `https://github.com/EmbarkStudios/cargo-deny` — cargo-deny (licenses, bans, audits)
- **external** `https://github.com/mozilla/cargo-vet` — cargo-vet (supply-chain review)
- **external** `https://github.com/rust-secure-code/cargo-auditable` — cargo-auditable (dependency embedding)
- **external** `https://github.com/rust-secure-code/cargo-geiger` — cargo-geiger (unsafe code detection)
- **external** `https://github.com/rust-lang/miri` — miri (undefined behavior detection)
- **external** `https://semgrep.dev/` — semgrep (SAST)
- **external** `https://codeql.github.com/` — CodeQL (advanced scanning)
- **external** `https://github.com/gitleaks/gitleaks` — gitleaks (secret scanning)
- **external** `https://github.com/google/osv-scanner` — osv-scanner (vulnerability scanning)
- **external** `https://github.com/anchore/syft` — syft (SBOM generation)
- **external** `https://github.com/anchore/grype` — grype (vulnerability scanning)

### Health Tools

- **external** `https://github.com/kbknapp/cargo-outdated` — cargo-outdated (dependency drift)
- **external** `https://github.com/bnjbvr/cargo-machete` — cargo-machete (unused dependencies)
- **external** `https://github.com/RazrFalcon/cargo-bloat` — cargo-bloat (binary size)
- **external** `https://github.com/flamegraph-rs/flamegraph` — cargo-flamegraph (profiling)

## Tooling

### Testing

Tools and commands that prove behavior works.

| Tool / command     | Purpose                              |
| ------------------ | ------------------------------------ |
| `cargo test`       | Built-in Rust test runner            |
| `cargo nextest`    | Faster, CI-friendly test runner      |
| `cargo test --doc` | Runs documentation examples as tests |
| `cargo llvm-cov`   | Test coverage using LLVM coverage    |
| `cargo tarpaulin`  | Alternative coverage tool            |
| `proptest`         | Property-based testing               |
| `quickcheck`       | Property-based testing alternative    |
| `cargo fuzz`       | Fuzz testing                         |
| `loom`             | Concurrency testing                  |
| `criterion`        | Benchmark testing                    |

**Common testing commands:**

```bash
cargo test
cargo test --workspace --all-targets --all-features
cargo nextest run --workspace --all-features
cargo test --doc
cargo llvm-cov nextest --workspace --all-features
cargo fuzz run fuzz_target_1
cargo test --test loom_tests
cargo bench
```

### Quality

Tools and commands that enforce code correctness, style, and maintainability.

| Tool / command               | Purpose                                              |
| ---------------------------- | ---------------------------------------------------- |
| `cargo check`                | Fast compile/type/borrow-checking without full build |
| `cargo build`                | Full compilation                                     |
| `cargo build --release`      | Optimized release build                              |
| `cargo fmt`                  | Standard Rust formatter                              |
| `cargo clippy`               | Rust linter                                          |
| `rustfmt`                    | Formatting component used by `cargo fmt`             |
| `clippy`                     | Linting component used by `cargo clippy`             |
| `cargo doc`                  | Documentation generation                             |
| `RUSTDOCFLAGS="-D warnings"` | Treats documentation warnings as errors              |
| `cargo semver-checks`        | API compatibility checks for libraries               |
| `cargo package`              | Validates package contents                           |
| `cargo publish --dry-run`    | Validates crate publishing workflow                  |
| `pre-commit`                 | Runs quality checks before commits                   |

**Common quality commands:**

```bash
cargo check --workspace --all-targets --all-features
cargo build --release
cargo fmt --check
cargo clippy --workspace --all-targets --all-features -- -D warnings
RUSTDOCFLAGS="-D warnings" cargo doc --workspace --all-features --no-deps
cargo semver-checks
cargo package
cargo publish --dry-run
```

### Security

Tools and commands that identify vulnerabilities, supply-chain risk, unsafe code, and undefined behavior.

| Tool / command                     | Purpose                                                                                     |
| ---------------------------------- | ------------------------------------------------------------------------------------------- |
| `cargo audit`                      | Checks `Cargo.lock` against RustSec vulnerability advisories                                |
| `cargo deny`                       | Checks vulnerabilities, licenses, duplicate versions, banned crates, and dependency sources |
| `cargo vet`                        | Supply-chain review/audit workflow                                                          |
| `cargo auditable`                  | Embeds dependency metadata into binaries                                                    |
| `cargo geiger`                     | Reports usage of `unsafe` code                                                              |
| `cargo +nightly miri test`         | Detects some undefined behavior in Rust programs                                            |
| `#![forbid(unsafe_code)]`          | Prevents unsafe code in a crate                                                             |
| `#![deny(unsafe_op_in_unsafe_fn)]` | Forces explicit unsafe blocks inside unsafe functions                                       |
| `rg "unsafe"`                      | Simple unsafe-code search                                                                   |
| `semgrep`                          | Static application security testing                                                         |
| `codeql`                           | Advanced code scanning                                                                      |
| `gitleaks`                         | Secret scanning                                                                             |
| `osv-scanner`                      | Vulnerability scanning via OSV database                                                     |
| `syft`                             | SBOM generation                                                                             |
| `grype`                            | Vulnerability scanning from SBOM/container artifacts                                        |

**Common security commands:**

```bash
cargo audit
cargo deny check
cargo vet
cargo auditable build --release
cargo geiger
cargo +nightly miri test
rg "unsafe"
semgrep scan
codeql database analyze
gitleaks detect
osv-scanner .
syft .
grype .
```

### Health

Tools and commands that detect dependency drift, dead dependencies, bloat, performance issues, architecture problems, and long-term maintainability risks.

| Tool / command              | Purpose                                    |
| --------------------------- | ------------------------------------------ |
| `cargo tree`                | Shows dependency graph                     |
| `cargo tree --duplicates`   | Finds duplicate crate versions             |
| `cargo tree -i crate_name`  | Explains why a dependency is included      |
| `cargo outdated`            | Finds outdated dependencies                |
| `cargo machete`             | Finds unused dependencies                  |
| `cargo deny check licenses` | License health                             |
| `cargo deny check bans`     | Banned crate / duplicate dependency policy |
| `cargo bloat`               | Binary size analysis                       |
| `cargo flamegraph`          | Runtime profiling visualization            |
| `criterion`                 | Benchmark tracking                         |
| `cargo bench`               | Benchmark execution                        |
| `strip`                     | Removes symbols from release binaries      |
| `cargo llvm-cov`            | Coverage health metric                     |
| `cargo doc --no-deps`       | Documentation health                       |
| `cargo semver-checks`       | Public API stability health for libraries  |

**Common health commands:**

```bash
cargo tree
cargo tree --duplicates
cargo tree -i crate_name
cargo outdated
cargo machete
cargo deny check licenses
cargo deny check bans
cargo bloat --release
cargo flamegraph
cargo bench
cargo llvm-cov nextest --workspace --all-features
```

### Baseline Checks

The minimal set of checks for a healthy Rust project:

| Category | Baseline                                                                                             |
| -------- | ---------------------------------------------------------------------------------------------------- |
| Testing  | `cargo nextest run --workspace --all-features`, `cargo llvm-cov nextest`, `cargo test --doc`          |
| Quality  | `cargo fmt --check`, `cargo clippy -D warnings`, `cargo check --workspace`, `cargo doc --no-deps`    |
| Security | `cargo audit`, `cargo deny check`, `cargo +nightly miri test`, `cargo geiger`                        |
| Health   | `cargo machete`, `cargo outdated`, `cargo tree --duplicates`, `cargo bloat --release`                |

**Full check script:**

```bash
# Quality
cargo fmt --check
cargo clippy --workspace --all-targets --all-features -- -D warnings
cargo check --workspace --all-targets --all-features

# Testing
cargo nextest run --workspace --all-features
cargo test --doc
cargo llvm-cov nextest --workspace --all-features

# Security
cargo audit
cargo deny check
cargo +nightly miri test
cargo geiger

# Health
cargo machete
cargo outdated
cargo tree --duplicates
cargo bloat --release
```

## Rules

### Language & Edition

1. Declare the Rust edition in `Cargo.toml` (e.g., `edition = "2021"`); keep it within the two most recent stable editions supported by the Rust team.
2. Declare the Minimum Supported Rust Version (MSRV) in `Cargo.toml` when the crate is a library or application intended for broader use; run CI against the MSRV to prevent accidental breaking changes.
3. Commit `Cargo.lock` for binaries and applications; exclude it from version control for libraries published to crates.io.

### Formatting & Linting

4. Run `cargo fmt --check` in CI; do not merge pull requests with unformatted code.
5. Run `cargo clippy --all-targets --all-features -- -D warnings` in CI; treat every Clippy warning as a build failure unless explicitly allowed with a documented `#[allow(clippy::...)]` annotation.
6. Use `#![warn(rustdoc::broken_intra_doc_links)]` to catch broken doc links; do not allow invalid intra-doc links in published crates.

### Error Handling

7. Use `Result<T, E>` for operations that can fail; do not use `panic!` or `unwrap()` for expected failure modes in library code.
8. Use `Option<T>` for values that may be absent; do not use sentinel values, null pointers, or magic constants to represent "no value."
9. Handle errors explicitly at the call site or propagate them with `?`; do not silence errors with `.ok()` or `.unwrap_or_default()` without a comment explaining why the error is expected and acceptable.
10. Reserve `panic!` for truly unrecoverable programmer errors (index out of bounds, invariant violation); do not panic on expected runtime failures (file not found, network timeout).
11. Do not use `unwrap()` or `expect()` in library code for errors that callers can meaningfully handle; return a `Result` instead.
12. Use `thiserror` or `anyhow` for error types; do not define custom error enums with `impl Display` by hand unless the crate has zero dependencies as a design constraint.

### Unsafe Code

13. Document every `unsafe` block with a `// SAFETY:` comment explaining why the code is sound, what invariants it relies on, and why the surrounding safe API preserves safety.
14. Prefer safe abstractions over raw `unsafe`; encapsulate `unsafe` blocks in modules with safe public APIs.
15. Use `#![forbid(unsafe_code)]` for crates that must not contain unsafe; use `#![deny(unsafe_op_in_unsafe_fn)]` to require explicit `unsafe` blocks inside `unsafe fn`.
16. Run `cargo geiger` in CI to track and audit `unsafe` usage; do not add new `unsafe` blocks without justification.

### Naming Conventions

17. Name modules, functions, and variables in `snake_case`; name types, traits, and enums in `PascalCase`; name constants in `UPPER_SNAKE_CASE`. (refs: https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html)
18. Use `_` prefix for unused bindings (e.g., `_result`) to signal intentional discard; do not use `_` alone for side-effect expressions that may panic.

### Ownership & Types

19. Prefer borrowing over owning (`&str` over `String`, `&[T]` over `Vec<T>`) in function signatures; accept owned types only when the caller must transfer ownership or when cloning would be expensive.
20. Use `#[derive]` for `Clone`, `Debug`, `PartialEq`, and `Eq` on data types where the derived implementation is correct; do not implement these traits by hand unless custom behavior is required.
21. Implement `From` and `Into` for lossless conversions; implement `TryFrom` and `TryInto` for fallible conversions.
22. Use `Deref` and `DerefMut` only for smart pointer types; do not implement `Deref` as a way to emulate inheritance.
23. Use `#[must_use]` on types and functions where ignoring the return value is likely a bug (e.g., `Result`, `Option`, `Iterator`).

### Testing

24. Place unit tests in the same file as the code under test using `#[cfg(test)] mod tests { ... }`; place integration tests under `tests/` directory.
25. Use `#[test]` for unit tests and `#[tokio::test]` for async tests; do not run async tests without a runtime.
26. Use `assert!`, `assert_eq!`, and `assert_ne!` for assertions in tests; use `debug_assert!` family for assertions that should compile out in release.
27. Run `cargo nextest run --workspace --all-features` in CI for fast, parallel test execution; use `cargo test` when nextest is not available.
28. Run `cargo test --doc` to verify all documentation examples compile and pass; do not allow broken doc tests in published crates.
29. Gate on line and branch coverage with `cargo llvm-cov`; target 80% coverage floor for new modules and pair coverage with property-based testing for pure logic.

### Documentation

30. Document every public item (`pub`) with a doc comment (`///` or `//!`) explaining what it does, what it returns, and what errors it can raise.
31. Include a `README.md` at the crate root with a usage example; do not leave the crate description empty in `Cargo.toml`.
32. Run `cargo doc --no-deps --open` to generate and review documentation before publishing; ensure doc links resolve and examples compile.
33. Use `RUSTDOCFLAGS="-D warnings" cargo doc --workspace --all-features --no-deps` in CI; treat doc warnings as build failures.

### Dependencies

34. Use `cargo add <crate>` to add dependencies; pin versions in `Cargo.toml` with explicit version numbers (avoid `*` wildcards).
35. Run `cargo outdated` or `cargo update --dry-run` periodically to audit dependency freshness; do not let dependencies drift unattended.
36. Run `cargo machete` to find and remove unused dependencies; do not leave crates in `Cargo.toml` that are not referenced in code.
37. Run `cargo tree --duplicates` to detect duplicate crate versions; resolve version conflicts by updating or pinning consistent versions.
38. Run `cargo audit` in CI to check `Cargo.lock` against RustSec advisories; treat vulnerability findings as build failures.
39. Run `cargo deny check` to enforce license compliance, ban known-problematic crates, and audit dependency sources; do not merge PRs that violate policy.

### API Stability

40. Run `cargo semver-checks` before publishing library updates; do not publish breaking changes without incrementing the major version.
41. Use `cargo package` and `cargo publish --dry-run` to validate package contents before publishing; fix warnings before release.

### Control Flow

42. Prefer `match` over `if let` when all variants must be handled explicitly; use `if let` only when the unmatched variants are intentionally ignored.

### Performance

43. Run `cargo bloat --release` to analyze binary size; investigate significant size regressions before release.
44. Use `criterion` for benchmarking performance-critical code; do not rely on `std::time::Instant` for benchmark measurements.
45. Strip symbols from release binaries with `strip` or `cargo strip`; do not ship debug symbols in production builds unless explicitly required.