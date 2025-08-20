---

mindmap-plugin: basic

---

# isms

## 환경
- centos 7.x
rhel 7.x
rhel 9.x
rocky 9.x

## 나머지 자잘한 것
- hosts 600
su 4750
pam.d/su auth required pam_wheel.so use_uid # wheel group 에 속해있는 계정은 su 명령어를 통해 root 패스워드 검증을 통과 한 후 로그인가능
profile TMOUT=300
sshd_config permitrootlogin no
rsyslog.conf 640
remove at
stop&disable cupsd, rpcbind, postfix # 사용하는 서버예외
/etc/skel other - read
tcp_wrapper 활성화

	-
	  ```
	  var1="/tmp /bin /sbin /etc /var /home"
	  for i in ${var1} ; do find ${var1} -type f -perm -2 -exec ls -alLd {} \; | awk '{print $1 " : " $3 " : " $4 " : " $9}' ; done
	  ```

		- world wirteable 확인

## 미사용 계정 삭제
- daemon:x:2:2:daemon:/sbin:/sbin/nologin
userdel daemon
adm:x:3:4:adm:/var/adm:/sbin/nologin
userdel -rf adm
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
yum remove cups -y && userdel -rf lp
sync:x:5:0:sync:/sbin:/bin/sync
userdel sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
userdel shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
userdel halt
operator:x:11:0:operator:/root:/sbin/nologin
userdel operator
games:x:12:100:games:/usr/games:/sbin/nologin
userdel -rf games && groupdel games
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
userdel -rf ftp
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
userdel systemd-network
polkitd:x:999:998:User for polkitd:/:/sbin/nologin
삭제못함, 시스템 인증 관련 데몬의 계정. 서비스도 살아있음. (rocky는 없음)
libstoragemgmt:x:998:996:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
서비스 살아있어서 삭제안하겠음. 9버전대 os에는 없네... 명확하지않으니 삭제보류
colord:x:997:995:User for colord:/var/lib/colord:/sbin/nologin
centos7 서비스 살아있어서 삭제안하겠음. rhel 7 9버전대 os에는 없네... 명확하지않으니 삭제보류
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
userdel -rf rpc
saned:x:996:994:SANE scanner daemon user:/usr/share/sane:/sbin/nologin
userdel -rf saned
saslauth:x:995:76:Saslauthd user:/run/saslauthd:/sbin/nologin
yum remove cyrus-sasl -y && userdel -rf saslauth
abrt:x:173:173::/etc/abrt:/sbin/nologin
setroubleshoot:x:994:991::/var/lib/setroubleshoot:/sbin/nologin
rtkit:x:172:172:RealtimeKit:/proc:/sbin/nologin
pulse:x:171:171:PulseAudio System Daemon:/var/run/pulse:/sbin/nologin
radvd:x:75:75:radvd user:/:/sbin/nologin
unbound:x:992:987:Unbound DNS resolver:/etc/unbound:/sbin/nologin
qemu:x:107:107:qemu user:/:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
usbmuxd:x:113:113:usbmuxd user:/:/sbin/nologin
geoclue:x:991:985:User for geoclue:/var/lib/geoclue:/sbin/nologin
gluster:x:990:984:GlusterFS daemons:/run/gluster:/sbin/nologin
gdm:x:42:42::/var/lib/gdm:/sbin/nologin
rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
gnome-initial-setup:x:989:983::/run/gnome-initial-setup/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin

## 패스워드 복잡성
- /etc/security/pwquality.conf

	-
	  ```
	  minlen = 9
	  dcredit = -1
	  ucredit = -1
	  lcredit = -1
	  ocredit = -1
	  ```

- /etc/pam.d/system-auth , /etc/pam.d/password-auth

	-
	  ```
	  password requisite pam_pwquality.so enforce_for_root
	  ```


## ssh banner

-
  ```
  ####################################################
  Attention: This system is being monitored.
  Illegal access may result in legal penalties.
  ####################################################
  ```

	- Sub title
		- /etc/motd , /etc/issue , /etc/issue.net
	- Sub title
		- /etc/ssh/sshd_config

			-
			  ```
			  Banner /etc/issue
			  Banner /etc/issue.net
			  ```


## 계정 잠금
- faillock

	-
	  ```
	  auth [default=die] pam_faillock.so authfail audit deny=3 unlock_time=600
	  account required pam_faillock.so
	  ```

		- /etc/system-auth
