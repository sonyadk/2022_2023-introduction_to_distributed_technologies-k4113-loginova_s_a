University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Loginova Sofia Andreevna 

Lab: Lab3

Date of create: 18.11.2022

Date of finished: Not yet

## Ход работы

### Создаем ConfigMap 
Запускаем ```minikube```  ```minikube start```. Далее создаем ```ConfigMap.yaml```

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: frontend-configmap
data:
  react_app_user_name: "Sofia"
  react_app_company_name: "ITMO"
```
Выполняем ```kubectl create -f ConfigMap.yaml``` и проверяем, что появился ```ConfigMap``` командой ```kubectl get configmaps```.

![image](https://user-images.githubusercontent.com/52317905/209140821-2c9c0361-6908-4bbc-86d6-57cabaa40341.png)

### Создаем ReplicaSet

Создаем ```ReplicaSet``` с 2 репликами контейнера ```ifilyaninitmo/itdt-contained-frontend:master```

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend-replicaset
  labels:
    app: lab3-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab3-frontend
  template:
    metadata:
      labels:
        app: lab3-frontend
    spec:
      containers:
      - name: frontend-container
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          valueFrom:
            configMapKeyRef:
              name: frontend-configmap
              key: react_app_user_name
        - name: REACT_APP_COMPANY_NAME
          valueFrom:
            configMapKeyRef:
              name: frontend-configmap
              key: react_app_company_name
```

![image](https://user-images.githubusercontent.com/52317905/209143456-031e7208-ade9-405b-a814-b6699b7b032d.png)

### Создаем сервис

Создаем сервис командой ```kubectl create -f service.yaml```

```yaml 
apiVersion: v1
kind: Service
metadata:
  name: lab3-service
  labels:
    app: lab3-frontend
spec:
  type: NodePort
  selector:
    app: lab3-frontend
  ports:
    - protocol: TCP
      port: 3000
      targetPort: 3000
      nodePort: 30500
```
![image](https://user-images.githubusercontent.com/52317905/209145372-dd98ccab-a8b5-4caa-a5bc-3a1a674f9c84.png)

Далее начинаются танцы вокруг создания сертификата.

Генерируем приватный ключ ```openssl genrsa -out lab3.key 2048```.

Отправляем ключ на подпись ```openssl req -key lab3.key -new -out lab3.csr```. В ```Common Name``` пишем доменное имя, по которому будем с помощью ```Ingress``` заходить на сервер: ```lab3.sofia```

Подписываем созданным ключом ```openssl x509 -signkey lab3.key -in lab3.csr -req -days 30 -out lab3.crt```.

![image](https://user-images.githubusercontent.com/52317905/209150887-866969b0-6e32-46b6-b125-bf14b9121619.png)

### Создаем секрет

При помощи команды ```kubectl create secret tls lab3-tls --cert=lab3.crt --key=lab3.key```

![image](https://user-images.githubusercontent.com/52317905/209151910-5143de71-8b0a-4884-a775-df572ff0bb54.png)

### Создаем Ingress

Пишем манифейст ```Ingress``` для взаимодействия с ```service```

``` yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: frontend-ingress
spec:
  tls:
  - hosts:
      - lab3.sofia
    secretName: lab3-tls
  rules:
  - host: lab3.sofia
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: lab3-service
            port:
              number: 3000
```
Подключаем ```Ingress``` в ```minikube```: ```minikube addons enable ingress```

![image](https://user-images.githubusercontent.com/52317905/209153447-bd76e0b4-0de9-4dc6-a453-78312c7e5e3f.png)

Выполняем ```kubectl apply -f Ingress.yaml``` и проверяем создание ```Ingress``` командой ```kubectl get ingress```

![image](https://user-images.githubusercontent.com/52317905/209154015-a0d4b03b-a408-4d3a-95fb-3f3694e98d03.png)

Подключаемся к ```Ingress``` командой ```minikube tunnel```

![image](https://user-images.githubusercontent.com/52317905/209154257-5c6bd3f4-26ce-432e-abc7-b3269e22ca67.png)

### Сертификат
![image](https://user-images.githubusercontent.com/52317905/209157960-0c31605d-b3e8-4fab-8486-ecc85fa76dbd.png)


## Схема

![image](https://github.com/sonyadk/2022_2023-introduction_to_distributed_technologies-k4113-loginova_s_a/blob/main/lab3/lab3.png)
