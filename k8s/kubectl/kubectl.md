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
kubectl get nodes -o wide
kubectl get node cl128p75cket5bspv91v-oles -o  yaml

kubectl get nodes
kubectl get namespaces
kubectl get deploy
kubectl get po
kubectl get svc // services
kubectl get sa // service account
kubectl get hpa // horizontal pod autoscaler

kubectl get podmetrics
kubectl get nodemetrics

kubectl get configmaps
kubectl get secret

kubectl get sc // storageClass
kubectl get pv
kubectl get pvc
kubectl get sts
kubectl get crd

kubectl get networkpolicy
kubectl get resourcequota

kubectl get role
kubectl get rolebinding
kubectl get clusterrole
kubectl get clusterrolebinding
```

Create service account  
`kubectl create sa saname`

Также kubectl поддерживает формат шаблонов [golang templates](https://golang.org/pkg/text/template/#pkg-overview).

Все labels у каждой ноды в кластере:  
```
kubectl get nodes -o go-template --template='{{ range .items }}{{ printf "%s\n" .metadata.name }}{{ range $key, $value := .metadata.labels }}{{ printf "  %s = %s\n" $key $value }}{{ end }}{{ end }}'
```

Посмотреть образы контейнеров, запущенных в кластере:  
```
kubectl get pods -n test -o go-template --template='{{range .items}}{{range .spec.containers}}{{printf "%s\n" .image}}{{end}}{{end}}'
```

Вывести для pod аннотации app.kubernetes.io/instance, у которых количество рестартов первого контейнера более 100.  
```
kubectl get pods --all-namespaces -o go-template --template='{{range .items}}{{if gt (index .status.containerStatuses 0).restartCount 100.0}}{{ printf "%s\n" (index .metadata.labels "app.kubernetes.io/instance")}}{{end}}{{end}}'
```

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

```
kubectl get pod $pod -o yaml
kubectl annotate pod $pod mypod="super"
```
```
kubectl taint node cl1s8cp7jdh3nbc4fsj2-yhec dedicated=true:NoSchedule
```

#### Configmap and secret
```
kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2
kubectl create configmap game-config --from-file=myconfig
kubectl get configmaps my-config -o yaml

kubectl create configmap my-config \
    --from-file=foo.json \
    --from-file=bar=foobar.conf \
    --from-file=config-opts/ \
    --from-literal=some=thing

kubectl create secret generic my-password --from-literal=password=mysqlpassword
kubectl create secret generic nginx-env --from-literal=AUTH=testpassword
kubectl get secret my-password
kubectl get secret my-password -o yaml
```

```
kubectl create secret generic regcred --from-file=.dockerconfigjson=<path/to/.docker/config.json> --type=kubernetes.io/dockerconfigjson
kubectl create secret docker-registry regcred --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password=<your-pword> --docker-email=<your-email>
```

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.41.2/deploy/static/provider/cloud/deploy.yaml
kubectl -n ingress-nginx get deploy
kubectl -n ingress-nginx get svc
curl -D - -s -o /dev/null -H 'Host: blue.example.com' http://84.201.133.236/
```

`htpasswd -c auth username`

New password:  
Re-type new password:  
Adding password for user username

`kubectl create secret generic basic-auth --from-file=auth`

secret/basic-auth created

`kubectl get secret basic-auth -o yaml`

apiVersion: v1  
data:  
  auth: dm96ZXJvdjokYXByMSRUb2VWRy9TLyRlM2dkUzZDdFFKWTltMk9lNzFqWXcwCg==

```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.1.0/cert-manager.yaml
```
