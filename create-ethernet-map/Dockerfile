FROM ubuntu:22.04

ARG DEBIAN_FRONTEND=noninteractive

RUN apt -y update \
    && apt install -y git cargo rustc \
    && rm -rf /var/lib/apt/lists/*

RUN git clone https://github.com/tenstorrent/luwen /usr/luwen

WORKDIR /usr/luwen/app/create-ethernet-map

RUN cargo update zerofrom@0.1.6 --precise 0.1.5 && cargo update litemap@0.7.5 --precise 0.7.4

RUN cargo build --release --target x86_64-unknown-linux-gnu

FROM ubuntu:22.04
COPY --from=0 /usr/luwen/target/x86_64-unknown-linux-gnu/release/create-ethernet-map /bin/create-ethernet-map

ENTRYPOINT ["bash", "-c", "/bin/create-ethernet-map output && cat output"]
