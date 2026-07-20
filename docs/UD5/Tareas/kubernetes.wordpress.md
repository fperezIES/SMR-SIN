---
icon: material/file-edit
---

TODO:

A continuación se propone una práctica detallada para que los alumnos desplieguen una instalación de WordPress con alta disponibilidad en un clúster de Kubernetes. Esta práctica está pensada para que los estudiantes adquieran competencias en la configuración de recursos en Kubernetes, la utilización de despliegues con réplicas, la separación de la capa de datos (base de datos) de la capa de front-end (WordPress), y el uso de Ingress para exponer la aplicación de forma accesible. Además, se trabajará con el almacenamiento persistente y la escalabilidad del front-end.

---

### Objetivos de la práctica

1. **Comprender la arquitectura de un despliegue WordPress altamente disponible:**
    
    - Separación de la capa de aplicación (WordPress) y la capa de datos (MySQL o MariaDB).
    - Uso de un Deployment con varias réplicas para garantizar la disponibilidad del front-end.
    - Almacenamiento persistente para mantener la información de la base de datos.
2. **Aprender a definir y utilizar objetos de Kubernetes clave:**
    
    - **Deployments** y **ReplicaSets** para escalado.
    - **Services** (ClusterIP, NodePort o LoadBalancer) para interconectar pods.
    - **PersistentVolume** (PV) y **PersistentVolumeClaim** (PVC) para almacenamiento persistente.
    - **ConfigMaps** y/o **Secrets** para parámetros sensibles (credenciales DB).
    - **Ingress** para exponer el servicio al exterior a través de un hostname.
3. **Practicar con el escalado horizontal automático (HPA - Horizontal Pod Autoscaler)** para ajustarse a picos de tráfico.
    

---

### Requisitos previos

- Tener acceso a un clúster Kubernetes funcional (minikube, kind o un clúster real).
- Tener instalado `kubectl` y acceso administrativo al clúster.
- Disponer de una imagen de WordPress (por ejemplo, la oficial `wordpress:latest`) y de una imagen de base de datos compatible (`mysql:5.7` o `mariadb:latest`).
- Un Ingress Controller desplegado (por ejemplo, Nginx Ingress Controller).

---

### Pasos recomendados

#### 1. Preparar el namespace y objetos base

1. Crear un **namespace** específico para aislar la práctica:
    
    ```bash
    kubectl create namespace wordpress-ha
    ```
    
2. Trabajar siempre dentro de ese namespace:
    
    ```bash
    kubectl config set-context --current --namespace=wordpress-ha
    ```
    

#### 2. Desplegar la base de datos con persistencia

1. **PersistentVolumeClaim** para la base de datos:  
    Crear un fichero `mysql-pvc.yaml`:
    
    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 5Gi
      storageClassName: standard
    ```
    
    Aplicarlo:
    
    ```bash
    kubectl apply -f mysql-pvc.yaml
    ```
    
2. **Deployment de la base de datos** (ej. MySQL): Crear `mysql-deployment.yaml`:
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      replicas: 1
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - name: mysql
            image: mysql:5.7
            env:
              - name: MYSQL_ROOT_PASSWORD
                value: "rootpass"
              - name: MYSQL_DATABASE
                value: "wordpress"
              - name: MYSQL_USER
                value: "wpuser"
              - name: MYSQL_PASSWORD
                value: "wppass"
            ports:
              - containerPort: 3306
            volumeMounts:
              - name: mysql-storage
                mountPath: /var/lib/mysql
          volumes:
            - name: mysql-storage
              persistentVolumeClaim:
                claimName: mysql-pvc
    ```
    
    Aplicarlo:
    
    ```bash
    kubectl apply -f mysql-deployment.yaml
    ```
    
3. **Service** para la base de datos:  
    Crear `mysql-service.yaml`:
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      selector:
        app: mysql
      ports:
        - port: 3306
          targetPort: 3306
      clusterIP: None # Opcional, para StatefulSets. Si no, se puede omitir.
    ```
    
    Aplicarlo:
    
    ```bash
    kubectl apply -f mysql-service.yaml
    ```
    

#### 3. Desplegar WordPress con alta disponibilidad

1. **Deployment de WordPress** con réplicas:  
    Crear `wordpress-deployment.yaml`:
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: wordpress
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: wordpress
      template:
        metadata:
          labels:
            app: wordpress
        spec:
          containers:
          - name: wordpress
            image: wordpress:latest
            env:
              - name: WORDPRESS_DB_HOST
                value: "mysql.wordpress-ha.svc.cluster.local:3306"
              - name: WORDPRESS_DB_USER
                value: "wpuser"
              - name: WORDPRESS_DB_PASSWORD
                value: "wppass"
              - name: WORDPRESS_DB_NAME
                value: "wordpress"
            ports:
              - containerPort: 80
    ```
    
    Aplicarlo:
    
    ```bash
    kubectl apply -f wordpress-deployment.yaml
    ```
    
2. **Service** para WordPress: Crear `wordpress-service.yaml`:
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: wordpress-service
    spec:
      selector:
        app: wordpress
      ports:
        - port: 80
          targetPort: 80
      type: ClusterIP
    ```
    
    Aplicarlo:
    
    ```bash
    kubectl apply -f wordpress-service.yaml
    ```
    

#### 4. Exponer WordPress externamente mediante Ingress

1. **Ingress** para acceder por un dominio (ej. `wordpress.local`): Crear `wordpress-ingress.yaml`:
    
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: wordpress-ingress
      annotations:
        nginx.ingress.kubernetes.io/rewrite-target: /
    spec:
      rules:
        - host: wordpress.local
          http:
            paths:
              - path: /
                pathType: Prefix
                backend:
                  service:
                    name: wordpress-service
                    port:
                      number: 80
      ingressClassName: nginx
    ```
    
    Aplicar:
    
    ```bash
    kubectl apply -f wordpress-ingress.yaml
    ```
    
    Luego, añadir `wordpress.local` al fichero `/etc/hosts` apuntando a la IP del Ingress Controller (si procede, en caso de minikube usar `minikube ip`).
    

#### 5. Opcional: Configurar un Horizontal Pod Autoscaler (HPA)

1. Instalar métricas (si no están) con `metrics-server`.
    
2. Crear un HPA que escale el deployment de WordPress según el uso de CPU:
    
    ```bash
    kubectl autoscale deployment wordpress --cpu-percent=50 --min=3 --max=10
    ```
    
    Esto hará que, si el consumo de CPU supera el 50%, se creen más réplicas del front-end WordPress, incrementando la alta disponibilidad y capacidad de respuesta.
    

---

### Comprobación de la práctica

1. Ejecutar `kubectl get pods` y asegurar que existen múltiples pods de `wordpress` y uno de `mysql`.
2. Acceder a `http://wordpress.local` desde el navegador (después de ajustar el `/etc/hosts` o DNS) y completar la instalación de WordPress.
3. Introducir contenido de prueba en WordPress y comprobar que, si se simula una carga elevada, el HPA incrementa el número de réplicas.
4. Verificar que si se elimina uno de los pods de WordPress manualmente (`kubectl delete pod <pod>`) el Deployment repone automáticamente el pod, manteniendo la disponibilidad.

---

### Conclusiones

Con esta práctica, los alumnos habrán:

- Desplegado una aplicación compleja (WordPress) en Kubernetes.
- Separado la capa de datos de la capa de aplicación.
- Garantizado la alta disponibilidad a través de réplicas y un Ingress para acceso externo.
- Experimentado con el almacenamiento persistente.
- Implementado un autoscalado horizontal basado en métricas de CPU.

De esta forma, se refuerzan conceptos fundamentales de Kubernetes que serán de gran utilidad en entornos productivos.