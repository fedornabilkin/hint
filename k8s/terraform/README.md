`main.tf` — в этом файле указываются основные параметры для инициализации провайдера Yandex.Cloud.

`vars.auto.tfvars` — в этом файле указываем публично доступные параметры, в нашем случае — регион в облаке, который будет использоваться по умолчанию.

`private.auto.tfvars` — файл с приватными параметрами, который не должен попасть в git-репозиторий, поэтому не забудьте добавить его в .gitignore. Здесь нужно указать ваши параметры, которые можно получить через yc config list.   
```bash
yc_token = "..."
yc_cloud_id = "..."
yc_folder_id = "..."
```

`network.tf` — файл, в котором мы описываем настройки virtual private cloud. Мы создаем сеть internal, а также — по одной подсети в каждой зоне доступности — ru-central1-a, ru-central1-b и ru-central1-c:

`service-accounts.tf` — файл для настройки сервисных аккаунтов. Мы создаем два аккаунта — один нужен для управления группами узлов — для их автоматического масштабирования, а второй — для авторизации в container registry — чтобы мы могли скачивать и заливать наши образы в yandex container registry.
Сервисный аккаунт — аккаунт, от имени которого программы могут управлять ресурсами в Yandex.Cloud. То есть мы можем написать приложение, которое, используя сервисный аккаунт, будет обращаться к API Yandex Cloud и управлять ресурсами — добавлять ноды в кластер, удалять или создавать базы и так далее. В данном случае нам требуется как раз аккаунт для управления виртуальными машинами в группе узлов — чтобы работало автоматическое масштабирование кластера, а также сервисный аккаунт, который сможет обращаться к container registry.
Container registry — это репозиторий, который хранит образы контейнеров. Практически тот же docker hub, только приватный в Yandex.Cloud.

`kubernetes.tf` — основной файл, в котором мы описываем кластер, а также группу узлов, которая будет к нему подключаться. Вообще кластеры бывают двух типов — зональный и региональный. Региональный дает большую отказоустойчивость, за счет создания трех машин для api server, а также размещения их в трех разных зонах доступности. Таким образом, при падении одного из серверов доступность нарушена не будет. Такой режим рекомендуется использовать в production окружениях.

Описание параметров  
https://registry.terraform.io/providers/yandex-cloud/yandex/latest/docs/resources/kubernetes_cluster

### init скачает и настроит провайдер Yandex.Cloud
`terraform init`

Запускаем создание ресурсов  
`terraform apply`

Список кластеров  
`yc container cluster list`

Токен  
`yc k8s create-token --profile=default`

Описание API  
`kubectl api-resources`  
`kubectl api-versions`

Устаналиваем конфиг для kubectl, чтобы работать с кластером  
`yc container cluster get-credentials kuber-cluster --external`  
`kubectl cluster-info`

Запрос на апи кластера  
`curl -D - -s -k -H 'Authorization: Bearer t1.9euelZrMjs-NmMidjsjIis6RkpjLx-3rnpWajc2Um8malJbOkozHypCekJzl8_d7MmEB-u83YCFo_N3z9zthXgH67zdgIWj8.uKcpweH146V-fQLbs_R2tEk7psOgxWhLy4Z1DfI-d39KuRvDoUlS99fofi2enKPJDm-MQKxLT5vpPMCOIFvCCg' https://84.201.133.155/api/`

