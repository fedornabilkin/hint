###Качаем миникуб
https://storage.googleapis.com/minikube/releases/latest/minikube-installer.exe
https://kubernetes.io/docs/tasks/tools/install-minikube/

Проверяем наличие поддержки виртуализации   
`systeminfo`

Включаем Hyper-V   
`Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All`

Предварительно положить файл kubectl.exe в директорю windows, затем.
```
minikube start
minikube kubectl
minikube config set vm-driver virtualbox
```

```
kubectl claster-info
kubectl cluster-info | grep dashboard
minikube dashboard
minikube logs
```

`kubectl cluster-info dump`

`kubectl completion zsh`

```
kubectl run kubia --image=luksa/kubia --port=8080 --generator=run/v1
kubectl expose rc kubia --type=LoadBalancer --name kubia-http
minikube service kubia-http
```

```
kubectl run unhealthy --image=luksa/kubia-unhealthy --port=8080 --generator=run/v1
kubectl expose rc unhealthy --type=LoadBalancer --name unhealthy-http
minikube service unhealthy-http
```

```
kubectl scale rc kubia --replicas=3
kubectl get rc
kubectl get po
```

```
kubectl get pods -o wide
kubectl describe pod <NAME>
kubectl explain pods
```

```
NAME          READY   STATUS    RESTARTS   AGE     IP           NODE       NOMINATED NODE   READINESS GATES
kubia-4h6dt   1/1     Running   0          105s    172.17.0.9   minikube   <none>           <none>
kubia-jk2nx   1/1     Running   0          21m     172.17.0.6   minikube   <none>           <none>
kubia-mbdvz   1/1     Running   0          4m12s   172.17.0.7   minikube   <none>           <none>
```

```
kubectl create -f kubia-manual.yaml
kubectl logs pln-manual // посмотреть логи пода
kubectl logs pln-manual -c pln // логи конкретного конетейнера по имени контейнера
kubectl logs mypod --previous // логи предыдущего конетейнера
```

`kubectl port-forward pln-manual 8888:8080`


```
kubectl get po --show-labels
kubectl get po -L app,env
kubectl label po pln-manual creation_method=manual
kubectl label po pln-manual creation_method=man2 --overwrite
kubectl get po -l creation_method=manual
kubectl get po -l env
kubectl get po -l '!env'
```

```
kubectl label node <node-name> gpu=true
kubectl label node minikube disk=hdd --overwrite
kubectl get nodes -l gpu=true
```

`kubectl annotate pod pln-manual pln.ru/description="plohoneponyal"`

```
kubectl get ns
kubectl get po --namespace kube-system // -n
kubectl create namespace pln-namespace
kubectl create -f pln-manual.yaml -n pln-namespace
```

```
kubectl delete po -l creation_method=manual
kubectl delete ns pln-namespace // удаляет пространство и все поды
kubectl delete po --all
kubectl delete all --all // удаляет все ресурсы в пространстве
```

`kubectl delete rc kubia --cascade=false` // не удалит поды контроллера

```
kubectl get rs
kubectl describe rs
```

```
kubectl get svc
kubectl exec pln-77gwh -- curl -s http://10.107.150.173 // запрос из контейнера пода
kubectl exec -it kubia-3inly bash
while true; do curl http://10.107.218.131; done
```

`kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type=="ExternalIP")].address}'`

### deplpoyment   
```
kubectl patch deployment pln-deployment -p '{"spec":{"template": {"spec": {"containers":[{"name": "nodejs", "image": "luksa/kubia:v2"}]}}}}'
kubectl patch deployment pln-deployment -p '{"spec":{"minReadySeconds":10}}'
kubectl patch deployment pln-deployment -p '{"spec":{"replicas":5}}'
kubectl set image deployment pln-deployment nodejs=luksa/kubia:v2
kubectl rollout status deployment pln-deployment
kubectl rollout undo deployment pln-deployment // Эта команда возвращает развертывание к предыдущей редакции.
kubectl rollout history deployment pln-deployment
kubectl rollout undo deployment pln-deployment --to-revision=1

RollingUpdate - плавное обновление, когда новая версия с обратной совместимостью
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
Recreate - небольшой простой, когда новая версия ломает старую
```















