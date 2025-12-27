# ---------- Build stage ----------
FROM golang:1.22-alpine AS builder

WORKDIR /app

# Install git (needed for some Go modules)
RUN apk add --no-cache git

# Copy go mod files first for caching
COPY go.mod go.sum ./
RUN go mod download

# Copy the rest of the source
COPY . .

# Build the binary
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -o app ./cmd/api

# ---------- Runtime stage ----------
FROM alpine:3.19

WORKDIR /app

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy binary from builder
COPY --from=builder /app/app .

# Expose backend port
EXPOSE 8080

# Run as non-root
USER appuser

CMD ["./app"]
