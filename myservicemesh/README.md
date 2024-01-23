# README
## La marche à suivre
1. Install Istio
   https://istio.io/latest/docs/setup/getting-started/

   `curl -L https://istio.io/downloadIstio | sh -`
   `cd istio-1.20.2`
   `export PATH=$PWD/bin:$PATH`

   `istioctl install --set profile=demo -y`

   Install the Istio addons (Kiali, Prometheus, Jaeger, Grafana):
   `kubectl apply -f samples/addons`

   Enable auto-injection of the Istio side-cars when the pods are started:
   `kubectl label namespace default istio-injection=enabled`

   Configure Docker so that it uses the Kubernetes cluster:
   `minikube docker-env`
   `eval $(minikube -p minikube docker-env)`
   `eval $(minikube docker-env)`

3. Start the app
- Start Docker Daemon(守护进程）
  Start Docker 

- Start the Minikube Cluster 
  `minikube start --cpus=2 --memory=5000 --driver=docker`

- Deploy the app in the Minikube Cluster 
  - Set the Kubernetes config
  `kubectl apply -f configmap.yaml`
  - Launch the Kubernetes deployment and service for PostgreSQL, and the Ingress gataway:
  `kubectl apply -f infrastructure.yaml`
  - Deploy the micro services and service mesh proxies
  `kubectl apply -f microservices.yaml`
  - Get the access to the Ingress gateway
  `./ingress-forward.sh`
----
1. Un problème pendant l’installation de Istio
Error from server (NotFound): error when creating "samples/addons/prometheus.yaml": namespaces "istio-system" not found
->  Create the namespace manually (需要手动创建命名空间)：`kubectl create ns istio-system`

2. Circuit Breaker 断路器
Le modèle de Circuit Breaker intervient en introduisant un disjoncteur contrôlable pour empêcher des appels de service continus en cas d'échecs, évitant ainsi un effondrement complet du système.

Adding a circuit breaker to carservice:
`kubectl apply -f circuit-breaker.yaml`
Disable the circuit breaker using:
`kubectl apply -f circuit-breaker.yaml`

----
### Monitoring 
**Display the Kiali dashboard**
Kiali is a console for Istio service mesh.
`kubectl -n istio-system port-forward deployment/kiali 20001:20001`
Launch the console: ~[http://localhost:20001/](http://localhost:20001/)~
Active the service:
~~[http://localhost:31380/carservice/cars](http://localhost:31380/carservice/cars)~~
https://localhost:31380/myservice

**Monitoring with Graphana**
`kubectl -n istio-system port-forward deployment/grafana 3000:3000`
https:// localhost:3000/
-> Dashboards
	-> istio/Istio Control Plane Dashboard
![](README/image.png)

----
