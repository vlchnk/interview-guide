# Kubernetes

## Содержание
1. [Введение в Kubernetes](#введение-в-kubernetes)
2. [Основные концепции](#основные-концепции)
3. [Архитектура Kubernetes](#архитектура-kubernetes)
4. [Объекты Kubernetes](#объекты-kubernetes)
5. [Управление ресурсами](#управление-ресурсами)
6. [Сетевая модель](#сетевая-модель)
7. [Хранение данных](#хранение-данных)
8. [Безопасность](#безопасность)
9. [Развертывание и управление](#развертывание-и-управление)
10. [Типичные сценарии использования](#типичные-сценарии-использования)
11. [Вопросы на собеседовании](#вопросы-на-собеседовании)

## Введение в Kubernetes

Kubernetes (K8s) — это открытая платформа для автоматизации развертывания, масштабирования и управления контейнеризированными приложениями. Kubernetes был разработан Google на основе их опыта работы с системой Borg и позже передан в Cloud Native Computing Foundation (CNCF).

### Ключевые особенности Kubernetes:
- **Автоматическое размещение**: автоматически размещает контейнеры на основе их требований к ресурсам
- **Самовосстановление**: перезапускает контейнеры, которые завершились с ошибкой, заменяет и перепланирует контейнеры при сбое узлов
- **Горизонтальное масштабирование**: масштабирует приложения вверх и вниз с помощью простой команды или автоматически на основе использования CPU
- **Балансировка нагрузки**: автоматически распределяет трафик между контейнерами
- **Управление конфигурацией**: развертывает и обновляет секреты и конфигурацию без перестроения образов
- **Оркестрация хранилища**: автоматически монтирует выбранную систему хранения
- **Декларативная модель**: описывает желаемое состояние системы, а Kubernetes обеспечивает его достижение

## Основные концепции

### Контейнер (Container)
Легковесная, автономная, исполняемая упаковка программного компонента, которая включает в себя все необходимое для его запуска: код, среду выполнения, системные инструменты, библиотеки и настройки.

### Pod
Наименьшая развертываемая единица в Kubernetes. Pod представляет собой группу из одного или нескольких контейнеров с общими ресурсами хранения и сети.

### Node (Узел)
Рабочая машина в кластере Kubernetes. Может быть виртуальной или физической. Каждый узел содержит службы, необходимые для запуска подов.

### Cluster (Кластер)
Набор узлов, которые запускают контейнеризированные приложения, управляемые Kubernetes.

### Namespace (Пространство имен)
Виртуальный кластер внутри физического кластера Kubernetes. Используется для организации ресурсов при наличии множества пользователей.

### Controller (Контроллер)
Компонент, который следит за состоянием кластера через API-сервер и вносит изменения, чтобы привести текущее состояние к желаемому.

### Service (Сервис)
Абстракция, определяющая логический набор подов и политику доступа к ним.

### Label (Метка)
Пары ключ-значение, прикрепленные к объектам для их идентификации и организации.

## Архитектура Kubernetes

### Компоненты плоскости управления (Control Plane)
Компоненты плоскости управления принимают глобальные решения о кластере и обнаруживают и реагируют на события в кластере.

#### API Server
Компонент, который предоставляет API Kubernetes. Это точка входа для всех административных задач.

```
kubectl -> API Server -> etcd
```

#### etcd
Распределенное хранилище ключ-значение, которое хранит все данные кластера.

#### Scheduler (Планировщик)
Компонент, который отвечает за распределение подов по узлам.

#### Controller Manager
Компонент, который запускает контроллеры. Каждый контроллер — это отдельный процесс, но для уменьшения сложности они скомпилированы в один бинарный файл и запускаются в одном процессе.

### Компоненты узла (Node)
Компоненты узла запускаются на каждом узле и поддерживают работу подов.

#### Kubelet
Агент, который запускается на каждом узле кластера. Он следит за тем, чтобы контейнеры запускались в поде.

#### Kube-proxy
Сетевой прокси, который запускается на каждом узле кластера. Он поддерживает сетевые правила на узлах.

#### Container Runtime
Программное обеспечение, ответственное за запуск контейнеров (Docker, containerd, CRI-O).

```
                   +-------------------+
                   |   Control Plane   |
                   +--------+----------+
                            |
                            v
+-------------+    +--------+-------+    +-------------+
|    Node     |    |      Node      |    |    Node     |
|  +-------+  |    |   +-------+    |    |  +-------+  |
|  | Pods  |  |    |   | Pods  |    |    |  | Pods  |  |
|  +-------+  |    |   +-------+    |    |  +-------+  |
+-------------+    +----------------+    +-------------+
```

## Объекты Kubernetes

### Pod
Pod — это наименьшая развертываемая единица в Kubernetes. Pod представляет собой группу из одного или нескольких контейнеров с общими ресурсами хранения и сети.

```yaml
# Пример определения Pod
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

### Deployment
Deployment предоставляет декларативные обновления для подов и ReplicaSets. Вы описываете желаемое состояние в Deployment, и контроллер Deployment изменяет фактическое состояние до желаемого состояния с контролируемой скоростью.

```yaml
# Пример определения Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

### Service
Service — это абстракция, которая определяет логический набор подов и политику доступа к ним.

```yaml
# Пример определения Service
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
```

### ConfigMap и Secret
ConfigMap и Secret используются для отделения конфигурационных данных от кода приложения.

```yaml
# Пример определения ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  config.json: |
    {
      "environment": "production",
      "debug": false
    }
```

```yaml
# Пример определения Secret
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  username: YWRtaW4=  # base64 encoded "admin"
  password: cGFzc3dvcmQ=  # base64 encoded "password"
```

### StatefulSet
StatefulSet используется для управления стационарными приложениями. Он обеспечивает уникальность и постоянство сетевых идентификаторов и хранилища.

```yaml
# Пример определения StatefulSet
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 1Gi
```

## Управление ресурсами

### Запросы и лимиты ресурсов
Kubernetes позволяет указать, сколько ресурсов (CPU и память) требуется для каждого контейнера.

```yaml
# Пример запросов и лимитов ресурсов
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: resource-demo-ctr
    image: nginx
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```

### Горизонтальное автомасштабирование подов (HPA)
HPA автоматически масштабирует количество подов в развертывании, репликасете или наборе реплик на основе наблюдаемого использования CPU или других выбранных метрик.

```yaml
# Пример HPA
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

### Вертикальное автомасштабирование подов (VPA)
VPA автоматически настраивает запросы и лимиты ресурсов контейнеров на основе их использования.

## Сетевая модель

### Сервисы и типы сервисов
Kubernetes предоставляет несколько типов сервисов для доступа к приложениям:

- **ClusterIP**: Экспонирует сервис на внутренний IP-адрес кластера. Этот тип делает сервис доступным только внутри кластера.
- **NodePort**: Экспонирует сервис на IP-адрес каждого узла на статическом порту.
- **LoadBalancer**: Экспонирует сервис внешне, используя облачный балансировщик нагрузки.
- **ExternalName**: Сопоставляет сервис с содержимым поля externalName (например, foo.bar.example.com).

### Ingress
Ingress управляет внешним доступом к сервисам в кластере, обычно HTTP. Ingress может предоставлять балансировку нагрузки, SSL-терминацию и виртуальный хостинг на основе имени.

```yaml
# Пример Ingress
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
```

### Сетевые политики
Сетевые политики определяют, как группы подов могут взаимодействовать друг с другом и с другими сетевыми конечными точками.

```yaml
# Пример NetworkPolicy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: test-network-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  - Egress
  ingress:
  - from:
    - ipBlock:
        cidr: 172.17.0.0/16
        except:
        - 172.17.1.0/24
    - namespaceSelector:
        matchLabels:
          project: myproject
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 6379
  egress:
  - to:
    - ipBlock:
        cidr: 10.0.0.0/24
    ports:
    - protocol: TCP
      port: 5978
```

## Хранение данных

### Volumes
Volume — это директория, которая доступна контейнерам в поде. Kubernetes поддерживает множество типов томов, и под может использовать любое количество томов одновременно.

```yaml
# Пример использования Volume
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      path: /data
      type: Directory
```

### PersistentVolume и PersistentVolumeClaim
PersistentVolume (PV) — это часть хранилища в кластере, которая была подготовлена администратором или динамически подготовлена с использованием Storage Classes. PersistentVolumeClaim (PVC) — это запрос на хранилище пользователя.

```yaml
# Пример PersistentVolume
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0003
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: slow
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    path: /tmp
    server: 172.17.0.2
```

```yaml
# Пример PersistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: slow
```

### StorageClass
StorageClass предоставляет способ описания "классов" хранилища. Различные классы могут сопоставляться с различными уровнями качества обслуживания, политиками резервного копирования или произвольными политиками, определенными администраторами кластера.

```yaml
# Пример StorageClass
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
reclaimPolicy: Retain
allowVolumeExpansion: true
mountOptions:
  - debug
volumeBindingMode: Immediate
```

## Безопасность

### RBAC (Role-Based Access Control)
RBAC — это метод регулирования доступа к ресурсам компьютера или сети на основе ролей отдельных пользователей.

```yaml
# Пример Role
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

```yaml
# Пример RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### SecurityContext
SecurityContext определяет привилегии и контроль доступа для пода или контейнера.

```yaml
# Пример SecurityContext
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: sec-ctx-demo
    image: busybox
    command: [ "sh", "-c", "sleep 1h" ]
    securityContext:
      allowPrivilegeEscalation: false
```

### Network Policies
Network Policies определяют, как поды могут взаимодействовать друг с другом и с другими сетевыми конечными точками.

## Развертывание и управление

### Kubectl
Kubectl — это инструмент командной строки для управления кластерами Kubernetes.

```bash
# Основные команды kubectl
kubectl get pods                     # Получить список подов
kubectl describe pod <pod-name>      # Получить подробную информацию о поде
kubectl logs <pod-name>              # Получить логи пода
kubectl exec -it <pod-name> -- bash  # Запустить оболочку в поде
kubectl apply -f <file-name.yaml>    # Создать или обновить ресурс из файла
kubectl delete -f <file-name.yaml>   # Удалить ресурс из файла
```

### Helm
Helm — это менеджер пакетов для Kubernetes, который помогает устанавливать и управлять приложениями в кластере Kubernetes.

```bash
# Основные команды Helm
helm repo add stable https://charts.helm.sh/stable  # Добавить репозиторий
helm search repo stable                            # Поиск чартов в репозитории
helm install my-release stable/mysql               # Установить чарт
helm list                                          # Список установленных релизов
helm upgrade my-release stable/mysql               # Обновить релиз
helm uninstall my-release                          # Удалить релиз
```

### Kustomize
Kustomize — это инструмент для настройки конфигурации Kubernetes без шаблонов.

```yaml
# Пример kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- deployment.yaml
- service.yaml
namePrefix: dev-
commonLabels:
  environment: dev
```

## Типичные сценарии использования

### Микросервисная архитектура
Kubernetes идеально подходит для развертывания и управления микросервисами, обеспечивая изоляцию, масштабируемость и отказоустойчивость.

### Непрерывная интеграция и доставка (CI/CD)
Kubernetes интегрируется с инструментами CI/CD для автоматизации процесса развертывания приложений.

### Обработка данных и аналитика
Kubernetes может использоваться для запуска распределенных систем обработки данных, таких как Apache Spark, Apache Flink и TensorFlow.

### Многоарендная среда
Kubernetes поддерживает многоарендность через пространства имен, квоты ресурсов и сетевые политики.

### Гибридные и мультиоблачные развертывания
Kubernetes обеспечивает единый интерфейс управления для приложений, работающих в разных облачных провайдерах и локальных средах.

## Вопросы на собеседовании

### Основные концепции Kubernetes

#### Что такое Kubernetes и для чего он используется?
Kubernetes — это открытая платформа для автоматизации развертывания, масштабирования и управления контейнеризированными приложениями. Он используется для:
- Автоматизации развертывания приложений
- Масштабирования приложений
- Управления контейнеризированными приложениями
- Обеспечения отказоустойчивости
- Балансировки нагрузки
- Управления конфигурацией и секретами

#### Какие основные компоненты входят в архитектуру Kubernetes?
- **Компоненты плоскости управления**:
  - API Server: точка входа для всех административных задач
  - etcd: распределенное хранилище ключ-значение
  - Scheduler: отвечает за распределение подов по узлам
  - Controller Manager: запускает контроллеры
- **Компоненты узла**:
  - Kubelet: агент, который запускается на каждом узле
  - Kube-proxy: сетевой прокси
  - Container Runtime: программное обеспечение для запуска контейнеров

#### Что такое Pod и почему он является наименьшей единицей в Kubernetes?
Pod — это наименьшая развертываемая единица в Kubernetes, которая представляет собой группу из одного или нескольких контейнеров с общими ресурсами хранения и сети. Pod является наименьшей единицей, потому что:
- Контейнеры в поде всегда запускаются на одном узле
- Контейнеры в поде разделяют сетевое пространство имен (IP-адрес и порты)
- Контейнеры в поде могут взаимодействовать через localhost
- Контейнеры в поде могут разделять хранилище через тома

### Объекты и ресурсы Kubernetes

#### Какая разница между Deployment, ReplicaSet и StatefulSet?
- **Deployment**: управляет развертыванием и обновлением приложений. Создает и управляет ReplicaSets.
- **ReplicaSet**: обеспечивает заданное количество реплик подов.
- **StatefulSet**: используется для стационарных приложений, которым требуется стабильные сетевые идентификаторы, постоянное хранилище и упорядоченное развертывание и масштабирование.

#### Что такое Service и какие типы сервисов существуют?
Service — это абстракция, которая определяет логический набор подов и политику доступа к ним. Типы сервисов:
- **ClusterIP**: экспонирует сервис на внутренний IP-адрес кластера
- **NodePort**: экспонирует сервис на IP-адрес каждого узла на статическом порту
- **LoadBalancer**: экспонирует сервис внешне, используя облачный балансировщик нагрузки
- **ExternalName**: сопоставляет сервис с внешним DNS-именем

#### Как работает Ingress и для чего он используется?
Ingress — это API-объект, который управляет внешним доступом к сервисам в кластере, обычно HTTP. Ingress может предоставлять балансировку нагрузки, SSL-терминацию и виртуальный хостинг на основе имени. Он используется для:
- Маршрутизации HTTP-трафика к разным сервисам на основе URL-пути
- Виртуального хостинга на основе имени хоста
- SSL/TLS-терминации
- Балансировки нагрузки

### Управление ресурсами и масштабирование

#### Как работает горизонтальное автомасштабирование подов (HPA)?
HPA автоматически масштабирует количество подов в развертывании, репликасете или наборе реплик на основе наблюдаемого использования CPU или других выбранных метрик. Процесс включает:
1. Сбор метрик с подов
2. Сравнение метрик с целевыми значениями
3. Расчет желаемого количества реплик
4. Масштабирование ресурса (Deployment, ReplicaSet, StatefulSet)

#### Что такое запросы и лимиты ресурсов и как они влияют на планирование подов?
- **Запросы (requests)**: минимальное количество ресурсов, которое гарантированно доступно для контейнера
- **Лимиты (limits)**: максимальное количество ресурсов, которое может использовать контейнер

Запросы влияют на планирование подов: планировщик размещает под только на узле, который может удовлетворить запросы ресурсов пода. Лимиты ограничивают использование ресурсов контейнером и могут привести к завершению контейнера, если он превышает лимит памяти.

#### Как реализовать автомасштабирование кластера Kubernetes?
Автомасштабирование кластера можно реализовать с помощью:
- **Cluster Autoscaler**: автоматически масштабирует количество узлов в кластере на основе потребности в ресурсах
- **Node Auto Provisioning**: автоматически создает группы узлов с оптимальными характеристиками
- **Облачных провайдеров**: AWS Auto Scaling Groups, GCP Managed Instance Groups, Azure VMSS

### Сеть и хранение

#### Как работает сетевая модель Kubernetes?
Сетевая модель Kubernetes основана на следующих принципах:
- Все поды могут взаимодействовать со всеми другими подами без NAT
- Все узлы могут взаимодействовать со всеми подами без NAT
- IP-адрес пода такой же, как видит его под

Реализация этой модели обеспечивается сетевыми плагинами (CNI), такими как Calico, Flannel, Weave Net.

#### Что такое PersistentVolume и PersistentVolumeClaim?
- **PersistentVolume (PV)**: ресурс хранилища в кластере, который был подготовлен администратором или динамически подготовлен с использованием Storage Classes
- **PersistentVolumeClaim (PVC)**: запрос на хранилище пользователя

PVC запрашивает определенный размер и режим доступа к хранилищу, а система находит подходящий PV и связывает их.

#### Как реализовать резервное копирование и восстановление данных в Kubernetes?
Резервное копирование и восстановление данных в Kubernetes можно реализовать с помощью:
- **Velero**: инструмент для резервного копирования и восстановления ресурсов Kubernetes и томов
- **Операторов баз данных**: специализированные операторы для баз данных, которые реализуют резервное копирование и восстановление
- **Снимков томов**: использование снимков томов, предоставляемых облачными провайдерами
- **Пользовательских решений**: использование контейнеров для запуска задач резервного копирования и восстановления

### Безопасность и управление доступом

#### Как работает RBAC в Kubernetes?
RBAC (Role-Based Access Control) в Kubernetes работает на основе следующих объектов:
- **Role и ClusterRole**: определяют, какие действия можно выполнять с ресурсами
- **RoleBinding и ClusterRoleBinding**: связывают роли с пользователями, группами или сервисными аккаунтами

RBAC позволяет детально контролировать, кто имеет доступ к ресурсам Kubernetes и какие действия они могут выполнять.

#### Что такое SecurityContext и как он влияет на безопасность подов?
SecurityContext определяет привилегии и контроль доступа для пода или контейнера. Он может включать:
- Запуск контейнера от имени определенного пользователя и группы
- Предотвращение повышения привилегий
- Настройка возможностей Linux (capabilities)
- Настройка SELinux/AppArmor/Seccomp профилей
- Запуск контейнера в привилегированном или непривилегированном режиме
- Настройка доступа к файловой системе

SecurityContext повышает безопасность подов, ограничивая их привилегии и доступ к ресурсам системы.
