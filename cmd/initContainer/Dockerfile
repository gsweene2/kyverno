# Multi-stage docker build
# Build stage
FROM golang@sha256:724abf4dd44985d060f7aa91af5211eb2052491424bd497ba3ddc31f7cee969d AS builder

LABEL maintainer="Kyverno"

# LD_FLAGS is passed as argument from Makefile. It will be empty, if no argument passed
ARG LD_FLAGS
ARG TARGETPLATFORM

ADD . /kyverno
WORKDIR /kyverno

RUN export GOOS=$(echo ${TARGETPLATFORM} | cut -d / -f1) && \
    export GOARCH=$(echo ${TARGETPLATFORM} | cut -d / -f2)

RUN go env

RUN CGO_ENABLED=0 go build -o /output/kyvernopre -ldflags="${LD_FLAGS}" -v ./cmd/initContainer/

# Packaging stage
FROM scratch

LABEL maintainer="Kyverno"

COPY --from=builder /output/kyvernopre /
COPY --from=builder /etc/passwd /etc/passwd

USER 10001

ENTRYPOINT ["./kyvernopre"]
