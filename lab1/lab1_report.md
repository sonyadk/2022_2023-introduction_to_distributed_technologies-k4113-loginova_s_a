University: [ITMO University](https://itmo.ru/ru/)

Faculty: [FICT](https://fict.itmo.ru)

Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)

Year: 2022/2023

Group: K4113c

Author: Loginova Sofia Andreevna 

Lab: Lab1

Date of create: 18.11.2022

Date of finished: Not yet

## Ход работы

### Docker 
Давным-давно, где-то в пустынных аудиториях, студентка второго курса бакалавриата устанавливала Docker. Но это было так давно, что отчета об этом не сохранилось. А жаль. В любом случаем, Docker на моей бедной Windows 10 стоял и ждал своего часа.

### minikube
Идем на [официальный сайт](https://minikube.sigs.k8s.io/docs/start/) или [вот сюда](https://kubernetes.io/ru/docs/tasks/tools/install-minikube/) и устанавливаем minikube. Дальше нам нужно развернуть _minikube cluster_, для этого используем команду ``` minikube start```, и проверяем, что появился узел ```kubectl get nodes```. 

Все на месте? Какие мы молодцы, горжусь!

![image](https://user-images.githubusercontent.com/52317905/202762171-bd6e7d79-17ce-49d4-834a-d62142a24325.png)

### Что по подам?

Создаем ``` manifest.yaml ``` и указываем в нем необходимую информацию. Привет конспекту в Miro.

![image](https://user-images.githubusercontent.com/52317905/202764971-dab659b2-5a71-4cd4-9fe0-7e0c2d1c43db.png)

Все еще передавая привет конспекту в Miro и моей невнимательности с n-ной попытки создаем объект, выводим список наших PODов.

![image](https://user-images.githubusercontent.com/52317905/202763487-b5127080-3c66-4614-a740-ae3ac6a84b09.png)

### Запустим сервис

Запускаем сервис, для доступа к созданному контейнеру командой ``` minikube kubectl -- expose pod vault --type=NodePort --port=8200 ``` и прокидываем порт на POD ``` minikube kubectl -- port-forward service/vault 8200:8200 ```. 

![image](https://user-images.githubusercontent.com/52317905/202766054-4b0bd23c-6a9e-456b-a08f-f8c061b4e07a.png)

Переходим на http://localhost:8200 и, о Боги, оно работает. Кто-то сомневался? Я лично - да.

![image](https://user-images.githubusercontent.com/52317905/202768128-2adbc6c5-5917-4b08-81bc-06c003ee1d18.png)

### Проверим логи

Как нам войти в эту чудо-систему? Проверим логи: ```minikube kubectl -- logs service/vault```. 

![image](https://user-images.githubusercontent.com/52317905/202766290-ad3e3253-b2d2-4708-a140-6c36e5071976.png)

Находим Root Token, вставляем его в поле и радуемся жизни.

![image](https://user-images.githubusercontent.com/52317905/202766430-d76d10d6-bbde-4eea-b9f4-dbf880b7856b.png)

### Ауч

Со времен бакалавриата мы с Docker обходили друг друга стороной, потому что я скорее продакт, чем DevOps, ~да и химии у нас не сложилось~. В общем пришлось пару дней побродить по просторам интернета, дабы понять, как вообще и что. Посоветую [курс](https://stepik.org/lesson/550144/), может поможет кому-то, кто тоже не очень "шарит".

_P.s. Ребята, которые будут пытаться запустить minikube через драйвера VirtualBox, лучше сразу топайте в driver=Docker и будет счастье._

### Схема ~для привлечения внимания~ 

![image](https://user-images.githubusercontent.com/52317905/202770367-a4f864e5-abd8-403d-beea-d72654526f0c.png)
