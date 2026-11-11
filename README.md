# rust-cappy

Local deltas on top of [rust-skills](https://github.com/leonardomso/rust-skills):
the catalog rules that don't apply to this setup, and the conventions the catalog
doesn't cover.

## Install

rust-cappy is an addendum, not a replacement — it cites rule names that only
exist in rust-skills, so install the catalog first:

```bash
npx add-skill leonardomso/rust-skills
npx add-skill korewaChino/rust-cappy
```

Read the individual rust-skills rule file when one applies; rust-cappy only
records where this machine diverges from the catalog or the catalog is silent.
A project's own `AGENTS.md`, `Cargo.toml`, `rustfmt.toml` and `clippy.toml`
outrank both.

## License

[Unlicense](UNLICENSE) — public domain.
