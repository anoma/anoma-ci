VERSION 0.8

anoma:
  FROM ubuntu:24.04

  ENV DEBIAN_FRONTEND=noninteractive

  WORKDIR /__w/anoma/anomapay-backend

  ARG toolchain=stable
  ARG mold_version=2.40.4
  ARG tag=anoma-main

  RUN apt-get update -y
  RUN apt-get install -y curl
  RUN apt-get install -y protobuf-compiler 
  RUN apt-get install -y build-essential 
  RUN apt-get install -y clang-tools clang
  RUN apt-get install -y libudev-dev
  RUN apt-get install -y libssl-dev
  RUN apt-get install -y pkg-config
  RUN apt-get install -y gcc
  RUN apt-get install -y parallel
  RUN apt-get install -y python3
  RUN apt-get install -y ca-certificates
  RUN apt-get install -y unzip
  RUN apt-get install -y wget

  # install rust 
  RUN curl https://sh.rustup.rs -sSf | sh -s -- -y

  ENV PATH="/root/.cargo/bin:/root/.local/bin:$PATH"
  ENV RUSTUP_HOME="/root/.rustup"
  ENV CARGO_HOME="/root/.cargo"
    
  RUN rustup toolchain install $toolchain-x86_64-unknown-linux-gnu --no-self-update --component clippy,rustfmt,rust-analysis,rust-docs,rust-src,llvm-tools-preview
  RUN rustup target add --toolchain $toolchain-x86_64-unknown-linux-gnu wasm32-unknown-unknown
  RUN rustup default $toolchain-x86_64-unknown-linux-gnu

  # install cargo binstall 
  RUN curl -L --proto '=https' --tlsv1.2 -sSf https://raw.githubusercontent.com/cargo-bins/cargo-binstall/main/install-from-binstall-release.sh | bash

  # install cargo nextest
  RUN cargo binstall cargo-nextest --no-confirm

  # install sccache
  RUN cargo binstall sccache --no-confirm

  # install cargo cache
  RUN cargo binstall cargo-cache --no-confirm

  # install taplo
  RUN cargo binstall taplo-cli --no-confirm

  # install just
  RUN cargo binstall just --no-confirm

  # install foundry
  RUN curl -L https://foundry.paradigm.xyz | bash

  # download mold
  RUN curl -o mold.tar.gz -LO https://github.com/rui314/mold/releases/download/v$mold_version/mold-$mold_version-x86_64-linux.tar.gz
  RUN tar --strip-components 2 -xvzf mold.tar.gz mold-$mold_version-x86_64-linux/bin/mold
  RUN mv mold /usr/local/bin
  RUN chmod +x /usr/local/bin/mold

  SAVE IMAGE --push ghcr.io/heliaxdev/anoma-ci:anoma-latest ghcr.io/heliaxdev/anoma-ci:$tag