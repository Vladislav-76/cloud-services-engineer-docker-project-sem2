FROM golang:1.24.3-alpine AS builder

WORKDIR /app

COPY . .

RUN apk add --no-cache git &&\
    go mod download &&\
    CGO_ENABLED=0 go build -o api ./cmd/api


FROM alpine:3.22

WORKDIR /backend

COPY --from=builder /app/api .

RUN addgroup -g 1001 -S backend-group &&\
    adduser -u 1001 -S backend-user -G backend-group

USER backend-user

EXPOSE 8081

HEALTHCHECK \
  --interval=30s \
  --timeout=5s \
  --start-period=10s \
  --retries=5 \
  CMD wget -q --spider http://localhost:8081/health || exit 1

CMD ["./api"]
