# AWS Code Build

## Prepare
1. mkdir apps
1. clone project
1. build test

``` bash
$ mkdir apps
$ git clone https://github.com/mondrianlab/mondrian-lab-boot.git
$ ./gradlew clean build -x test
```

### GitHub Personal Access Token

1. Settings > Developer Settings > Personal access tokens
1. Create New Token
1. Scopes: repo


## AWS Code Build

1. 빌드: CodeBuild
1. 배포: CodeDeploy
1. 파이프라인: CodePipeline

## Code Build

github 에서 소스를 내려받아 빌드를 수행.  
빌드 수행 절차는 buildspec.yml 파일로 정의한다.  
빌드 결과 아티팩트를 S3 에 업로드

1. S3 버킷 생성
    - Name: gitabout-mondrian-lab-bucket
    
1. 빌드 프로젝트 생성
    - Name: mondrian-lab-ui-build
    - Github 개인용 억세스 토큰 저장
    - URL: https://github.com/mondrianlab/mondrian-lab-boot
    - 환경 이미지: 관리형 이미지
    - 운영체제: Amazon Linux2
    - Runtime: Standard 3.0
    - 서비스역할: 새 서비스 역할
    - Artifact
        - Amazon S3
        - Bucket: gitabout-mondrian-lab-bucket
        - Name: mondrian-lab-ui-build
        
1. 빌드 실행
    - 결과 파일이 S3 bucket 에 도착했는지 확인
    
## Code Deploy

### EC2 IAM 역할 부여

1. IAM 역할 생성
    - 정책: AmazonEC2RoleForAWSCodeDeploy
    - Name: mondrianlab-ec2-codedeploy    
1. EC2 IAM 역할 수정
    - 작업 > 보안 > IAM 역할 수정
    - mondrianlab-ec2-codedeploy 선택
1. EC2 재부팅

### CodeDeploy Agent 설치

* Agent 설치
    ``` bash 
    wget https://aws-codedeploy-ap-northeast-2.s3.amazonaws.com/latest/install 
    chmod +x ./install
    sudo ./install auto
    ```
   
* 설치시 ruby 를 못찾겠다는 오류가 발생하는 경우
   ``` bash  
   sudo yum install ruby
   ```
   
* 설치 확인
   ``` bash 
   sudo service codedeploy-agent status
   ```
   
### CodeDeploy 어플리케이션 생성

1. IAM 역할 생성
    - 정택: AWSCodeDeployRole
    - Name: mondrianlab-codedeploy-role 
   
1. Application 생성
    - Name: mondrianlab-ui
    - 플랫폼: EC2/온프레미스

1. 배포그룹생성
    - Name: mondrianlab-codedeploy-group
    - 서비스역할: mondrianlab-codedeploy-role
    - 환경구성
        - Amazon EC2 인스턴스
        - 태그 Name(mondrianlab-ec2) 으로 EC2 인스턴스 선택
    - AWS CodeDeploy 에이전트 설치: 안함    
    - 로드밸런서 비활성
   
## Code Pipeline

1. 파이프라인 생성
    - Name: mondrian-lab-ui-pipeline
    
1. 소스 스테이지
    - Github v1
    - repo: mondrianlab/mondrian-lab-ui
    - branch: master
    
1. 빌드 스테이지
    - AWS Code Build
    - mondrianlab-ui-build

1. 배포 스테이지
    - AWS Code Deploy
    - mondrianlab-codedeploy
    
        
    

