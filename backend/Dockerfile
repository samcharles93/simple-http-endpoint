FROM --platform=$BUILDPLATFORM golang:1.20.3-alpine AS builder

WORKDIR /code

ENV CGO_ENABLED 0
ENV GOPATH /go
ENV GOCACHE /go-build

COPY go.mod go.sum main.go ./
RUN --mount=type=cache,target=/go/pkg/mod/cache \
    --mount=type=cache,target=/go-build \
    go build -o bin/backend main.go

CMD ["/code/bin/backend"]

FROM builder as dev-envs

RUN <<EOF
apk update
apk add git netcat-openbsd
EOF

RUN <<EOF
addgroup -S docker
adduser -S --shell /bin/bash --ingroup docker vscode
EOF

COPY --from=gloursdocker/docker / /

CMD [ "go", "run", "main.go" ]

FROM alpine
COPY --from=builder /code/bin/backend /usr/local/bin/backend
CMD [ "/usr/local/bin/backend" ]
