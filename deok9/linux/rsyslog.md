---

mindmap-plugin: basic

---

# rsyslog

## 환경
- CentOS 7.9

## rsyslog.conf
- Log Server

	-
	  ```
	  
	  $ModLoad imuxsock # provides support for local system logging (e.g. via logger command)
	  $ModLoad imjournal # provides access to the systemd journal
	  $ModLoad imtcp
	  $InputTCPServerRun 514
	  $WorkDirectory /var/lib/rsyslog
	  $ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat
	  $IncludeConfig /etc/rsyslog.d/*.conf
	  $OmitLocalLogging on
	  $IMJournalStateFile imjournal.state
	  *.info;mail.none;authpriv.none;cron.none;local4.none;local5.none                /var/log/messages
	  authpriv.*                                              /var/log/secure
	  mail.*                                                  -/var/log/maillog
	  cron.*                                                  /var/log/cron
	  *.emerg                                                 :omusrmsg:*
	  uucp,news.crit                                          /var/log/spooler
	  local7.*                                                /var/log/boot.log
	  local4.notice   /var/log/cmd.log
	  $template FILENAME1,"/remote_log/%fromhost-ip%/%$YEAR%/%$MONTH%/%$DAY%/system.log"
	  local5.* ?FILENAME1
	  ```

- Host Server
	- Sub title