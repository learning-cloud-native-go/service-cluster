# Setting up an Istio Gateway



## Development environment

### Development dependencies
- https://kubernetes.io/docs/tasks/tools/install-minikube/
- https://github.com/helm/helm#install
- https://istio.io/docs/setup/#downloading-the-release

### 01. Starting minikube
- `minikube start --memory=16384 --cpus=4`
- `minikube tunnel` (💡separate terminal)

### 02. Setting up Istio
- `curl -L https://git.io/getLatestIstio | ISTIO_VERSION=1.3.3 sh -`

The steps according to [istio.io/docs/setup/install/helm](https://istio.io/docs/setup/install/helm/),
- `kubectl create namespace istio-system`
- `helm template istio-1.3.3/install/kubernetes/helm/istio-init --name istio-init --namespace istio-system | kubectl apply -f -`
- `kubectl get crds | grep 'istio.io' | wc -l` (should see 23)
- `helm template istio-1.3.3/install/kubernetes/helm/istio --name istio --namespace istio-system | kubectl apply -f -`

>💡 Investigate the progress via `kubectl get po -n istio-system`.



## Deployment process

### Gateway
- `kubectl apply -f gateway.yaml`

>💡 More info via `kubectl get svc istio-ingressgateway -n istio-system`.

### Service A
- `kubectl create ns servicea`
- `kubectl label ns servicea istio-injection=enabled`
- `kubectl apply -f servicea-deployment.yaml`
    - >💡 Investigate the progress via `kubectl get po -n servicea`.
- `kubectl apply -f servicea-service.yaml`
    - >💡 Verify via `kubectl get svc -n servicea`.
- `kubectl apply -f servicea-virtualservice.yaml`

>💡 <istio-ingressgateway:LoadBalancer:EXTERNAL-IP>/servicea/greet should give `{"response": "Hello world!"}`.

### Service B
- `kubectl create ns serviceb`
- `kubectl label ns serviceb istio-injection=enabled`
- `kubectl apply -f serviceb-deployment.yaml`
    - >💡 Investigate the progress via `kubectl get po -n serviceb`.
- `kubectl apply -f serviceb-service.yaml`
    - >💡 Verify via `kubectl get svc -n serviceb`.
- `kubectl apply -f serviceb-virtualservice.yaml`

>💡 <istio-ingressgateway:LoadBalancer:EXTERNAL-IP>/serviceb/greet should give `{"response": "Hello world!"}`.