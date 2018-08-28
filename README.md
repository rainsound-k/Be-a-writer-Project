# Be a writer

Be a writer 서비스는 누구나 책을 작성해 작가가 될 수 있는 서비스입니다.

주소: [https://beawriter.co.kr](https://beawriter.co.kr)

Elastic Beanstalk에 Nginx-uWSGI-Django로 구성된 Docker 이미지를 배포합니다.

**소스코드는 Bitbucket private repository 에서 관리 중이며 아래 링크에서 세부 내용 확인 가능합니다.**

[프로젝트 설명](https://github.com/rainsound-k/Be-a-writer-Project/blob/master/beawriter-detail.pdf)


## Requirements

### 공통사항

- Python (3.6)
- .secrets/의 JSON파일 작성 (아래의 .secrets항목 참조)
- (선택사항) Docker로 실행할 경우, Docker설치 필요

### AWS환경

- Python (3.6)
- S3 Bucket, 해당 Bucket을 사용할 수 있는 IAM User의 AWS AccessKey, SecretAccessKey
- RDS Database(보안 그룹 허용 필요), 해당 Database를 사용할 수 있는 RDS의 User, Password

## Installation (Django runserver)

### 로컬 환경

```
pip install -r .requirements/dev.txt
python manage.py runserver
```

### AWS 환경

```
export DJANGO_SETTINGS_MODULE=config.settings.dev
pip install -r .requirements/dev.txt
python manage.py runserver
```

### 배포 환경

```
export DJANGO_SETTINGS_MODULE=config.settings.production
pip install -r .requirements/dev.txt
python manage.py runserver
```

## Installation (Docker)

### 로컬 환경

`localhost:8000` 에서 확인

```
docker build -t beawriter:local -f Dockerfile.local
docker run --rm -it 8000:80 beawriter:local
```

### AWS 환경 (개발 모드)

```
docker build -t beawriter:dev -f Dockerfile.dev
docker run --rm -it 8000:80 beawriter:dev
```

### AWS 환경 (배포 모드)

```
docker build -t beawriter:production -f Dockerfile.production
docker run --rm -it 8000:80 beawriter:production
```

## DockerHub 관련

apt, pip관련 내용을 미리 빌드해서 DockerHub 저장소에 업로드

```
docker build -t beawriter:base -f Dockerfile.base .
docker tag beawriter:base <자신의 사용자명>/<저장소명>:base
docker push <사용자명>/<저장소명>:base
```

이후 Elastic Beanstalk을 사용한 배포 시, 해당 이미지를 사용

```dockerfile
FROM    <사용자명/<저장소명>:base
...
...
```

## Deploy

`.deploy.sh`파일을 사용

```
./deploy.sh
```

## .secrets

### .secrets/base.json

```json
    {
      "SECRET_KEY": "<Django secret key>",
      "SUPERUSER_USERNAME": "<Default superuser username>",
      "SUPERUSER_EMAIL": "<Default superuser email>",
      "SUPERUSER_PASSWORD": "<Default superuser password>",
  
      "FACEBOOK_APP_ID": "<Facebook beawriter app id>",
      "FACEBOOK_SECRET_CODE": "<Facebook beawriter secret code>",
      
      "IAMPORT_SHOP_ID": "<Iamport shop id>",
      "IAMPORT_API_KEY": "<Iamport api key>",
      "IAMPORT_API_SECRET": "<Iamport api secret>",
      
      "EMAIL_HOST_USER": "<Company email>",
      "EMAIL_HOST_PASSWORD": "<Email host password>",

      "AWS_ACCESS_KEY_ID": "<AWS access key (Permission: S3)>",
      "AWS_SECRET_ACCESS_KEY": "<AWS secret access key>",
      "AWS_STORAGE_BUCKET_NAME": "<AWS S3 Bucket name>",
      "AWS_S3_REGION_NAME": "ap-northeast-2",
      "AWS_S3_SIGNATURE_VERSION": "s3v4",
      "AWS_DEFAULT_ACL": "private"
    }
```

### .secrest/dev.json, .secrets/production.json

```json
{
  "DATABASES": {
    "default": {
      "ENGINE": "django.db.backends.postgresql",
      "HOST": "<AWS RDS end-point>",
      "NAME": "<DB name>",
      "USER": "<DB username>",
      "PASSWORD": "<DB user password>",
      "PORT": 5432
    }
  }
}
```