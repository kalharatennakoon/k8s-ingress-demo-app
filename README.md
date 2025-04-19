# k8s-ingress-demo-app
A simple Hello World web application demonstrating a full Kubernetes setup with Docker, Deployment, Service, Ingress, and Port Forwarding. This project serves as a beginner-friendly example of deploying containerized applications in a local Kubernetes environment using Minikube.


```bash
docker build . -t hello-world-nginx-app

docker tag hello-world-nginx-app randilt/hello-world-nginx-app

docker push randilt/hello-world-nginx-app
```

```bash
kubectl apply -f deployment-nginx-app.yml 

// verify
k get deploy -n dev-ns
```

```bash
kubectl expose deployment nginx-app-deployment --name nginx-app-service --port=8080 --target-port=80 -n dev-ns

The expose command parses the referenced Deployment object to extract valuable pairing details such as Name, Label, Selector, and containerPort to populate these values in the Service object.

//verify
k get svc -n dev-ns
k describe svc nginx-app-service -n dev-ns
```

```bash
Enable the Ingress addon: (via Minikube)
minikube addons enable ingress

// verify
k get ns
kubectl get pods -n ingress-nginx

```

```bash
minikube ip
> 192.168.49.2

kubectl create ingress nginx-app-ingress \
  --class=nginx \
  --rule="demo.192.168.49.2.sslip.io/*=nginx-app-service:80" \
  -n dev-ns

kubectl create ingress nginx-app-ingress \
  --class=nginx \
  --rule="demo.192.168.49.2.sslip.io/*=nginx-app-service:80" \
  -n dev-ns


// port-forwarding
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8081:80

// this works - but not with ingress
kubectl port-forward -n dev-ns svc/nginx-app-service 8081:8080


> output
Forwarding from 127.0.0.1:8081 -> 80
Forwarding from [::1]:8081 -> 80
```


troubleshoot:
k get ns -n ingress-nginx 

check whether port 8081 in use
lsof -i :8081

if port 8081 in use, kiil that process if not critical
kill -9 <PID>


