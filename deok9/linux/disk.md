---

mindmap-plugin: basic

---

# disk

## 속도체크
- dd
	- 쓰기 테스트

		-
		  ```
		  
		  time dd if=/dev/zero of=/var/test bs=8k count=1000000
		  ```

			- 1000000+0 records in
			1000000+0 records out
			8192000000 bytes (8.2 GB) copied, 8.92194 s, 918 MB/s
			real    0m8.924s
			user    0m0.151s
			sys     0m8.763s
	- 읽기 테스트

		-
		  ```
		  
		  time dd if=/var/test of=/dev/null bs=8k count=1000000
		  ```

			- 1000000+0 records in
			1000000+0 records out
			8192000000 bytes (8.2 GB) copied, 9.4147 s, 870 MB/s
			real    0m9.420s
			user    0m0.153s
			sys     0m6.676s
	- 읽기,쓰기 테스트

		-
		  ```
		  
		  time dd if=/var/test of=/tmp/test bs=8k count=1000000
		  ```

			- 1000000+0 records in
			1000000+0 records out
			8192000000 bytes (8.2 GB) copied, 17.7223 s, 462 MB/s
			real    0m17.727s
			user    0m0.209s
			sys     0m14.241s

## 디스크확장
- parted

	-
	  ```
	  
	  parted /dev/sdc1 resizepart [start] [end]
	  ```

		- 명령어는 마운트 되어있는 상태로 진행 가능하며, 서비스에 지장 없다는 것을 확인함
		- 시작점과 끝점은 parted /dev/sdc1 print 명령으로 확인 가능하다
- lvm

	-
	  ```
	  
	  lvmextend -L+1G /dev/vo/lvdata
	  ```

- /etc/fstab 갱신
- xfs filesystem 은 별도의 xfs_growfs 명령어가 필요함