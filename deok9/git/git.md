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
		- init : Git 저장소를 **생성**하거나 기존 저장소를 **다시 초기화**하세요.
		- remote add origin : 연결할(될) URL 주소 명시.

## test
- 업로드(push)

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

				- remote: Support for password authentication was removed on August 13, 2021.
					- commit 이후 push 명령 실행시 위와 같은 에러가 나온다면 [SSH Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-fine-grained-personal-access-token) 을 발급해야 함
						- **"세분화된 개인 액세스 토큰 만들기"** 로 진행함
						- 생성한 토큰값은 저장해 놓을 것
						- 저장소 권한 도 수정해 줘야 함
				- "./{filename}" 을 명시하지 않고 "." 를 입력할 경우 전체 파일을 선택하는 것이며, git rm --cached <file> 명령어로 등록 된 파일을 지울 수도 있다.

					-
					  ```
					  git rm --cached .bash_profile .bashrc .cshrc .gitconfig .lesshst .ssh/authorized_keys .tcshrc
					  ```

						- rm '.bash_profile'
						rm '.bashrc'
						rm '.cshrc'
						rm '.gitconfig'
						rm '.lesshst'
						rm '.ssh/authorized_keys'
						rm '.tcshrc'

							-
							  ```
							  git status
							  ```

								- On branch master
								void
								No commits yet
								void
								Changes to be committed:
								(use "git rm --cached <file>..." to unstage)
								new file:   .bash_logout
								new file:   update_file_01
								void
								Untracked files:
								(use "git add <file>..." to include in what will be committed)
								.bash_history
								.bash_profile
								.bashrc
								.cshrc
								.gitconfig
								.lesshst
								.ssh/
								.tcshrc
- 복제(clone)

	-
	  ```
	  
	  git clone https://github.com/wlsejrdyd/deok
	  ```

		- Cloning into 'deok'...
		remote: Enumerating objects: 109, done.
		remote: Counting objects: 100% (29/29), done.
		remote: Compressing objects: 100% (29/29), done.
		remote: Total 109 (delta 5), reused 0 (delta 0), pack-reused 80
		Receiving objects: 100% (109/109), 1.11 MiB | 25.81 MiB/s, done.
		Resolving deltas: 100% (21/21), done.
			- 업로드 이후 첫 `clone` 은 문제가 없음. **(이미 디렉터리가 있으니까..)**, `pull`을 이용해 내용만을 갱신한다

				-
				  ```
				  
				  git clone https://github.com/wlsejrdyd/deok
				  ```

					- fatal: destination path 'deok' already exists and is not an empty directory.
- pull

	-
	  ```
	  
	  git pull https://github.com/wlsejrdyd/deok
	  ```

		- remote: Enumerating objects: 115, done.
		remote: Counting objects: 100% (35/35), done.
		remote: Compressing objects: 100% (32/32), done.
		remote: Total 115 (delta 7), reused 3 (delta 1), pack-reused 80
		Receiving objects: 100% (115/115), 1.11 MiB | 26.43 MiB/s, done.
		Resolving deltas: 100% (23/23), done.
		From https://github.com/wlsejrdyd/deok
		- branch            HEAD       -> FETCH_HEAD
		hint: You have divergent branches and need to specify how to reconcile them.
		hint: You can do so by running one of the following commands sometime before
		hint: your next pull:
		hint:
		hint:   git config pull.rebase false  # merge
		hint:   git config pull.rebase true   # rebase
		hint:   git config pull.ff only       # fast-forward only
		hint:
		hint: You can replace "git config" with "git config --global" to set a default
		hint: preference for all repositories. You can also pass --rebase, --no-rebase,
		hint: or --ff-only on the command line to override the configured default per
		hint: invocation.
		fatal: Need to specify how to reconcile divergent branches.
			- 쉘은 잘 떨어졌는데 저장소에 `새로만든 파일이 안보인다

				-
				  ```
				  git pull https://github.com/wlsejrdyd/deok --ff-only
				  ```

					- [참조1](https://woongchoi84.github.io/2020/01/15/post-productive-howtousegithub.html)

				-
				  ```
				  
				  git pull https://github.com/wlsejrdyd/deok rebase
				  ```

					- [참조2](https://velog.io/@eunddodi/git-pull-%EC%8B%9C-%EB%B0%9C%EC%83%9D%ED%95%98%EB%8A%94-warning-%ED%95%B4%EA%B2%B0%ED%95%98%EA%B8%B0Need-to-specify-how-to-reconcile-divergent-branches)

				-
				  ```
				  git pull https://github.com/wlsejrdyd/deok ./deok
				  ```