---

mindmap-plugin: basic

---

# apache

## config
- url replace

	-
	  ```
	  
	  <VirtualHost *:80>
	  ServerName www.deok.kr
	  Redirect / https://www2.deok.kr
	  </VirtualHost>
	  ```

		- www.deok.kr 으로 클라이언트가 web server로 접근 시 www2.deok.kr 으로 보낼 수 있음
- 보안설정
	- AllowOverride AuthConfig
		- ${APACHE_PATH}/bin/htpasswd -c passfilename userid
			- 첫 생성 시 -c 옵션을 추가해서 설정하며, 이후부터 -c 옵션은 해당 계정의 정보를 지운다
			- passfilename 에 위치한 경로에 .htaccess 파일 잊지 말고 생성 해두기
		- .htaccess

			-
			  ```
			  
			  AuthType Digest
			  AuthName "DY.JIN PAGE, Get out now!"
			  AuthUserFile /devp/app/apache/htdocs/deokpass
			  order deny,allow
			  deny from all
			  allow from ALLOW_IP
			  <Limit GET POST>
			  require valid-user
			  </Limit>
			  ```

				- {HOME}/.htaccess 파일을 생성하여 추가하면 됨
	- web server ip 사용차단

		-
		  ```
		  
		  <VirtualHost *:80>
		  DocumentRoot /home/source/public_html
		  ServerName x.x.x.x
		  Redirect 403 /
		  ErrorDocument 403 "nope"
		  UseCanonicalName Off
		  UserDir Disabled
		  </VirtualHost>
		  ```

			- web server ip 로 접근하는 방법을 차단한다
	- https redirect

		-
		  ```
		  
		  RewriteEngine On
		  RewriteCond %{HTTPS} !=on
		  RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R,L]
		  ```

	- path control

		-
		  ```
		  <Directory />
		  Require all denied
		  Require ip x.x.x.x
		  </Directory>
		  ```

			- url path / 아래의 접근을 ip 로 제어함
	- extension control

		-
		  ```
		  <Files ".html">
		  Require all denied
		  Require ip x.x.x.x
		  </Files>
		  ```

			- files (확장자) 단위를 지정하며 ip 로 제어한다
			- 다른 제시어로는 예를 들어 <Files ~ ".(htm|css|js|php)$"> 등으로 제어 할 수 있다
	- directory control

		-
		  ```
		  
		  <Location  /aaa/bbb>
		  Require all denied
		  Require ip x.x.x.x
		  </Location>
		  ```

			- www.deok.kr/aaa/bbb 의 디렉터리를 ip 로 제어한다
- downloading index.html

	-
	  ```
	  
	  AddType application/x-httpd-php .php4 .php .html .htm .inc
	  AddType application/x-httpd-php-source .phps
	  ```

		- index.html 이 표시가 안되고 계속 다운로드만 된다면 해당 옵션을 확인해볼 것
		- AddType application/x-compress .Z 활성화되어있는지 확인
- semaphore 설정

	-
	  ```
	  echo 250 32000 100 512 > /proc/sys/kernel/sem && echo 512 32000 512 512 > /proc/sys/kernel/sem
	  ```

		- 접속자 수 증가로 웹 서버 부하가 발생하면 느려지거나 증상 없이 간혈적으로 멈추곤 할 때 설정을 임시적으로 변경하여 완화 할 수 있다

## error
- 403 error

	-
	  ```
	  <Directory />
	  ~
	  ~
	  ~
	  Order allow,deny
	  Allow from all
	  Require all granted
	  </Directory>
	  ```