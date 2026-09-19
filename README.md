# vta-lab-build

Reproducible **aarch64 Linux** build of the OpenVTC
[verifiable-trust-infrastructure](https://github.com/OpenVTC/verifiable-trust-infrastructure)
`vta` service, pinned to a release tag, on GitHub's free arm64 runners.

It exists because the same build does not fit on an 8 GB laptop-class machine:
one dependency, `trust-tasks-rs`, is about 525,000 lines of generated Rust in a
single crate and needs more than 4 GB for one compiler process.

## What the workflow does

1. Clones upstream at `vta-service-v0.34.1` and checks the commit matches.
2. Builds a Debian bookworm image (glibc 2.36, to match a Raspberry Pi 5),
   pinned by digest, carrying `libdbus-1-dev` and `libssl-dev`.
3. Builds `vta` twice, on two separate runners, and records a source lock:
   tag, commit, `Cargo.lock` digest, features, toolchain and native package
   versions.
4. Runs `cargo deny` against upstream's own `deny.toml` and writes a CycloneDX SBOM.
5. Compares the two binaries and publishes `build-manifest.json` with the
   reproducibility level actually achieved.

## Two things worth knowing about this build

- **A DIDComm-free build does not compile.** With `didcomm` off,
  `vta-service` fails with 18 errors at this tag: `trust_tasks/services.rs`
  imports DIDComm-only operations with no `cfg` gate. The feature is additive
  in name only. The transport can still be left off at runtime.
- **`cli-synthesis` cannot be omitted.** The `vta` binary declares it in
  `required-features`, so every local build carries the super-admin claim
  synthesiser that upstream's own comments call a footgun.

Nothing here is production infrastructure. No secrets are used, and the build
runs only public, pinned sources.
