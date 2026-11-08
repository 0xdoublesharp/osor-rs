# Publishing OSOR

The `Publish crate` workflow is started manually from `main`. It resolves a
release tag to one commit, checks its version, runs the complete CPU/GPU-feature
CI matrix and dependency policy, builds CPU API docs, and verifies the packaged
crate. Uploading requires the separate `publish` input.

## Prepare a release

1. Update `Cargo.toml`, `Cargo.lock`, and the changelog, then run
   `scripts/check.sh` and `scripts/cargo.sh publish --locked --dry-run`.
2. Commit and push the release source to `main`.
3. Create and push the matching tag, for example `v0.1.0` for package `0.1.0`.
4. Run `Publish crate` from the Actions tab with that tag and leave **Upload to
   crates.io** unchecked. All checks must pass.
5. Run it again with the same tag and **Upload to crates.io** checked to publish.

The equivalent GitHub CLI commands are:

```sh
gh workflow run publish.yml --ref main -f tag=v0.1.0
# When ready to upload:
gh workflow run publish.yml --ref main -f tag=v0.1.0 -F publish=true
```

Keep published tags fixed. Confirm the
[crate page](https://crates.io/crates/osor) and [API docs](https://docs.rs/osor).
Git consumers such as PhotoCraft keep their resolved source in `Cargo.lock`.

## Authentication

The upload job publishes with crates.io
[trusted publishing](https://crates.io/docs/trusted-publishing). The `osor`
crate's trusted publisher on crates.io must name repository
`0xdoublesharp/osor-rs` and workflow `publish.yml`. `rust-lang/crates-io-auth-action` exchanges the job's OIDC token
for a short-lived crates.io token and revokes it when the job ends. The
repository stores no registry token.

## Package and documentation

`cargo package --list` shows the published files. Repository-only agent guidance,
CI workflows and dependency-policy configuration are excluded; source, examples,
documentation and all license notices remain included. Weight files stay outside
the package. docs.rs builds the CPU library for `x86_64-unknown-linux-gnu`, with
no GPU toolkit required.
