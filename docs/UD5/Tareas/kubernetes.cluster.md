---
icon: material/file-edit
---

TODO:
A continuación se presenta una guía orientativa para instalar y configurar un clúster de Kubernetes desde cero. Se asume que se parte de máquinas con un sistema operativo Linux (por ejemplo, Ubuntu Server) y que se dispone de privilegios de administrador. Estas instrucciones están pensadas para estudiantes de Formación Profesional con conocimientos básicos de administración de sistemas Linux, redes y virtualización.

## Objetivos de la guía

- Preparar el entorno necesario para instalar Kubernetes.
- Configurar nodos maestro (control plane) y nodos trabajadores.
- Comprender los pasos clave en el despliegue de un clúster usando kubeadm.
- Aprender a verificar la instalación y desplegar el primer servicio.

## Requisitos previos

- **Hardware:** Al menos 2 máquinas (virtuales o físicas). Una para el nodo maestro y otra para el nodo trabajador.
    - Nodo maestro: 2 CPU, 2 GB RAM (mínimo)
    - Nodo trabajador: 2 CPU, 2 GB RAM (mínimo)
- **Sistema Operativo:** Ubuntu 20.04 o 22.04 LTS (se recomienda) o sistemas Linux equivalentes.
- **Acceso root o privilegios de sudo.**
- Conexión a Internet.
- Sincronización horaria (NTP) para evitar problemas de certificados.

## Preparación del entorno

### Configuración del hostname y hosts

1. Asignar a cada máquina un nombre de host significativo, por ejemplo:
    - Maestro: `k8s-master`
    - Nodo trabajador: `k8s-worker1`
2. Editar el archivo `/etc/hosts` para que ambos nodos se conozcan entre sí por nombre, por ejemplo:
    
    ```bash
    192.168.1.10   k8s-master
    192.168.1.11   k8s-worker1
    ```
    

Ajustar las IP y nombres según la red que utilicéis.

### Deshabilitar el swap

Kubernetes no funciona correctamente con swap activado, por lo que es necesario deshabilitarlo:

```bash
sudo swapoff -a
```

Además, editar `/etc/fstab` y comentar la línea del swap para que no se active tras reiniciar.

### Instalar dependencias

Instalar paquetes necesarios, como `apt-transport-https`, `ca-certificates`, `curl`:

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
```

### Añadir repositorios de Kubernetes

Se deben agregar las claves y repos de Kubernetes (kubeadm, kubelet, kubectl):

```bash
curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] \
https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Actualizar e instalar las herramientas de Kubernetes:

```bash
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

La instrucción `apt-mark hold` evita que se actualicen automáticamente y se rompa el clúster.

### Instalar contenedor runtime (Containerd)

Kubernetes necesita un runtime de contenedores. Usaremos `containerd`:

```bash
sudo apt-get install -y containerd
```

Configurar `containerd` creando un archivo de configuración por defecto:

```bash
sudo mkdir -p /etc/containerd
sudo containerd config default | sudo tee /etc/containerd/config.toml
```

Editar `/etc/containerd/config.toml` y en la sección `[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]`, comprobar que `SystemdCgroup = true` (esto mejora la integración con kubelet).

Reiniciar containerd:

```bash
sudo systemctl restart containerd
sudo systemctl enable containerd
```

### Configurar el tráfico de red y el módulo br_netfilter

Kubernetes requiere que el kernel permita el reenvío de paquetes en el bridge. Añadir las siguientes líneas:

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

sudo modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables=1
net.ipv4.ip_forward=1
EOF

sudo sysctl --system
```

## Crear el clúster (en el nodo maestro)

En el nodo maestro, inicializar el clúster con `kubeadm`:

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

Esta red (pod-network-cidr) se usará con un plugin de red compatible, como Flannel. Tras ejecutar este comando, se obtendrá un token y la información para unir nodos trabajadores al clúster.

Una vez finalice, configurar el entorno para `kubectl`:

```bash
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Instalar la red de pods

Para que los pods puedan comunicarse, instalar un plugin de red. Por ejemplo, Flannel:

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

Espera unos minutos hasta que todos los nodos y pods del sistema estén en estado `Running`.

## Añadir el nodo trabajador al clúster

En el nodo trabajador, usar el comando que se generó en el `kubeadm init`. Por ejemplo:

```bash
sudo kubeadm join k8s-master:6443 --token <token-generado> --discovery-token-ca-cert-hash sha256:<hash-generado>
```

Si no tienes el comando a mano, puedes obtenerlo desde el maestro:

```bash
kubeadm token create --print-join-command
```

Una vez el nodo trabajador se una al clúster, en el maestro puedes verificar:

```bash
kubectl get nodes
```

Deberías ver el `k8s-master` con estado `Control Plane` y el `k8s-worker1` en `Ready` tras un tiempo.

## Desplegar un primer servicio

Para comprobar que todo funciona, crear un despliegue de prueba con Nginx:

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
```

Listar los servicios:

```bash
kubectl get svc
```

Observar el `NodePort` asignado. Desde un navegador (apuntando a la IP del nodo y al puerto asignado) deberíamos ver la página por defecto de Nginx.

## Conclusiones y próximos pasos

- Hemos creado un clúster Kubernetes básico con un nodo maestro y un nodo trabajador.
- Se ha instalado y configurado el runtime de contenedores, las herramientas kubeadm/kubelet/kubectl, y un plugin de red.
- Se ha desplegado un servicio sencillo para validar el funcionamiento.

A partir de aquí, se pueden realizar más prácticas, como:

- Añadir más nodos trabajadores.
- Desplegar aplicaciones más complejas.
- Investigar sobre la monitorización, logging, y seguridad en Kubernetes.
- Integrar Ingress Controllers y almacenamiento persistente.

Este es un punto de partida sólido para comprender la arquitectura y el flujo de trabajo en Kubernetes. La clave es experimentar, cometer errores, aprender a solucionarlos y profundizar en la documentación oficial.