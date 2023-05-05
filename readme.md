
## Basic Digital Ocean Ingress Quick Start

Summarized from this guide:
https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes

# Deploy services:
```bash
kubectl apply -f kubernetes/echo1.yaml
kubectl apply -f kubernetes/echo2.yaml
```

Verify everything is ready:
```bash
kubectl get svc
kubectl get pods
```

Kubernetes Nginx Ingress Controller
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/do/deploy.yaml
```

Verify two pods completed and one is running:
```bash
kubectl get pods -n ingress-nginx \
  -l app.kubernetes.io/name=ingress-nginx --watch
  ```

Verify that the ingress-nginx-controller has an external IP address:
```bash
kubectl get svc --namespace=ingress-nginx
```

Demoing a minimum viability ingress which has no SSL or annotations.
Warning--may not work on certain domains such as .dev.
```bash
kubectl apply -f kubernetes/echo_ingress_minimum_viable.yaml
```

Add in DNS records pointing to Load Balancer and confirm with CURL:
Failed curl for .dev... might be domain type specific as it's ssl only

Apply the cert manager:
```bash
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.7.1/cert-manager.yaml
```

Add staging:
```bash
kubectl create -f kubernetes/staging_issuer.yaml
```
(Note that certificates will only be created after annotating and 
updating the Ingress resource provisioned in the previous step.)

Add prod: 
```bash
kubectl create -f kubernetes/prod_issuer.yaml
```


Only required as a Digital Ocean Workaround:
```bash
kubectl apply -f kubernetes/ingress_nginx_svc.yaml
```


Use the annotated version (not minimum viable) with staging issuer (edit within file):
```bash
kubectl apply -f kubernetes/echo_ingress.yaml
```

Validate there is a created certificate:
```bash
kubectl describe ingress
```

Validate:
```bash
wget --save-headers -O- echo1.example.com
```


Use the annotated version (not minimum viable) with production issuer (edit again):
```bash
kubectl apply -f kubernetes/echo_ingress.yaml
```

Verify:
```bash
kubectl describe certificate echo-tls
```
