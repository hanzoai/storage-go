# Hanzo Storage Go SDK

[![Go Reference](https://pkg.go.dev/badge/github.com/hanzoai/storage-go.svg)](https://pkg.go.dev/github.com/hanzoai/storage-go)
[![CI](https://github.com/hanzoai/storage-go/actions/workflows/ci.yml/badge.svg)](https://github.com/hanzoai/storage-go/actions/workflows/ci.yml)
[![Apache V2 License](https://img.shields.io/badge/license-Apache%20V2-blue.svg)](https://github.com/hanzoai/storage-go/blob/master/LICENSE)

Go client SDK for Hanzo Storage (S3-compatible object storage). Built on MinIO Go client.

## Install

```sh
go get github.com/hanzoai/storage-go
```

## Quick Start

```go
package main

import (
	"context"
	"log"

	storage "github.com/hanzoai/storage-go"
	"github.com/hanzoai/storage-go/pkg/credentials"
)

func main() {
	client, err := storage.New("storage.hanzo.ai", &storage.Options{
		Creds:  credentials.NewStaticV4("YOUR-ACCESS-KEY", "YOUR-SECRET-KEY", ""),
		Secure: true,
	})
	if err != nil {
		log.Fatalln(err)
	}

	log.Printf("connected: %#v\n", client)
}
```

## Common Operations

### Create a Bucket

```go
ctx := context.Background()
err := client.MakeBucket(ctx, "my-bucket", storage.MakeBucketOptions{
	Region: "us-east-1",
})
if err != nil {
	log.Fatalln(err)
}
```

### Upload an Object

```go
file, err := os.Open("/tmp/data.bin")
if err != nil {
	log.Fatalln(err)
}
defer file.Close()

info, err := client.PutObject(ctx, "my-bucket", "data.bin", file, -1,
	storage.PutObjectOptions{ContentType: "application/octet-stream"})
if err != nil {
	log.Fatalln(err)
}
log.Printf("uploaded: %s (%d bytes)\n", info.Key, info.Size)
```

### Download an Object

```go
obj, err := client.GetObject(ctx, "my-bucket", "data.bin", storage.GetObjectOptions{})
if err != nil {
	log.Fatalln(err)
}
defer obj.Close()

localFile, err := os.Create("/tmp/data.bin")
if err != nil {
	log.Fatalln(err)
}
defer localFile.Close()

if _, err := io.Copy(localFile, obj); err != nil {
	log.Fatalln(err)
}
```

### List Objects

```go
for obj := range client.ListObjects(ctx, "my-bucket", storage.ListObjectsOptions{Recursive: true}) {
	if obj.Err != nil {
		log.Fatalln(obj.Err)
	}
	log.Println(obj.Key, obj.Size)
}
```

### Remove an Object

```go
err := client.RemoveObject(ctx, "my-bucket", "data.bin", storage.RemoveObjectOptions{})
if err != nil {
	log.Fatalln(err)
}
```

### Upload a File from Disk

```go
info, err := client.FPutObject(ctx, "my-bucket", "backup.tar.gz", "/tmp/backup.tar.gz",
	storage.PutObjectOptions{ContentType: "application/gzip"})
if err != nil {
	log.Fatalln(err)
}
log.Printf("uploaded %s (%d bytes)\n", info.Key, info.Size)
```

## API Reference

Full API documentation is available on pkg.go.dev:

- [github.com/hanzoai/storage-go](https://pkg.go.dev/github.com/hanzoai/storage-go)

### Bucket Operations

| Method | Description |
|--------|-------------|
| `MakeBucket` | Create a new bucket |
| `ListBuckets` | List all buckets |
| `BucketExists` | Check if a bucket exists |
| `RemoveBucket` | Remove a bucket |
| `ListObjects` | List objects in a bucket |
| `ListIncompleteUploads` | List in-progress multipart uploads |

### Object Operations

| Method | Description |
|--------|-------------|
| `GetObject` | Download an object |
| `PutObject` | Upload an object from a reader |
| `FGetObject` | Download an object to a file |
| `FPutObject` | Upload an object from a file |
| `StatObject` | Get object metadata |
| `CopyObject` | Copy an object server-side |
| `RemoveObject` | Remove a single object |
| `RemoveObjects` | Remove multiple objects |
| `SelectObjectContent` | Run S3 Select queries on an object |

### Presigned Operations

| Method | Description |
|--------|-------------|
| `PresignedGetObject` | Generate a presigned URL for download |
| `PresignedPutObject` | Generate a presigned URL for upload |
| `PresignedHeadObject` | Generate a presigned URL for HEAD |
| `PresignedPostPolicy` | Generate presigned POST form data |

### Bucket Policy and Notification

| Method | Description |
|--------|-------------|
| `SetBucketPolicy` | Set bucket access policy |
| `GetBucketPolicy` | Get bucket access policy |
| `SetBucketNotification` | Configure event notifications |
| `GetBucketNotification` | Get event notification config |
| `RemoveAllBucketNotification` | Remove all notification configs |
| `ListenBucketNotification` | Listen for bucket events |

## Examples

Working examples are in the [`examples/`](https://github.com/hanzoai/storage-go/tree/master/examples) directory:

- [`examples/s3/`](https://github.com/hanzoai/storage-go/tree/master/examples/s3) -- S3-compatible operations
- [`examples/minio/`](https://github.com/hanzoai/storage-go/tree/master/examples/minio) -- Extension operations

## Attribution

Based on [MinIO Go Client SDK](https://github.com/minio/minio-go). See upstream [LICENSE](https://github.com/minio/minio-go/blob/master/LICENSE) for attribution.

## License

Copyright (c) Hanzo AI, Inc. Licensed under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0). See [LICENSE](./LICENSE) for details.
