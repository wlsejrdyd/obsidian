---

mindmap-plugin: basic

---

# rsyslog

## 로그서버 구성
- 환경
	- CentOS 7.9
	- Log Server 192.168.57.1
	Test Server 192.168.57.2
- rsyslog.conf
	- Log Server

		-
		  ```
		  
		  $ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
		  $ModLoad imjournal # provides access to the systemd journal
		  $ModLoad imudp
		  $UDPServerRun 514
		  $ModLoad imtcp
		  $InputTCPServerRun 514
		  $WorkDirectory /var/lib/rsyslog
		  $ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
		  $IncludeConfig /etc/rsyslog.d/*.conf
		  $OmitLocalLogging on
		  $IMJournalStateFile imjournal.state
		  *.info;mail.none;authpriv.none;cron.none;local4.none;local5.none /var/log/messages
		  authpriv.* /var/log/secure
		  mail.* -/var/log/maillog
		  cron.* /var/log/cron
		  *.emerg :omusrmsg:*
		  uucp,news.crit /var/log/spooler
		  local7.* /var/log/boot.log
		  local4.notice /var/log/cmd.log
		  $template FILENAME1,"/remote_log/%fromhost-ip%/%$YEAR%/%$MONTH%/%$DAY%/system.log"
		  local5.* ?FILENAME1
		  ```

	- Test Server

		-
		  ```
		  
		  $ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
		  $ModLoad imjournal # provides access to the systemd journal
		  $ModLoad imfile
		  $WorkDirectory /var/lib/rsyslog
		  $ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
		  $IncludeConfig /etc/rsyslog.d/*.conf
		  $OmitLocalLogging on
		  $IMJournalStateFile imjournal.state
		  *.info;mail.none;authpriv.none;cron.none;local4.none;local5.none /var/log/messages
		  authpriv.* /var/log/secure
		  mail.* -/var/log/maillog
		  cron.* /var/log/cron
		  *.emerg :omusrmsg:*
		  uucp,news.crit /var/log/spooler
		  local7.* /var/log/boot.log
		  local4.notice /var/log/cmd.log
		  local5.* @@192.168.57.1
		  ```

			- UDP 사용 시 @ 1개
			- TCP 사용 시 @@ 2개
			- UDP 사용 시 tcpdump 로 내용 확인 가능함
- /etc/rsyslog.d/jdy1.conf
	- Test Server

		-
		  ```
		  
		  $InputFileName /var/log/{cmd.log,secure,messages}
		  $InputFileTag cmd-:
		  $InputFileStateFile stat-file1
		  $InputFileSeverity info
		  $InputFileFacility local5
		  $InputRunFileMonitor
		  ```


## sftp log config
- sshd_config

	-
	  ```
	  
	  Subsystem      sftp     /usr/libexec/openssh/sftp-server -f local2 -l INFO
	  ```

- rsyslog.conf

	-
	  ```
	  
	  local2.* /var/log/.sftp.log
	  ```


## command history log
- cmd.sh
- Sub title