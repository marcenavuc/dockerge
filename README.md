# Лаба 4

Сборка образов 
```bash
docker compose build
```
![Docker build](photos/docker_compose_build.png)

Добавление образов в minikube
```bash
minikube image load dockerge-interface dockerge-streamlit
```
![Minikube load](photos/minikube_load.png)

Применение манифестов 
```bash
kubectl apply -f manifests
```
![Kubectl apply](photos/kubectl_apply.png)

Проверка, что запустилось
```bash
kubectl get pods
kubectl get deployment
kubectl get services
```
![Kubectl get](photos/kubectl_get_new.png)

Проверка подключения к сервису
```bash
minikube service interface-service
curl -X POST "http://127.0.0.1:53698/ask/" \
-H "Content-Type: application/json" \
-d '{
  "question": "Что такое НПА?"
}'
```
![Intreface logs](photos/interface_logs.png)
Вообщем-то вывод правильный, потому что эмбедер и прочее не загружены, так и должно быть, это мок)


Дашборд minikube
```bash
minikube dashboard --url
```
![Minikube dash](photos/minikube_dash_new.png)
