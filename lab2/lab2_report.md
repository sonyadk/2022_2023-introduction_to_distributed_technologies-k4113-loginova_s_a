University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Loginova Sofia Andreevna 

Lab: Lab2

Date of create: 18.11.2022

Date of finished: 10.12.2022

## Ход работы

### Счачиваем образ

Устанавливаем _locally_ образ  ``` docker run ifilyaninitmo/itdt-contained-frontend:master -p 3000:3000 ```

![image](https://user-images.githubusercontent.com/52317905/206803246-434019e0-6a28-4e91-9d73-0d0207cb46a3.png)

### Запускаем Minikube

Для этого используем команду ``` minikube start ```

![image](https://user-images.githubusercontent.com/52317905/206804460-8a95126e-660a-4729-bff8-9aa1a3c2806b.png)

### Пишем .yaml  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab2-frontend
  labels:
    app: lab2-frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab2-frontend
  template:
    metadata:
      labels:
        app: lab2-frontend
    spec:
      containers:
      - name: lab2-frontend
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          value: "Sofia"
        - name: REACT_APP_COMPANY_NAME
          value: "ITMO"
```

### Создаем Deployment

Для создания используем ```kubectl create -f deployment.yaml```, для проверки развертывания выполняем  ```kubectl get deployments```  

![image](https://user-images.githubusercontent.com/52317905/206807914-c17d15f6-19e0-417c-af5a-3b968b008158.png)

![image](https://user-images.githubusercontent.com/52317905/206808108-e360cd8c-114b-4894-a0a3-23db572b8543.png)

### Создаем сервис
Для создания используем ``` minikube kubectl -- expose deployment lab2-frontend --port=3000 --target-port=3000```. После прокидываем порты ```minikube kubectl -- port-forward service/lab2-frontend 3000:3000```

![image](https://user-images.githubusercontent.com/52317905/206808439-e9a113c4-5bce-471d-94e8-ee770a227eb5.png)

### Проверяем

Переходим на ```locallhost:3000```и смотрим что отображается

![image](https://user-images.githubusercontent.com/52317905/206808529-c2da5583-af59-497b-8d84-82b5b7c16218.png)

### Логи

Для проверки логов используем ```minikube kubectl get pods```

![image](https://user-images.githubusercontent.com/52317905/206808838-3821813b-0ace-4fe2-9ab9-253495b76c82.png)

Проверяем логи каждого пода отдельно

![image](https://user-images.githubusercontent.com/52317905/206808977-9488d721-04c3-4447-b129-a66cc5bbae91.png)


### Схема ~для привлечения внимания~ 

![image](https://github.com/sonyadk/2022_2023-introduction_to_distributed_technologies-k4113-loginova_s_a/blob/main/lab2/lab2.png)
