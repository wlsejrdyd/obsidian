---

mindmap-plugin: basic

---

# Docker

## Registry
- Private Registry Image 삭제 요청

	-
	  ```
	  curl -X DELETE https://localhost/v2/_catalog/<repository>:<tag>

		- Sub title

	-
	  ```
	  docker pull localhost:5000/node01:1.0


	-
	  ```
	  docker inspect --format='{{index .RepoDigests 0}}' localhost:5000/node01:1.0

- Private Registry 생성

	-
	  ```
	  docker container run -d -p 5000:5000 --name registry registry

- Registry 확인

	-
	  ```
	  curl -X GET http://localhost/v2/_catalog