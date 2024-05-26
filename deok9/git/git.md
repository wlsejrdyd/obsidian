---

mindmap-plugin: basic

---

# git

## 테스트 환경
- OS : RedHat Enterprise Linux 9.2
CPU : 2c
RAM : 8g
AWS EC2

## install

-
  ```
  yum install git
  ```


## config
- global

	-
	  ```
	  
	  git config --global user.name wlsejrdyd
	  git config --global user.email wlsejrdyd@gmail.com
	  git config --global --list
	  ```

		- user.name=wlsejrdyd
		user.email=wlsejrdyd@gmail.com
- repository 연결

	-
	  ```
	  
	  git init
	  git remote add origin https://github.com/wlsejrdyd/scripts
	  git remote -v
	  ```

		- origin  https://github.com/wlsejrdyd/scripts (fetch)
		origin  https://github.com/wlsejrdyd/scripts (push)
		- * init : Git 저장소를 **생성**하거나 기존 저장소를 **다시 초기화**하세요.