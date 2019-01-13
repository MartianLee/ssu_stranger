# 이방인 STRANGER in 숭실대학교 SSU

## 실환경 구축 방법

현재 실서버에서는 dotenv gem을 사용해 키를 관리하고 있습니다.
키는 실서버에서 수동으로 고치는 것이 아니라 Enginyard 서버 각각의 cookbook을 통해 배포합니다.
키는 cookbook의 custom-env-vars/files/프로젝트명/env.custom에 있습니다.
키는 프로젝트루트/shared/config/config/env.custom에 배포됩니다.

```
export SLACK_WEBHOOK_URL=xx
export ADMIN_NAME=xx
export ADMIN_PASSWORD=xx
export SMS_API_AUTH_TOKEN=xx
export SMS_SENDER=xx
export MAILTRAP_USERNAME=xx
export MAILTRAP_PASSWORD=xx
```

SMS는 청기와랩을 사용합니다. SMS_SENDER에는 SMS API앱에 등록된 발송대표전화번호 를 넣습니다. 아래 결과값을 SMS_API_AUTH_TOKEN에 넣습니다.

```
irb> Base64.encode64('청기와 SMS API_ID:청기와 SMS API_KEY').chomp
```

## 로컬 개발 환경 구축 방법

기본적인 Rail 개발 환경에 rbenv, pow/powder를 이용합니다.

```
$ rbenv install 2.3.1
$ bundle install
```

### 소스관리 설정

반드시 https://github.com/awslabs/git-secrets를 설치하도록 합니다. 설치 후에 반드시 https://github.com/awslabs/git-secrets#installing-git-secrets 이 부분을 참고하여 로컬 레포지토리에 모두 설정 합니다.

```
$ git secrets --install
$ git secrets --register-aws
```

그리고 데이터베이스는 각 레포지토리마다 다릅니다. 아래 git hook 을 설정합니다

```
$ echo $'#!/bin/sh\nif [ "1" == "$3" ]; then spring stop && touch ./tmp/restart.txt; fi' > .git/hooks/post-checkout
$ chmod +x .git/hooks/post-checkout
```

### 데이터베이스 준비

#### mysql 설정
mysql을 구동해야합니다. mysql의 encoding은 utf8mb4를 사용합니다. mysql은 버전 5.6 이상을 사용합니다.

encoding세팅은 my.cnf에 아래 설정을 넣고 반드시 재구동합니다. 참고로 맥에선 /usr/local/Cellar/mysql/(설치하신 mysql버전 번호)/my.cnf입니다.

```
[mysqld]
innodb_file_format=Barracuda
innodb_large_prefix = ON
```

#### 연결 정보

프로젝트 최상위 폴더에 local_env.yml이라는 파일을 만듭니다. 데이터베이스 연결 정보를 아래와 예시를 보고 적당히 입력합니다.

```
development:
  database:
    username: 사용자이름
    password: 암호
```
 
#### 스키마

```
mysql > CREATE DATABASE ssu_stranger_브랜치이름 CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

이후 db:migrate로 수행합니다.

### 관리자 준비

프로젝트 최상위 폴더에 .powenv에 등록합니다.

```
export ADMIN_NAME="admin"
export ADMIN_PASSWORD="1234"
export SMS_API_AUTH_TOKEN="xx"
export SMS_SENDER="xx"
export SMS_RECEIVER="xx"
```
