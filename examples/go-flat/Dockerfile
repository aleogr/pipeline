FROM alpine:3.22

ARG TARGETPLATFORM

RUN apk --no-cache add ca-certificates

COPY $TARGETPLATFORM/app /usr/local/bin/app

ENTRYPOINT ["/usr/local/bin/app"]
