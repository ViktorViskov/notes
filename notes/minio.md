Open source server to storage media files 

Docker compose file
```yaml
services:
  minio:
    container_name: minio_storage
    image: docker.io/bitnami/minio:2024
    restart: unless-stopped
    ports:
      - '9000:9000'
      - '9001:9001'
    environment:
      - MINIO_ROOT_USER=${MINIO_USER}
      - MINIO_ROOT_PASSWORD=${MINIO_PASS}
    volumes:
      - '/storage/hdd:/bitnami/minio/data'
```

### boto3 python client
```python
import boto3
from requests import get

from utils.config import load_config


class StorageAdditionFilesManager:
    def __init__(self) -> None:
        self.config = load_config()

        self._init_connection()
        self._create_bucket()
    
    def _init_connection(self) -> None:
        self.minio_client = boto3.client(
            's3',
            endpoint_url=self.config.MINIO_ADDR,
            aws_access_key_id=self.config.MINIO_USER,
            aws_secret_access_key=self.config.MINIO_PASSWORD)

    def _create_bucket(self) -> None:
        try:
            self.minio_client.head_bucket(Bucket=self.config.MINIO_BUCKET)
        except Exception as e:
            print(f"Creating bucket {self.config.MINIO_BUCKET}", e)
            self.minio_client.create_bucket(Bucket=self.config.MINIO_BUCKET)
    
    def add(self, file_name: str, file_body: bytes) -> str:
        try:
            file_path = f"{self.config.MINIO_DIR}/{file_name.rsplit('.', 1)[0]}.pdf"
            self.minio_client.put_object(Bucket=self.config.MINIO_BUCKET, Key=file_path, Body=file_body, ContentType='application/pdf')
            
            return file_name

        except Exception as e:
            # print(e)
            print(f"Can not load addition file", e)
```

### .env
```bash
MINIO_ADDR       =http://65.109.29.162:9000
MINIO_USER       =car_storage
MINIO_PASSWORD   =dfede
MINIO_BUCKET     =reports
MINIO_DIR        =carfaxx
```

### Example of public share for MinIO server or Amazone S3
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

[[backend]]
[[python]]
