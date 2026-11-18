# Pending changes to the copied standards

Every repo copies `code-standards/README.md` and the topic files it declares, and a change to one turns every repo
that copies it red until it resyncs. So a decided change to that text waits here, one line each with its reason, and
the list ships together: one PR that makes every change and empties the list, then one resync round across the
repos.

What isn't copied ships on its own and doesn't go here: `sync.sh`, the workflows, `PRINCIPLES.md`, `ruff.toml`,
`.golangci.yml`, `.ansible-lint`, the examples and the org profile.

## Go standard: deviating from the current release needs an ADR

`code-standards/go.md` says `go.mod`'s `go` line names the current release. Some repos have to build on a host pinned to an older toolchain, for example one where `GOTOOLCHAIN=local` refuses a newer `go` line. Allow that, but only with an ADR in the repo that names the version, why it's needed, and what would move it forward. The `go` line stays an exact version either way, per Versions and pins.

Prompted by prometheus-tsdb-reader, which pins `go 1.26.10` for its build host (its ADR-005).

## Versions and pins: cut to the rules repos follow

Worse is better applies to the standards too. Three repos list the release rules as deviations (hearsay, ideamachine, lattice: manifest at `0.0.0`, no CI job checking a tag against it) and infra lists the OS packages row twice. Replace the section's bullets and table with:

```markdown
- A deployment pins what it runs to a commit or an image digest. A tag can move, so it isn't a pin.
- Libraries have lower bounds in the manifest and exact versions in the committed lockfile.
- Tools, Ansible collections, and other downloads are pinned to an exact version or commit, with a checksum when the source isn't signed. [containers.md](https://github.com/lowmagic/.github/blob/main/code-standards/containers.md) covers image tags in compose files.
- GitHub Actions use a major tag for `actions/*`. Any other action is pinned to a full commit SHA, with a comment naming its version.
- A release is an annotated `vX.Y.Z` tag on main, and the release commit sets the manifest's version to match. Versions follow semver over the interface the repo's architecture names: a published schema or views, an API, or output another program reads. Another scheme needs an ADR.
```

This drops the OS packages and standards rows, the `uvx` launcher bullet, the first release at `0.0.0`, the CI check of a tag against the manifest, reading the version from package metadata, and the integer version for data formats (`python.md` and `sql.md` already number migrations). In `go.md`, the version bullet becomes: "A module's version is its `vX.Y.Z` tag. From `v2` on, the module path ends in `/vN`, as Go requires. Code that reports the version reads it with `debug.ReadBuildInfo`." On resync, hearsay, ideamachine, lattice and infra drop the deviations these rules no longer back; the ones about tags that aren't `vX.Y.Z` stay.

## Docs: drop the fixed spec and roadmap format

Five repos list the roadmap format as a deviation (hearsay, ideamachine, pen16, prometheus-tsdb-reader, compa-engine), and nothing checks it. The examples keep showing one shape. Replace the `docs/SPEC.md`, `docs/ROADMAP.md` and deferred-requirement bullets with:

```markdown
- `docs/SPEC.md` says what the product is meant to do. It opens with the problem: who has it, and what goes wrong for them today. Then the requirements, each with a short ID, such as `QUERY-3`, that the roadmap cites. What isn't checked or decided yet goes under Unknowns and Open questions. [examples/SPEC.md](https://github.com/lowmagic/.github/blob/main/code-standards/examples/SPEC.md) shows the shape.
- `docs/ROADMAP.md` says the order it gets built in: numbered slices, each usable on its own. Each says what a user would notice, which requirement IDs it delivers, and how someone can demonstrate it's done, and is marked done once it is. No dates; git says when. [examples/ROADMAP.md](https://github.com/lowmagic/.github/blob/main/code-standards/examples/ROADMAP.md) shows the shape.
- A deferred requirement stays in the spec and gets a later slice, not a stub in code.
```

This drops the `Status:`/`Delivers:`/`Done when:` field order, the rule that the done-making change sets the status, the never-renumber rule for IDs, and the check of spec IDs against `Delivers:` lines. On resync, hearsay, ideamachine, pen16 and prometheus-tsdb-reader drop their `Status:`/`Delivers:` deviations; missing IDs, missing problem statements and missing done-whens stay.
