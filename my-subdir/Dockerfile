ARG BUILDER_IMAGE=golang:1.19
ARG RUNTIME_IMAGE=cgr.dev/chainguard/busybox

FROM $BUILDER_IMAGE AS build

    WORKDIR /app
    ADD . .
    RUN go build -o app-golang-kaniko-with-subpath main.go


FROM $RUNTIME_IMAGE AS runtime

    ENV PORT=8080
    EXPOSE 8080
    COPY --from=build /app/app-golang-kaniko-with-subpath /app-golang-kaniko-with-subpath
    CMD [ "/app-golang-kaniko-with-subpath" ]
