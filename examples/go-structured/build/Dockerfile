FROM alpine:3.22

ARG TARGETPLATFORM

RUN apk --no-cache add ca-certificates

COPY $TARGETPLATFORM/app1 /usr/local/bin/app1

ENTRYPOINT ["/usr/local/bin/app1"]
