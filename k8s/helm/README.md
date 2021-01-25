```
helm create appName
helm upgrade --install local-chart -n helm ./
helm -n helm list
helm rollback local-chart -n helm
helm -n helm uninstall local-chart

kubectl get cm
helm history chart_name
helm upgrade chart_name --install --force ./helm_chart/
helm -n monitoring upgrade --install prometheus-stack -f values.dev.yaml ./
kubectl -n monitoring logs prometheus-adapter-858c748d58-5rslm

kubectl get --raw /apis/custom.metrics.k8s.io/v1beta1
kubectl get deploy -n monitoring prometheus-adapter -o yaml | grep dns
```

```
sudo apt install golang
echo 'export GOPATH=~/go' >> ~/.bashrc
source ~/.bashrc
mkdir $GOPATH

go get -u go.mozilla.org/sops/v3/cmd/sops
cd $GOPATH/src/go.mozilla.org/sops/
git checkout develop
make install

sops -e -gpg <key-gpg> <file>
```

```
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo add stable https://charts.helm.sh/stable
helm repo update
helm search repo <name>
helm  pull prometheus-community/kube-prometheus-stack
tar zxf kube-prometheus-stack-12.4.0.tgz

helm pull prometheus-community/prometheus-adapter
```

EFK (ELK)
```


```
