ARG BUILD_FROM
FROM rust:bookworm AS govee2mqtt
ARG SOURCE_REV=5660911412b6f8d5a069249fa4e8115ff33764bd
RUN apt-get update && apt-get install -y --no-install-recommends cmake clang pkg-config libssl-dev && rm -rf /var/lib/apt/lists/*
WORKDIR /src
RUN git init && \
    git fetch --depth=1 https://github.com/wardmatter/govee2mqtt.git "$SOURCE_REV" && \
    git checkout --detach FETCH_HEAD
RUN cargo build --release --locked --jobs 2

FROM ${BUILD_FROM}
COPY run.sh /run.sh
COPY --from=govee2mqtt /src/target/release/govee /app/
COPY --from=govee2mqtt /src/assets /app/assets/
COPY --from=govee2mqtt /src/AmazonRootCA1.pem /app/
CMD ["/run.sh"]
