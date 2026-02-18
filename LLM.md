# Hanzo Storage Go SDK

Fork of MinIO Go SDK (upstream: `github.com/minio/minio-go`).
S3-compatible object storage client for Hanzo services.

**Repo**: `github.com/hanzoai/storage-go`
**Go module**: `github.com/hanzoai/storage-go`
**Go version**: 1.24.0

## Usage

```go
import "github.com/hanzoai/storage-go"
```

Central type is the `Client` struct in `api.go`. All operations
accept `context.Context` and use Options structs for parameters.

## Commands

```bash
# Unit tests (no server needed)
go test -short -race ./...

# Full tests (requires MinIO at localhost:9000)
SERVER_ENDPOINT=localhost:9000 ACCESS_KEY=minioadmin SECRET_KEY=minioadmin \
  ENABLE_HTTPS=1 MINT_MODE=full go test -race -v ./...

# Functional tests
go build -race functional_tests.go
SERVER_ENDPOINT=localhost:9000 ACCESS_KEY=minioadmin SECRET_KEY=minioadmin \
  ENABLE_HTTPS=1 MINT_MODE=full ./functional_tests

# Lint
make lint
golangci-lint run --timeout=5m --config ./.golangci.yml

# Build examples
make examples
```

## File Layout

```
api.go                  # Client struct, core initialization
api-get-*.go            # GET operations (objects, ACLs, attributes)
api-put-*.go            # PUT operations
api-list.go             # Listing operations
api-stat.go             # Stat/info operations
api-copy-object.go      # Copy operations
api-compose-object.go   # Multipart compose
api-bucket-*.go         # Bucket ops (lifecycle, encryption, versioning, etc.)
api-object-*.go         # Object ops (legal hold, retention, tagging, lock)
api-error-response.go   # Error types and HTTP status mapping
api-datatypes.go        # Shared data types
functional_tests.go     # Integration tests (build tag: mint)
```

## Packages (`pkg/`)

| Package | Purpose |
|---------|---------|
| `credentials/` | Credential providers (static, env, IAM, STS, file, chain) |
| `signer/` | AWS Signature V2/V4, streaming signatures |
| `encrypt/` | Server-side encryption (SSE-C, SSE-S3, SSE-KMS) |
| `s3utils/` | URL parsing, region detection |
| `lifecycle/` | Object lifecycle rules |
| `notification/` | Bucket event notifications |
| `policy/` | Bucket policy management |
| `tags/` | Object and bucket tagging |
| `replication/` | Bucket replication config |
| `cors/` | CORS configuration |
| `kvcache/` | Key-value cache |
| `singleflight/` | Concurrent request dedup |

## Patterns

- Path-style and virtual-host-style S3 URLs supported
- Streaming via io.Reader/Writer for large objects
- Retry with configurable max retries
- Bucket location caching (`bucketLocCache`)
- MD5/SHA256 integrity checks configurable per request
