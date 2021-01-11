###Установка yandex cli  
`curl https://storage.yandexcloud.net/yandexcloud-yc/install.sh | bash`

Выход из консоли
`loagout` or `exit`

Инициализация (надо перейти по ссылке и получить токен авторизиации, затем ввести его)   
`yc init`

Выбрать дефолтную директорию, установить дефолтную зону следуя подсказкам

Смотрим списки
```
yc compute instance list
yc config profile list
yc vpc network list
```

Смотрим данные облака `yc config list`
```
token: ...
cloud-id: ...
folder-id: ...
compute-default-zone: ru-central1-b
```

Создаем профиль
```
yc config profile create test-profile
yc config profile create service-account-rebrain
```

Переключаемся на профиль   
`yc config profile activate default`

Устанавливаем ключ, клауд и фолдер профилю
```
yc config set service-account-key key.json
yc config set cloud-id b2gehvlhu32hpuc0tl12
yc config set folder-id b1gtca688p1cbjjh88fr
```

Удалить профиль default   
`yc config profile delete default`

Сервисный аккаунт с правами на чтение folder и генрация ключа с сохранением в файл
```
yc iam service-account create --name rebrain
yc resource-manager folder add-access-binding b1gtca688p1cbjjh88fr --role=viewer --service-account-name=rebrain
yc iam key create --service-account-name rebrain --output key.json
```

`yc resource-manager folder add-access-binding FOLDER_ID --role=k8s.admin --service-account-name=rebrain`

`yc container cluster get-credentials kuber-cluster --external`















