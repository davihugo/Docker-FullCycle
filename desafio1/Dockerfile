FROM golang:1.22-alpine AS builder

WORKDIR /app

COPY main.go .

RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o app main.go


FROM scratch

COPY --from=builder /app/app /app

ENTRYPOINT ["/app"]
