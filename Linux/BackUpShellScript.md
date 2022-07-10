# BackUp Shell Script

## 1. 풀 백업 Shell Script
```shell
#!/bin/sh
YEAR=$(date +%Y)
MONTH=$(date +%m)
DAY=$(date +%d)
HOUR=$(date +%H)
MINUTE=$(date +%M)
SECOND=$(date +%S)
API_BASE_URL={API_BASE_URL}
FULL_BACKUP_API_URL={FULL_BACKUP_API_URL}
BACKUP_TYPE="fullBackup"
HOST_BACKUP_PATH="/home/backUp/$BACKUP_TYPE"
FILE_NAME="full_backup_$YEAR$MONTH$DAY.tar"
echo "$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/$FILE_NAME >> Full BackUp Start...\n"
# 호스트 디렉토리 생성
if [ ! -d $HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY ]; then
  # 해당일자에 디렉토리 가 없다면 생성
  mkdir -p $HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY
fi
# 컨테이너 디렉토리 생성
sudo docker exec {데이터베이스 컨테이너 이름} mkdir -p /$BACKUP_TYPE/$YEAR/$MONTH/$DAY
# 풀 백업 덤프 실행
sudo docker exec {데이터베이스 컨테이너 이름} mariabackup --backup --target-dir=/$BACKUP_TYPE/$YEAR/$MONTH/$DAY --user={사용자} --password={비밀번호}
# 풀 백업 덤프 .tar 압축
sudo docker exec {데이터베이스 컨테이너 이름} tar -cvf $FILE_NAME /$BACKUP_TYPE/$YEAR/$MONTH/$DAY/
# 압축된 백업을 호스트 서버로 복사
sudo docker cp {데이터베이스 컨테이너 이름}:/$FILE_NAME /$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY
# 컨테이너에 남아있는 백업 데이터는 삭제
sudo docker exec {데이터베이스 컨테이너 이름} rm -rf $FILE_NAME
# 호스트 서버의 풀 백업 파일사이즈(바이트 단위) 체크
FILE_SIZE=$(wc -c "$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/$FILE_NAME" | awk '{print $1}')
# 풀 백업 덤프 이력저장
curl --location --request POST "$API_BASE_URL$FULL_BACKUP_API_URL" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "file_name=$FILE_NAME" \
  --data-urlencode "path=$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/" \
  --data-urlencode "backup_type=$BACKUP_TYPE" \
  --data-urlencode "file_size=$FILE_SIZE"
# 30일 이상 된 폴더 삭제(컨테이너의 압축되지 않은 데이터)
sudo docker exec {데이터베이스 컨테이너 이름} find /$BACKUP_TYPE -mindepth 1 -maxdepth 1 -mtime +30 -type d -exec rm -rf {} \;
echo "\n\n$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/$FILE_NAME >> Full BackUp Stop..."
```

## 2. 증분 백업 Shell Script
```shell
#!/bin/sh
YEAR=$(date +%Y)
MONTH=$(date +%m)
DAY=$(date +%d)
HOUR=$(date +%H)
MINUTE=$(date +%M)
SECOND=$(date +%S)
API_BASE_URL={API_BASE_URL}
INCREMENTAL_BACKUP_API_URL={INCREMENTAL_BACKUP_API_URL}
BACKUP_TYPE="incrementalBackup"
HOST_BACKUP_PATH="/home/backUp/$BACKUP_TYPE"
FULL_BACKUP_PATH="fullBackup/$(date +%Y -d 'last sunday')/$(date +%m -d 'last sunday')/$(date +%d -d 'last sunday')"
FILE_NAME="incremental_backup_$YEAR$MONTH$DAY.tar"
echo "$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/$FILE_NAME >> Incremental BackUp Start...\n"
# 호스트 디렉토리 생성
if [ ! -d $HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY ]; then
  # 해당일자에 디렉토리 가 없다면 생성
  mkdir -p $HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY
fi
# 컨테이너 디렉토리 생성
sudo docker exec {데이터베이스 컨테이너 이름} mkdir -p /$BACKUP_TYPE/$YEAR/$MONTH/$DAY
# 풀 백업 덤프 실행
sudo docker exec {데이터베이스 컨테이너 이름} mariabackup --backup --target-dir=/$BACKUP_TYPE/$YEAR/$MONTH/$DAY --incremental-basedir=/$FULL_BACKUP_PATH --user={사용자} --password={비밀번호}
# 풀 백업 덤프 .tar 압축
sudo docker exec {데이터베이스 컨테이너 이름} tar -cvf $FILE_NAME /$BACKUP_TYPE/
# 압축된 백업을 호스트 서버로 복사
sudo docker cp {데이터베이스 컨테이너 이름}:/$FILE_NAME /$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY
# 컨테이너에 남아있는 백업 데이터는 삭제
sudo docker exec {데이터베이스 컨테이너 이름} rm -rf $FILE_NAME
# 호스트 서버의 풀 백업 파일사이즈(바이트 단위) 체크
FILE_SIZE=$(wc -c "$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/$FILE_NAME" | awk '{print $1}')
# 풀 백업 덤프 이력저장
curl --location --request POST "$API_BASE_URL$INCREMENTAL_BACKUP_API_URL" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --data-urlencode "file_name=$FILE_NAME" \
  --data-urlencode "inc_backup_path=$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/" \
  --data-urlencode "full_backup_path=/$FULL_BACKUP_PATH" \
  --data-urlencode "backup_type=$BACKUP_TYPE" \
  --data-urlencode "file_size=$FILE_SIZE"
# 30일 이상 된 폴더 삭제
sudo docker exec {데이터베이스 컨테이너 이름} find /$BACKUP_TYPE -mindepth 1 -maxdepth 1 -mtime +30 -type d -exec rm -rf {} \;
echo "\n\n$HOST_BACKUP_PATH/$YEAR/$MONTH/$DAY/$FILE_NAME >> Incremental BackUp Stop..."
```

## 3. Cron Scheduler
```shell
$ sudo crontab -e
```
```shell
# 매주 일요일 00시 전체 백업
0 0 * * 0 /home/backUp/fullBackup.sh > /home/backUp/fullBackup/`date +\%Y`/`date +\%m`/`date +\%d`/full_backup_`date "+\%Y\%m\%d \%H:\%M:\%S"`.log
# 매주 월~토  00시 증분 백업
0 0 * * 1-6 /home/backUp/incrementalBackup.sh > /home/backUp/incrementalBackup/`date +\%Y`/`date +\%m`/`date +\%d`/incremental_backup_`date "+\%Y\%m\%d \%H:\%M:\%S"`.log
# Cron 작동 후 Postfix 메일 발송을 막는다.
MAILTO=""
```

## 4. 복원준비
### 4-1. 설명(호스트 기준)
- 준비(복원)를 통해 백업 중에 발생했던 데이터의 업데이트 사항들을 반영하여 데이터의 일관성을 유지한다.
- 만약 백업 중에 업데이트가 발생했으나 복원 준비를 하지 않은 상태에서 복원을 시도하면, InnoDB 엔진이 이를 발견하고 복원을 거부한다.

### 4-2. 전체(기본) 백업 복원 준비(호스트 기준)
```shell
$ mariabackup --prepare --target-dir={풀 백업경로}
```

### 4-3. 증분백업 복원 준비(호스트 기준)
```shell
$ mariabackup --prepare --target-dir={풀 백업경로} --incremental-dir={증분 백업경로}
```
```shell
예시...
전체 백업 데이터 복원 준비:
$ mariabackup --prepare --target-dir={풀 백업경로}
전체 백업 데이터 <- 증분1:
$ mariabackup --prepare --target-dir={풀 백업경로} --incremental-dir={증분 백업경로 1번}
전체 백업 데이터 <- 증분2:
$ mariabackup --prepare --target-dir={풀 백업경로} --incremental-dir={증분 백업경로 2번}
전체 백업 데이터 <- 증분3:
$ mariabackup --prepare --target-dir={풀 백업경로} --incremental-dir={증분 백업경로 3번}
...
```

## 5. 복원실행
### 5-1. 복원 조건
- 복원을 위해 MariaDB의 서비스를 잠시 중단한다.
- DB data 디렉토리(일반적으로 '/var/lib/mysql/')가 비워져 있어야 한다.
- 앞서 준비된 전체 백업 데이터로 복원을 진행한다.

### 5-2. 컨테이너 형태의 MariaDB 복원
- 복원조건 1번 을 충족시키려면 컨테이너를 정지해야 한다. ($ docker stop)
- 정지한 컨테이너에서는 Mariabackup 명령어를 내릴 수 없으므로, 원본 컨테이너의 DB 데이터 및 백업 데이터 디렉토리에 접근할 수 있는 또 하나의 DB 컨테이너(이하 Mirror )를 띄운다.
- Mirror에서 Mariabackup 명령어로 복원을 진행한다.
- 원본 컨테이너를 재시작하면, Mirror와 볼륨을 공유하므로 복원한 데이터가 원본 컨테이너에도 그대로 적용된다.

### 5-3. 호스트를 이용한 복원명령
```shell
$ mariabackup --copy-back --target-dir={증분백업까지 결합한 최종 풀 백업경로}
```

### 5-4. Docker를 이용한 복원명령
```shell
$ sudo docker exec {데이터베이스 컨테이너 이름} /usr/bin/mariabackup --copy-back --target-dir={증분백업까지 결합한 최종 풀 백업경로}
```