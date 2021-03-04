# Kubernetes

1. [Introducción a Kubernetes](#intro)
2. [Arquitectura interna de un cluster de kubernetes](#architecture)
3. [Tipos de instalación de kubernetes](#install-types)
4. [Instalación de kubectl y minikube con el controlador de docker](#install)
5. [Iniciar minikube usando hipervisor virtualbox](#virtualbox)

<hr>

<a name="intro"></a>

## 1. Introducción a Kubernetes

### Escalabilidad

Es la capacidad de los sistemas para adaptarse al crecimiento por la demanda y complejidad.

- **Vertical:** Agregar más recursos al mismo nodo y aumentar la capacidad de cómputo.
- **Horizontal:** Agregar más nodos que se adapten a la carga de trabajo.

Kubernetes se utiliza para el escalado horizontal.

### Kubernetes

Kubernetes es un sistema para automatizar el despliege escalado y admininstración de aplicaciones en contenedores.
- OpenSource
- Auto-sanado
- Escalado horizontal
- Balanceo de carga y discovery

En Kubernetes todo se conoce como objetos. Se realizan peticiones de definición mediante la API y se almacenan en la base de datos etcd en formato yaml.

**Conceptos fundamentales**
- **PODS:** 
  - Unidad más pequeña que se puede desplegar y gestionar en kubernetes.
  - Es un grupo de uno o más contenedores que comparten almacenamientop y red además de especificaciones de cómo ejecutarse.
  - Son efímeros.

- **Deployments:**
  - Describe el estado deseado de una implementación.
  - Ejecuta multimles réplicas de la aplicación.
  - Reemplaza las que están defectuosas o las que no responden.

- **Services:**
  - Definición de cómo exponer una aplicación que se ejecuta en un conjunto de pods como un sevicio de red.
  - Por defecto se usa round-robin para balanceo de carga.

- **Config map:**
  - Permite desacoplar la configuración para hacer las imágenes más portables:
  - Almacenan variables de entorno, argumentos para línea de comandos o configuración de volúmenes que pueden consumir los pods (no soportan encriptación).

- **Labels:**
  - Pares de clave-valor para organizar, seleccionar, consultar y monitorizar objetos de forma más eficiente.
  - Ideales para UI y CLIs.

- **Selectores:**
  - Mecanismo para hacer consultas a las etiquetas.

<hr>

<a name="intro"></a>

## 2. Arquitectura interna de un cluster de kubernetes

Cuando se implementa kubernetes se obiente un clúster, que consta de un conjunto de máqunas de cómputo (nodos) que ejecutan aplicaciones en contenedores.

Los nodos alojan los pods que son los componentes de la carga de trabajo de la aplicación.

![Image 1](./images/img1.png)

- **kube-apiserver:** Provee la interfaz para las herramientas de administración (kubectl o kubernetes dashboard).
- **etcd:** Almacenamiento que mantiene la configuración y el estado del clúster.
- **kube-scheduler:** Al crear o escalar las aplicaciones, selecciona el nodo para los pods y los ejecuta.
- **kube-controller-manager:** Supervisa controladores más pequeños que ejecutan tareas de replicar pods y mangejar operaciones de los nodos.

<hr>

<a name="intro"></a>

## 3. Tipos de instalación de kubernetes

### Managed kubernetes
- **AKS:** Azure Kubernetes Service.
- **EKS:** Amazon Elastic Kubernetes Service.
- **GKE:** Google Kubernetes Engine.
- **IBM Cloud**

### Onpremise kubernetes

- **All in one:** Se instala todo en un único nodo usando mini kube (para propósitos educativos y pruebas).
- **Single master and multiworker:** Un nodo para el *control panel* y uno o más nodos controlados por el master.
- **Single master, single etcd and multiworker:** Un nodo para el ĉontrol panel un nodo para almacenar la configuración y el estado y uno o más nodos controlados por el master.
- **Multimaster and multiworker:** Múltiples nodos para el *control panel* en alta disponibilidad y uno o mñas nodos controlados por el master.
- **Multimaster, multietcd and multiworker:** Multiples nodos para el *control panel* y múltiples nodos para el almacenamiento etcd en alta disponibilidad y uno o más nodos controlados por el master.

<hr>

<a name="install"></a>

## 4. Instalación de kubectl y minikube con el controlador de docker

Para empezar comprobamos las actualizaciones del sistema:

~~~
sudo apt-get update -y
sudo apt-get upgrade -y
~~~

Después deberemos instalar algunas dependencias de descarga de datos:
~~~
sudo apt-get install apt-transport-https wget curl
sudo apt install virtualbox virtualbox-ext-pack
~~~
> En la instalación dale a Aceptar y  acepta la licencia.

A continuación descarga minikube, dale permisos e instálalo para que tengas acceso desde cualquier sitio:
~~~
wget https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
chmod +x minikube-linux-amd64
sudo mv minikube-linux-amd64 /usr/local/bin/minikube
~~~
Comprueba que está bien instalado ejecutando:
~~~
minikube version
~~~
Instala kubectl, dale permisos y colócalo en el path:
~~~
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
~~~
Comprueba que va todo correcto
~~~
kubectl version -o json
~~~

Arranqua minikube
~~~
minikube start
~~~

si ves que falla por un tema de red:
~~~
docker swarm leave --force
docker network prune
~~~

Si falla por un tema de permisos:
~~~
sudo usermod -aG docker $USER && newgrp docker
~~~

Comprueba que tienes acceso:
~~~
kubectl cluster-info
kubectl config view
~~~

Podemos ver el estado de minikube con el comando:

~~~
minikube status
~~~

Para detener la ejecución usamos el comando:

~~~
minikube stop
~~~

Para utilizar el dashboard de minikube usamos el comando

~~~
minikube dashboard
~~~

<hr>

<a name="intro"></a>

## 5. Iniciar minikube usando hipervisor virtualbox

Para poder lanzar minikube a través de virtualbox, debemos eliminar el cluster creado anteriormente mediante el comando:

~~~
minikube delete
~~~

Una vez instalado [virtualbox](https://www.virtualbox.org/)

Lanzamos minikube con el comando

~~~
minikube start --driver=virtualbox
~~~

Ahora podemos ver minikube virtualizado a través de la interfaz gráfica de virtualbox.

En cualquier caso, en la siguiente sección utilizaremos minikube con el controlador de docker así que eliminaremos de nuevo el cluster creado y volveremos a crearlo con el driver de docker...

~~~
minikube delete
minikube start --driver=docker
~~~