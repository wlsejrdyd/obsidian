---

mindmap-plugin: basic

---

# disk

## 속도체크
- dd

## 디스크확장
- parted

	-
	  ```
	  
	  parted /dev/sdc1 resizepart [start] [end]
	  resize2fs /dev/sdc1
	  ```

		- - 명령어는 마운트 되어있는 상태로 진행 가능하며, 서비스에 지장 없다는 것을 확인함
		- - 시작점과 끝점은 parted /dev/sdc1 print 명령으로 확인 가능하다
- lvm
	- Sub title