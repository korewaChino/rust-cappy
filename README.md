# rust-cappy

Local deltas on top of [rust-skills](https://github.com/leonardomso/rust-skills):
the catalog rules that don't apply to this setup, and the conventions the catalog
doesn't cover.

## Install

rust-cappy is an addendum, not a replacement — it cites rule names that only
exist in rust-skills, so install the catalog first:

```bash
npx skills add leonardomso/rust-skills -g
npx skills add korewaChino/rust-cappy -g
```

Read the individual rust-skills rule file when one applies; rust-cappy only
records where this machine diverges from the catalog or the catalog is silent.
A project's own `AGENTS.md`, `Cargo.toml`, `rustfmt.toml` and `clippy.toml`
outrank both.

The skill lives at `skills/rust-cappy/SKILL.md`, which is where the
[`skills` CLI](https://github.com/vercel-labs/skills) looks for skills in a
repository.

## License

[Unlicense](UNLICENSE) — public domain.
