# Debian bookworm (glibc 2.36) to match the target Raspberry Pi 5. Base pinned by digest.
# libdbus-1-dev: affinidi-tdk-common links the D-Bus Secret Service store on Linux
# unconditionally, so it is needed even with --no-default-features.
# libssl-dev: webauthn-rs pulls in openssl-sys.
FROM rust@sha256:93ce27a88655056a51dbdd8f5f2d7ddc071c7b0070fb288a37b5a285fc83971e
RUN apt-get update \
 && apt-get install -y --no-install-recommends libdbus-1-dev libssl-dev pkg-config \
 && rm -rf /var/lib/apt/lists/*
