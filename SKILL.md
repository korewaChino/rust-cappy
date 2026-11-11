---
name: rust-cappy
description: Local deltas and overrides to the generic rust-skills catalog for this machine's Rust projects — which rules to ignore (error docs, tokio::fs, abort/strip profiles, mocks), and the conventions the catalog lacks (eyre wrap_err, module naming, comment hygiene, programmatic I/O, test names, fmt/clippy/test loop). Use together with rust-skills, not instead of it.
metadata:
  requires:
    skill: rust-skills
    source: leonardomso/rust-skills
---

# Local Rust deltas

Requires [rust-skills](https://github.com/leonardomso/rust-skills) — install it
first with `npx add-skill leonardomso/rust-skills`. Nothing in this file is
self-contained: every rule name cited below is defined in that catalog.

`rust-skills` is the rule catalog. Read the individual rule file when one
applies; this file only records where this machine's setup differs or where the
catalog is silent. The project's own `AGENTS.md`, `Cargo.toml`, `rustfmt.toml`
and `clippy.toml` outrank both.

## Ignore these catalog rules

- `err-doc-errors`, `doc-errors-section` — no `# Errors` sections here; the
  clippy config sets `missing_errors_doc = "allow"` and errors are explained in
  prose.
- `async-tokio-fs` — blocking `std::fs` is deliberate in async code. Mount,
  composefs and syscall work needs synchronous semantics, and that is most of
  the filesystem traffic.
- `async-fn-in-trait` — applies only to monomorphized traits. Traits used
  through `dyn Trait` keep `#[async_trait]` (see `HACKING.md`).
- The "Recommended Cargo.toml" profile block — no `panic = "abort"` (kameo
  actors rely on `catch_unwind`) and no `strip = true` (panic backtraces must
  stay readable).
- `name-acronym-word` — casing of acronyms is mixed and not normalized
  (`HTTPActor`, `NodeVrf`, `DhcpPool`).
- `test-mockall-mocking`, `test-mock-traits` — tests use real fixtures
  (`test_support.rs`, temp dirs, RAII guards), not mocks.
- Dependency-adding rules (`mem-smallvec`, `mem-thinvec`, `mem-compact-string`,
  `test-proptest-properties`, `test-criterion-bench`, `test-loom-concurrency`) —
  only when the crate already depends on it or the bottleneck is measured.

## Conventions the catalog lacks

**Errors.** `thiserror` in libraries; `stable-eyre`/`color-eyre` in binaries and
application code with `.wrap_err("lowercase operation")` /
`.wrap_err_with(|| format!(...))`. That is catalog `err-anyhow-app` with a
different crate, not `anyhow`.

**Layout.** Directory modules get `mod.rs`; file modules are named after the
concept they hold (`network.rs`, `agent_actor.rs`, `network_sync.rs`). Actor
components take an `_actor`/`_sync` suffix. `src/lib.rs` is a list of `pub mod`
lines. Internals are `pub(crate)`.

**Constants.** Typed, range-checked, doc comment explaining the bounds and where
they come from:

```rust
/// MTU range for network TAPs; 576 is the minimum IPv4 hosts must accept.
const MTU_RANGE: RangeInclusive<u16> = 576..=65_535;
```

**Comments.** One line maximum, only where the code cannot say it itself. Never
investigation history, rationale paragraphs, or "the old code did X". Re-read
every comment you added after editing and delete it if it restates the code.

**Programmatic I/O.** `std::fs`, `nix`/`rustix` and existing RAII guards before
spawning tools. `cmd_lib` is for genuine external binaries (`mkfs.erofs`,
`ostree`, `bootc`); `clippy.toml` disallows `cmd_lib::run_cmd`/`run_fun`. Large
scratch output goes to `/var/tmp`, not the `/tmp` tmpfs.

**Tests.** `#[cfg(test)] mod tests` at the bottom of the file, or `tests.rs`
inside a module directory. Names are snake_case behavioral sentences
(`rejects_unsupported_version_during_validation_and_deserialization`). Shared
builders are prefixed `test_`. Privileged or hardware tests are `#[ignore]`d
with the exact command to run them in a comment or the project docs.

**Verify.** `cargo fmt --all`, then `cargo clippy -p <crate> --all-targets`, then
`cargo test -p <crate>`. Leave no new warnings, and do not reformat unrelated
code.
