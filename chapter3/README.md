## CloudFormation으로 Iam user 생성하기

- IAM_USER.yml 파일을 이용하여 CloudFormation 스택을 생성하여 i am user를 생성해보자
  ![alt text](image.png)

![alt text](image-1.png)

![alt text](image-2.png)

![alt text](image-3.png)

![alt text](image-4.png)

![alt text](image-5.png)

## AWS CLI 환경 구성하기

- 루트 사용자를 이용해 AWS CLI환경 구성하기

1. AWS CLI 파일 다운로드 및 설치

```bash
# 설치 파일을 다운로드
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
# 다운로드한 패키지를 시스템에 설치
sudo installer -pkg ./AWSCLIV2.pkg -target /
# 버전 확인
aws --version

```

2. IAM USER(S3권한만 부여)를 생성

- AWS 시크릿 관리자 콘솔 화면에서 액세스 키와 시크릿 키를 확인한다.

3. AWS 자격 증명(Credentials) 설정

- IAM 사용자(S3 권한 부여 등) 생성 후 발급받은 액세스 키와 시크릿 키를 등록합니다.

```bash
yunajoe@yunajoeui-MacBookPro ~ % aws configure list
NAME       | VALUE                 | TYPE          | LOCATION
profile    | <not set>             | None          | None
access_key | <not set>             | None          | None
secret_key | <not set>             | None          | None
region     | <not set>             | None          | None

# 설정 진행
AWS Access Key ID [None]: ************************
AWS Secret Access Key [None]:  ************************
Default region name [None]: ap-northeast-2
Default output format [None]: json

Configure AWS skills and the AWS MCP server for your AI coding agent(s)? [y/n/never]: n

```

```bash
# 설정 완료 후 확인
yunajoe@yunajoeui-MacBookPro ~ % aws configure list
NAME       | VALUE                 | TYPE                    | LOCATION
profile    | <not set>             | None                    | None
access_key | ********************  | shared-credentials-file |
secret_key | ********************  | shared-credentials-file |
region     | ap-northeast-2        | config-file             | ~/.aws/config

```

4. 가상 MFA 디바이스 생성 및 활성화

- 터미널에서 가상 MFA 디바이스를 생성하고 QR코드 이미지를 바탕화면에 저장합니다.

```bash

yunajoe@yunajoeui-MacBookPro ~ % aws iam create-virtual-mfa-device \
  --virtual-mfa-device-name yunajoe-mfa \
  --bootstrap-method QRCodePNG \
  --outfile ~/Desktop/QRCode.png
```

```json
{
  "VirtualMFADevice": {
    "SerialNumber": "arn:aws:iam::***********:mfa/yunajoe-mfa"
  }
}
```

5. MFA 활성화 (인증 코드 입력)

- 스마트폰 인증 앱(Google Authenticator 등)으로 다운로드한 QR코드를 스캔한 뒤, 연속으로 생성되는 6자리 숫자 두 개를 입력하여 MFA를 활성화

```bash

yunajoe@yunajoeui-MacBookPro ~ % aws iam enable-mfa-device \
  --user-name iam-yuna \
  --serial-number arn:aws:iam::***********:mfa/yunajoe-mfa \
  --authentication-code1 ****** \
  --authentication-code2 ******
```

6. IAM USER로 로그인하여 MFA 설정되어 있나 확인
