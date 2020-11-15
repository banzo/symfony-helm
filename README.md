# Symfony Helm

## Docker Compose

```bash
export "$(grep -v '^#' .env | xargs -d '\n')"

docker-compose up
```

- [localhost:8000](http://localhost:8000)
- [localhost:8000/products/1](http://localhost:8000/products/1)

## Build Images

```bash
docker build . -t banzo/symfony-helm-mysql -f ./docker/mysql/Dockerfile \
    --build-arg VERSION=8.0.21
docker push banzo/symfony-helm-mysql

docker build . -t banzo/symfony-helm-nginx -f ./docker/nginx/Dockerfile \
    --target prod \
    --build-arg VERSION=1.19.2
docker push banzo/symfony-helm-nginx

docker build . -t banzo/symfony-helm-php-fpm -f ./docker/php-fpm/Dockerfile \
    --target prod \
    --build-arg VERSION=7.4.10
docker push banzo/symfony-helm-php-fpm
```

## Kubernetes

```bash
brew install kubernetes-cli kubernetes-helm
```

```bash
kubectl create namespace symfony-helm

kubectl create -n symfony-helm secret generic database-secret \
    --from-literal=database=app \
    --from-literal=password=root \
    --from-literal=url=mysql://root:root@mysql:3306/app

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.35.0/deploy/static/provider/cloud/deploy.yaml

helm upgrade symfony-helm helm \
    --install \
    --set-string phpfpm.env.plain.APP_ENV=prod,nginx.host=symfony-helm.io,imageTag=latest \
    --namespace symfony-helm

kubectl delete all --all --all-namespaces
```

## Minikube & Helm

```bash
minikube start --vm=true --driver=hyperkit
minikube addons enable ingress

helm template helm

kubectl create secret generic database-secret \
    --from-literal=database=app \
    --from-literal=password=root \
    --from-literal=url=mysql://root:root@mysql:3306/app

helm install symfony-helm helm --set-string phpfpm.env.plain.APP_ENV=prod,nginx.host=symfony-helm.io,imageTag=latest

kubectl get services

kubectl get pods

# View Minikube dashboard
minikube dashboard

# View the website, this will open a browser window
minikube service nginx

# Upgrade the deployment
helm upgrade symfony-helm helm

# Delete the deployment
helm uninstall symfony-helm
```
