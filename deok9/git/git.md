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

		- origin  https://github.com/wlsejrdyd/scripts (fetch)
		origin  https://github.com/wlsejrdyd/scripts (push)
		- - init : Git 저장소를 **생성**하거나 기존 저장소를 **다시 초기화**하세요.
		- * remote add origin : 연결할(될) URL 주소 명시.
- 업로드(push) 테스트

	-
	  ```
	  
	  echo "Upload test file 01" > update_file_01
	  git add ./update_file_01
	  git status
	  ```

		- On branch master
		void
		No commits yet
		void
		Changes to be committed:
		(use "git rm --cached <file>..." to unstage)
		new file:   update_file_01

			-
			  ```
			  
			  git commit -m "write here history comment"
			  git push
			  ```

				- * remote: Support for password authentication was removed on August 13, 2021.
					- * commit 이후 push 명령 실행시 위와 같은 에러가 나온다면 [SSH Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token) 을 발급해야 함
						- * **"세분화된 개인 액세스 토큰 만들기"** 로 진행함
						- * 생성한 토큰값은 저장해 놓을 것
						- * 저장소 권한 도 수정해 줘야 함