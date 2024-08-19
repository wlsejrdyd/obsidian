---

mindmap-plugin: basic

---

# Docker

## Registry
- Private Registry 생성

	-
	  ```
	  docker container run -d -p 5000:5000 --name registry registry
	  ```

- Registry 확인

	-
	  ```
	  curl -X GET http://localhost/v2/_catalog
	  ```