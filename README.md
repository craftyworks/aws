# aws
AWS 구축기

## AWS Console 가입

12개월 프리티어 혜택을 받으려면 1년마다 갈아타야 한다

* craftywrosk@gmail.com
* ddam40@gmail.com
* gitabout@gmai.com [이번에사용할계정]

## MFA

우측상단 아이디클릭 후 내 보안 자격증명 선택

MFA 활성화 클릭

가상 MFA 디바이스선택

구글 또는 MS 인증기로인증

## IAM

1. 사용자 추가
  - Name: gitabout
  - Access 유형: 프로그래밍 방식

1. Group 생성
  - Name: Admin
  - 정책: AdministratorAccess

1. Admin 그룹에 gitabout 유저 추가 

## EC2

인스턴스 > 인스턴스 시작

Amazon Linux 2 선택

스토리지 30GB

보안그룹 추가
  - ssh : 내IP 만
  - http
  - https

새 키 패어 생성
  - ssh 접속을위한 프라이비트키 다운로드
  - 이름: ec2-gitabout-ssh-key

인스턴스 시작

Tag 추가
    - Name : mondrian-lab-ec2
    
## 탄력적 IP

1. 탄력적IP 주소할당
1. 탄력적IP 주소연결 - 인스턴스선택

## SSH 접속 설정

### Linux

```bash
cp ec2-gitabout-ssh-key.pem ~/.ssh
chmod 600 ~/.ssh/ec2-gitabout-ssh-key.pem
vim ~/.ssh/config
```

config 파일에 아래내용 추가
```
Host gitabout-aws
    HostName [EIP]
    User ec2-user
    IdentityFile ~/.ssh/ec2-gitabout-ssh-key.pem
```

등록완료후 ssh 로접속

```bash
$ssh gitabout-aws
```

### Windows 

* puttygen.exe 로 pem 파일을 ppk 로 변환
  * Conversions > Import Key > Save private key
* putty.exe 에 ppk 파일 등록
  * Connection > SSH > Auth

## EC2 Linux 설정

### Java 8 설치
```bash
sudo yum list | grep java-1.8
sudo yum install java-1.8.0-openjdk-devel.x86_64
```

### Git 설치
```bash 
sudo yum install git
```

Git 설정

``` bash
git config --global user.email "craftyworks@gmail.com"
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global core.autocrlf true
git config --global credential.helper store
git config --global http.sslVerify false
git config --global core.quotepath false    
```

Git 설정 확인

git config --global --list
### 타임존 변경
```bash
sudo rm /etc/localtime
sudo ln -s /usr/share/zoneinfo/Asia/Seoul /etc/localtime

date
```
### HOSTNAME 변경
```bash
sudo vim /etc/sysconfig/network
```
HOSTNAME 추가
```
NETWORKING=yes
NOZEROCONF=yes
HOSTNAME=gitabout.com
```

### hosts 파일 수정
```bash
sudo vim /etc/hosts
```

```
127.0.0.1   gitabout.com localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost6 localhost6.localdomain6
```

### 재부팅
sudo reboot

