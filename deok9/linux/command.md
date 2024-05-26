---

mindmap-plugin: basic

---

# command

## find

## grep
- 검색 문자열 강조

	-
	  ```
	  cat /var/named/domain.zone | grep "\b1.1.1.1\b"
	  ```

		- 1.1.1.1 의 겹치는 수 모두 제외

## curl

## screen
- 생성

	-
	  ```
	  
	  screen -S jdy
	  rsync -avz /data1/* /data2/
	  ```

- 나가기

	-
	  ```
	  ctrl + a + d
	  ```

- 리스트 확인

	-
	  ```
	  screen -list
	  ```

- 재접속

	-
	  ```
	  screen -r jdy
	  ```


## sort
- 특정 열로 정렬

	-
	  ```
	  ps -ef | sort -k4
	  ```

		- - ps -ef 4번째 열의 값으로 정렬 한다

## awk

## sed
- 특정 라인 출력

	-
	  ```
	  sed -n '13p' test.txt
	  ```

		- - test.txt 파일의 13번째 줄을 출력 한다