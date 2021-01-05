Установка  
https://kubernetes.io/ru/docs/tasks/tools/install-kubectl/

```
sudo apt-get update && sudo apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

`kubectl cluster-info`

`kubectl config get-contexts`

`kubectl config use-context yc-kub-test`

#### Основные команды:

`get` — получает ресурс из API-сервера, по умолчанию — в сокращенном формате, -o задает формат;  
`describe` — описывает ресурс в выбранном формате;  
`create` — создает ресурс из файла или с указанием типа и параметров;  
`delete` — удаляет ресурс по выбору или его имени;  
`edit` — открывает редактор и загружает в него файл описания ресурса, при сохранении отправляет измененный ресурс API-серверу;  
`explain` — показывает документацию по ресурсу;  
`patch` — вносит изменения в поля ресурса, используя стратегию слияния патча;  
`proxy` — запускает локальный прокси-сервер до API-сервера Kubernetes;  
`replace` — заменяет ресурс новой версией;  
`logs` — выдает логи ресурса;  
`expose` — создает сервис для pod, rs, rc;  
`port-forward` — прокидывает локальный порт на порт внутри контейнера pod;  
`run` — запускает pod из указанного образа (image);  
`scale` — изменяет spec.replicas;  
`rolling-update` — выполняет обновление, клиентом будет kubectl на хосте.

```
kubectl get namespaces
kubectl get nodes -o wide
kubectl get node cl128p75cket5bspv91v-oles -o  yaml
```

Также kubectl поддерживает формат шаблонов [golang templates](https://golang.org/pkg/text/template/#pkg-overview).

Все labels у каждой ноды в кластере:  
`kubectl get nodes -o go-template --template='{{ range .items }}{{ printf "%s\n" .metadata.name }}{{ range $key, $value := .metadata.labels }}{{ printf "  %s = %s\n" $key $value }}{{ end }}{{ end }}'`

Посмотреть образы контейнеров, запущенных в кластере:  
`kubectl get pods -n test -o go-template --template='{{range .items}}{{range .spec.containers}}{{printf "%s\n" .image}}{{end}}{{end}}'`

Вывести для pod аннотации app.kubernetes.io/instance, у которых количество рестартов первого контейнера более 100.  
`kubectl get pods --all-namespaces -o go-template --template='{{range .items}}{{if gt (index .status.containerStatuses 0).restartCount 100.0}}{{ printf "%s\n" (index .metadata.labels "app.kubernetes.io/instance")}}{{end}}{{end}}'`

```bash
kubectl run nginx --image nginx
kubectl describe pod nginx
kubectl label pod nginx test=true
kubectl get pod nginx -o go-template --template='{{ range $key, $value := .metadata.labels }}{{ printf " %s = %s\n" $key $value }}{{ end }}'
```

Команда, чтобы под не делал рестарт
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu
spec:
  containers:
  - name: ubuntu
    image: ubuntu:latest
    # Just spin & wait forever
    command: [ "/bin/bash", "-c", "--" ]
    args: [ "while true; do sleep 30; done;" ]
```

```bash
kubectl exec nginx -- id
kubectl exec -it nginx -- sh
kubectl logs nginx
kubectl delete pod nginx
```

команды cordon, drain и uncordon для запрещения размещения подов на ноде и переноса подов на другие ноды.  
```
kubectl cordon cl128p75cket5bspv91v-oles
kubectl drain cl128p75cket5bspv91v-oles --ignore-daemonsets
kubectl uncordon cl128p75cket5bspv91v-oles

kubectl get podmetrics
kubectl get nodemetrics
kubectl get podmetrics nginx -o yaml

kubectl top pods --all-namespaces
kubectl top nodes
```

```
yc container cluster list
cat ~/.kube/config
yc k8s create-token --profile=default
curl -D - -s -k -H 'Authorization: Bearer t1.8euelZqZl8rJxszLiZDNk6fNkZGdze3rnpWaxsmSnI_Ijc6MjMrNzJbMmpbl8_cNAzIA-u8nQkRU_d3z900xLwD67ydCRFT9.QYGp8iCLwNStqqCZhYzbjD9kG0AkMNocQ6tl8UbeoQoRMVwJNiSVOzNPoeG7wA6uDfy98f5Q2-csEFfc9vPRBA' https://84.201.159.209/api/v1/pods | jq .
curl -s -k -H 'Authorization: Bearer t1.8euelZqZl8rJxszLiZDNk6fNkZGdze3rnpWaxsmSnI_Ijc6MjMrNzJbMmpbl8_cNAzIA-u8nQkRU_d3z900xLwD67ydCRFT9.QYGp8iCLwNStqqCZhYzbjD9kG0AkMNocQ6tl8UbeoQoRMVwJNiSVOzNPoeG7wA6uDfy98f5Q2-csEFfc9vPRBA' https://84.201.159.209/api/v1/namespaces/default/pods/nginx-pod | jq .
```

```
yc vpc subnet list
```

```
kubectl create namespace test
kubectl apply -f deployment.yaml

```
