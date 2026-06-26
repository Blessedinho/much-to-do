# Build Stage
FROM golang:1.26 AS builder
WORKDIR /app

ENV GOPROXY=https://proxy.golang.org,direct
ENV GOSUMDB=off
ENV GOCACHE=/root/.cache/go-build

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -o muchtodo ./cmd/api

# Runtime Stage
FROM alpine:latest
RUN apk add --no-cache wget ca-certificates
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
WORKDIR /app
COPY --from=builder /app/muchtodo .
USER appuser
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:8080/health > /dev/null || exit 1
CMD ["./muchtodo"]
