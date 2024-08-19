---

mindmap-plugin: basic

---

# Kubernetes

## Master node pods 실행 방법

-
  ```
  kubectl describe node master | grep Taints
  ```

	- kubectl taint nodes --all node-role.kubernetes.io/control-plane:NoSchedule 뒤에 빼기 붙혀야 함...