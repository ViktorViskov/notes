Example of public share for MinIO server or Amazone S3

```json
policy = {
    "Version": "2012-10-17",
    "Statement": [
        {
        "Effect": "Allow",
        "Principal": { "AWS": ["*"] },
        "Action": ["s3:GetBucketLocation", "s3:ListBucket"],
        "Resource": [f"arn:aws:s3:::{bucket}"]
        },
        {
        "Effect": "Allow",
        "Principal": { "AWS": ["*"] },
        "Action": ["s3:GetObject"],
        "Resource": [f"arn:aws:s3:::{bucket}/*"]
        }
    ]
}
```

[[minio]]
[[s3]]
