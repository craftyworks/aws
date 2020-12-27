# RDS

## 데이터베이스 생성

- mariadb 10.2
- freetire
- name: db-mondrian
- id: mondrian
- pwd: mondrian114e!
- 스토리지 자동조정 안함
- 퍼블릭 억세스 가능
- name: rds-authgroup


## 파라미터 그룹

### 생성
1. 파라미터 그룹 생성
1. name: mondrian-mariadb

### 편집 

mondrian-mariadb 편집

character_set_client		utf8mb4
character_set_connection		utf8mb4
character_set_database		utf8mb4
character_set_filesystem		utf8mb4
character_set_results		utf8mb4
character_set_server		utf8mb4
collation_connection		utf8mb4_general_ci
collation_server		utf8mb4_general_ci
time_zone		Asia/Seoul
max_connections 150
lower_case_table_names 1

> utf8mb4 는 이모지 저장 가능 

### 파라미터반영

DB -> 수정

DB 파라미터 그룹 -> mondrian-mariadb 로변경


## 보안그룹 수정

EC2 > 보안그룹 

e2-authgorup 의 보안그룹ID를 복사하여 rds-authgroup 의 inbound 에 추가한다

## DB dump

1. EC2 에 mysql 클라이언트 설치

    ```bash
    sudo yum install mysql
    ```

2. 소스DB에서 export  
    ``` bash
    mysqldump -h {source_MySQL_DB_instance_endpoint} \
        -u{user} \
        -p{password} \
        --databases  database database2 > backup.sql
    ```

3. 타겟DB import
    ``` bash 
    mysql -h {target_MySQL_DB_instanct_endpoint} -u{user} -p < backup.sql  
   ```
