---

mindmap-plugin: basic

---

# Docker

## Private Registry Image 삭제 요청

-
  ```
  curl -X DELETE https://localhost/v2/<registry>/<repository>:<tag>
  ```


## Private Registry 생성

-
  ```
  docker container run -d -p 5000:5000 --name registry registry
  ```