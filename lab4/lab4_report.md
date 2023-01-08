University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Loginova Sofia Andreevna 

Lab: Lab4

Date of create: 18.11.2022

Date of finished: 29.12.2022

## Ход работы

### Одновременный запуск 2-х нод

```minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode-demo```

![image](https://user-images.githubusercontent.com/52317905/209170436-d31f6321-7576-4d5d-a903-91487499e03c.png)

Проверяем список нод ```kubectl get nodes```

![image](https://user-images.githubusercontent.com/52317905/209170521-35d8e450-2c82-4634-b1ad-d238db37e76f.png)

### Устанавливаем cicoctl

Для установки _cicoctl_ воспользуемся уже существующим официальным док'ом  и выполним команду: ```kubectl create -f calicoctl.yaml```

![image](https://user-images.githubusercontent.com/52317905/209171174-b0f2eafc-7169-472b-b203-43a7b6250943.png)

Дальше удаляем IpPool по умолчанию ```kubectl delete ippools default-ipv4-ippool```

![image](https://user-images.githubusercontent.com/52317905/209171366-8c1335f6-adbc-4413-8b20-2b0bca0444de.png)

## Манифест для IpPool

Возьмем за основу официальный [манифест](https://projectcalico.docs.tigera.io/networking/assign-ip-addresses-topology). Для проверки режима ```IPAM```  для запущеных ранее нод укажем ```label``` по признаку север/юг (south/north). 

``` yaml
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
   name: zone-south-ippool
spec:
   cidr: 192.168.0.0/24
   ipipMode: Always
   natOutgoing: true
   nodeSelector: zone == "south"
---
apiVersion: projectcalico.org/v3
kind: IPPool
metadata:
   name: zone-north-ippool
spec:
   cidr: 192.168.1.0/24
   ipipMode: Always
   natOutgoing: true
   nodeSelector: zone == "north"
 ```
Создаем IpPool ```kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch create -f - < ippool.yaml```
 
 ![image](https://user-images.githubusercontent.com/52317905/209173768-c10a753d-344f-4948-bae5-14aac672f688.png)
 
 Проверяем ```kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch get ippool -o wide```
 
 ![image](https://user-images.githubusercontent.com/52317905/209173921-ce3cca72-0443-47b2-831b-e073d97fff08.png)

## Развертывание и сервис

Манифест для развертывания:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lab4-front
  labels:
    app: lab4-front
spec:
  replicas: 2
  selector:
    matchLabels:
      app: lab4-front
  template:
    metadata:
      labels:
        app: lab4-front
    spec:
      containers:
      - name: lab4-front
        image: ifilyaninitmo/itdt-contained-frontend:master
        ports:
        - containerPort: 3000
        env:
        - name: REACT_APP_USERNAME
          value: "Sofia"
        - name: REACT_APP_COMPANY_NAME
          value: "ITMO"
```

Манифест для сервиса: 
```yaml
apiVersion: v1
kind: Service
metadata:
  name: lab4-service
spec:
  selector:
    app: lab4-service
  ports:
    - port: 3000
      targetPort: 3000
  type: LoadBalancer
  ```
Выполняем ```kubectl apply -f deployment.yaml``` и ```kubectl apply -f service.yaml```
  
![image](https://user-images.githubusercontent.com/52317905/209176826-2b4e44cb-6dd6-48b5-bd75-032c412dc736.png)
  
Проверяем при помощи ```kubectl get deployments``` и ```kubectl get services```

![image](https://user-images.githubusercontent.com/52317905/209177010-edb4ee33-660d-451f-8740-557243a91baa.png)

При проверке подов ```kubectl get po -o wide``` и ```kubectl describe pod lab4-front-8cd6b699-75tzj``` выявляется ошибка, что образ не ложится на контейнеры.

![image](https://user-images.githubusercontent.com/52317905/209189117-a2202c82-e445-40fa-bdb5-cb88228cbe17.png)

![image](https://user-images.githubusercontent.com/52317905/209188539-4ca572f5-e454-4abf-8aca-0ae5708cef6f.png)

## Схема

![iamge](https://github.com/sonyadk/2022_2023-introduction_to_distributed_technologies-k4113-loginova_s_a/blob/main/lab4/lab4.png)
