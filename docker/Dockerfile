FROM alpine:3 AS alpine
ARG TARGETOS
ARG TARGETARCH
RUN apk add -U --no-cache ca-certificates

RUN if [ "$TARGETARCH" = "arm64" ]; then \
        export DOWNLOAD_ARCH=arm64v8; \
    elif [ "$TARGETARCH" = "amd64" ]; then \
        export DOWNLOAD_ARCH=amd64; \
    else \
        echo "Unsupported architecture: $TARGETARCH" && exit 1; \
    fi && \
    wget https://github.com/tmate-io/tmate/releases/download/2.4.0/tmate-2.4.0-static-${TARGETOS}-${DOWNLOAD_ARCH}.tar.xz; \
    tar -xf tmate-2.4.0-static-${TARGETOS}-${DOWNLOAD_ARCH}.tar.xz && \
    mv tmate-2.4.0-static-${TARGETOS}-${DOWNLOAD_ARCH}/tmate /bin/
RUN chmod +x /bin/tmate


FROM alpine:3
ARG TARGETOS
ARG TARGETARCH
EXPOSE 3000

ENV GODEBUG netdns=go
ENV DRONE_PLATFORM_OS ${TARGETOS}
ENV DRONE_PLATFORM_ARCH ${TARGETARCH}

COPY --from=alpine /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=alpine /bin/tmate /bin/

LABEL com.centurylinklabs.watchtower.stop-signal="SIGINT"

ADD release/${TARGETOS}/${TARGETARCH}/drone-runner-docker /bin/
ENTRYPOINT ["/bin/drone-runner-docker"]
