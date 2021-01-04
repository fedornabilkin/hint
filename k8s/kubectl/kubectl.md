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

```bash
kubectl exec nginx -- id
kubectl exec -it nginx -- sh
kubectl logs nginx
kubectl delete pod nginx
```

команды cordon, drain и uncordon
