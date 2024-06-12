---

mindmap-plugin: basic

---

# bash script

## dump.sh

-
  ```
  
  #!/bin/bash
  
  
  # Copyright:: Copyright (c) 2015 Been Kyung-yoon (http://www.php79.com/)
  # License:: The MIT License (MIT)
  # Version:: 1.0.0
  
  
  # rsnapshot 원격 증분 백업시, MySQL 백업 설정
  DB_USER='root'              # MySQL 사용자.  모든 디비를 백업하려면 root 계정 필요(기본값)
  DB_PASS=''      # MySQL 비밀번호.
  DB_HOST='localhost'         # MySQL 서버 주소.  별도 서버에 분리되지 않았다면 로컬 서버는 localhost 입력.
  DB_BIN='/devp/app/mariadb/bin'           # mysql, mysqldump 실행 파일의 경로.  기본 /usr/bin , 컴파일시 /usr/local/mysql/bin 등
  DATE=`date +"%Y%m%d"`
  
  
  #  주의) 본 스크립트는 rsnapshot 전용으로, 백업 실행시 현재 디렉토리에서 *sql.gz 백업을 지우고 있습니다.
  #   따라서, [로컬 풀 백업](docs/local-full-backup.md)을 원하실 경우 php79-backup.sh 를 사용해야 합니다.
  
  
  # 메세지/로그 - 에러
  function php79_error
  {
  echo "error: $1"
  logger "php79-backup error: $1"
  }
  
  
  # 메세지/로그 - 정보
  function php79_info
  {
  # Cron 데몬에서 실행되므로, 정보는 화면에 출력하지 않음.
  logger "php79-backup info: $1"
  }
  
  
  # mysql, mysqldump 경로 확인
  if [ ! -f $DB_BIN/mysql ]; then
  php79_error "[ $DB_BIN/mysql ] 파일이 존재하지 않습니다."
  exit 1
  fi
  
  
  if [ ! -f $DB_BIN/mysqldump ]; then
  php79_error "[ $DB_BIN/mysqldump ] 파일이 존재하지 않습니다."
  exit 1
  fi
  
  
  # 백업 시작
  php79_info "$0 - started"
  
  
  # 백업 보관 디렉토리
  BACKUP_DIR=`dirname $0`
  
  
  # 이전 MySQL 백업 삭제
  rm -f $BACKUP_DIR/*.sql.gz
  
  
  # MySQL 백업
  DB_LIST=$($DB_BIN/mysql -u $DB_USER --password=$DB_PASS -h $DB_HOST -e "SHOW DATABASES;" | grep -Ev "(Database|information_schema|performance_schema)")
  if [ "$?" != "0" ]; then
  php79_error "mysql 접속이 실패하였습니다."
  php79_error "[command] $DB_BIN/mysql -u $DB_USER --password=**** -h $DB_HOST -e \"SHOW DATABASES;\""
  #exit 1 # 디비 접속 장애시에도, /home 등 나머지는 백업 진행되어야 함.
  fi
  for db in $DB_LIST
  do
  $DB_BIN/mysqldump -u $DB_USER --password=$DB_PASS -h $DB_HOST --max_allowed_packet=512M \
  --default-character-set=utf8 --opt --skip-lock-tables --single-transaction -Q -B --routines --triggers $db \
  | gzip > $BACKUP_DIR/$db.sql.gz
  if [ "$?" != "0" ]; then
  php79_error "[ $db ] mysqldump 작업이 실패하였습니다."
  fi
  done
  # 백업 완료 - 크론 데몬에서 실행되므로, 완료시 별도 메세지 출력안함.
  php79_info "$0 - completed successfully"
  
  ```


## cmd.sh

-
  ```
  
  # CMD Log
  HISTTIMEFORMAT="%Y-%m-%d [%H:%M:%S] "
  export HISTTIMEFORMAT
  export MALLOC_CHECK_=0
  function history_to_syslog() {
  declare USERCMD
  USERCMD=$(fc -ln -0 2>/dev/null|sed 's/\t //')
  declare PP
  if [ "$USER" == "root" ]
  then
  PP="]#"
  else
  PP="]$"
  fi
  if [ "$USERCMD" != "$OLD_USERCMD" ]
  then
  if [ "$(echo $LANG)" == "C" ]
  then
  logger -p local4.notice -t bash -i "$USER$(who am i|awk '{print $6}'):$PWD$PP $USERCMD"
  else
  logger -p local4.notice -t bash -i "$USER$(who am i|awk '{print $5}'):$PWD$PP $USERCMD"
  fi
  fi
  OLD_USERCMD=$USERCMD
  unset USERCMD PP
  }
  trap 'history_to_syslog' DEBUG
  ```


## daily_syschk.sh

-
  ```
  
  #!/bin/bash
  
  
  ###########################
  ### make by jindeokyong ###
  ###########################
  
  
  ## variables
  user=jindeokyong
  date1=`date +"%F"`
  date2=`date +%F -d '7day ago'`
  path1=/home/jindeokyong
  
  
  if [ ! -z "$path1/logs" ] ; then
  mkdir -p $path1/logs
  chown $user. $path1/logs
  fi
  
  
  col1="\033[0;33m"
  col2="\033[0;0m"
  col3="\033[0;31m"
  col4="\033[0;32m"
  
  
  if grep '\ 7\.' /etc/redhat-release &> /dev/null; then
  export osver1='7'
  elif    grep '\ 8\.' /etc/redhat-release &> /dev/null; then
  export osver1='8'
  elif    grep '\ 9\.' /etc/redhat-release &> /dev/null; then
  export osver1='9'
  fi
  
  
  chk_sys1 ()
  {
  echo -e "$col1##############################\n$col2[INFO] OS"
  cat /etc/redhat-release
  echo -e "$col1##############################\n$col2[INFO] HostName"
  echo -e "`hostname`"
  echo -e "$col1##############################$col2"
  disk1=$(df -h | grep -v -i "boot\|tmpfs\|use" | awk '{print $5}'| cut -d "%" -f 1 | sort -rh | head -1)
  disk2="90"
  disk3="80"
  if [ "${disk1}" -ge "${disk2}" ] ; then
  echo -e "[INFO] Disk (${col3}Critical${col2})"
  elif [ "${disk1}" -ge "${disk3}" ] ; then
  echo -e "[INFO] Disk (${col1}Warning${col2})"
  else
  echo -e "[INFO] Disk (${col4}Normal${col2})"
  fi
  df -h
  echo -e "$col1##############################\n$col2[INFO] Memory"
  free -m
  echo -e "$col1##############################\n$col2[INFO] Uptime"
  uptime
  echo -e "$col1##############################\n$col2[INFO] Selinux"
  sestatus
  echo -e "$col1##############################\n$col2[INFO] Login IP / 3 head"
  last | grep -v "reboot\|tty\|:0\|:1" | head -3
  echo -e "$col1##############################\n$col2[INFO] Login Failed / 3 head"
  lastb | head -3
  echo -e "$col1##############################\n$col2[INFO] Firewalld"
  systemctl is-active firewalld
  echo -e "$col1##############################\n$col2[INFO] Time"
  date
  case $(rpm -qa | grep chrony |wc -l) in
  1 ) chronyc sources | grep "\^\*" ;;
  * ) ntpq -p |grep "\*" ;;
  esac
  echo -e "$col1##############################\n$col2[INFO] File Change,Modify Time"
  if [ ${osver1} == "7" ] ;then
  files="/etc/fstab /etc/passwd /etc/group /etc/hosts /etc/hosts.allow /var/log/yum.log"
  elif [ ${osver1} == "8" ] ; then
  files="/etc/fstab /etc/passwd /etc/group /etc/hosts /etc/hosts.allow /var/log/dnf.log"
  elif [ ${osver1} == "9" ] ; then
  files="/etc/fstab /etc/passwd /etc/group /etc/hosts /etc/hosts.allow /var/log/dnf.log"
  fi
  for i in $files
  do
  if [ $(stat $i | grep -i "chan" | awk '{print $2}'|awk -F"-" '{print $2}') == $(date +"%F" |awk -F"-" '{print $2}') ] || [ $(stat $i | grep -i "modi" | awk '{print $2}'|awk -F"-" '{print $2}') == $(date +"%F"|awk -F"-" '{print $2}') ]; then
  echo -e "[INFO] $i (${col3}Critical${col2})\n $(stat $i | grep -i "chan\|modi")"
  else
  echo -e "[INFO] $i (${col4}Normal${col2})\n $(stat $i | grep -i "chan\|modi")"
  fi
  done
  }
  
  
  chk_sys1 > $path1/logs/$date1.txt
  
  
  ### Delete
  find $path1/logs/ -name "$date2.txt" -exec rm -rf {} \;
  ```


## deok_v0.2.sh
- Sub title