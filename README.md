## Tickets app
A small web application running microservices inside k8s cluster.

## Installation
### Prerequisite
- `kubectl` [CLI](https://kubernetes.io/docs/tasks/tools/)
- [Docker desktop](https://docs.docker.com/desktop/) with [kubernetes installed](https://docs.docker.com/desktop/kubernetes/#enable-kubernetes)

1. Create a new k8s cluster using `docker-desktop` context:
```shell
kubectl config get-contexts
kubectl config use-context docker-desktop
```

2. Get `ingress-nginx` [controller](https://kubernetes.github.io/ingress-nginx/deploy/#quick-start) up and running.

Example (the YAML config might change, refer to the installation guide above):
```shell
cd k8s
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.1/deploy/static/provider/cloud/deploy.yaml
```

3. Install `skaffold` [CLI](https://skaffold.dev/docs/install/#standalone-binary) for easier development
4. Tweak `hosts` file in your computer to redirect traffic from [ingress host configuration](./k8s/ingress-srv.yaml) to your localhost:
Example in MacOS:
```shell
sudo nano /etc/hosts
```
Then add the mapping and save. In this example, `tickets.dev` is the host for authentication rule of [`ingress-service`](./k8s/ingress-srv.yaml):
```shell
127.0.0.1 tickets.dev
```

5. Build a Docker image for `auth` service:
```shell
cd auth
docker build -t vinhlee95/auth .
```
Note that the image name, `vinhlee95/auth` in this case, will be used in [`auth-deployment`](./k8s/auth-deployment.yaml) config. 

6. Start the development environment:
```shell
skaffold dev
```

Test 1 endpoint:
```shell
curl tickets.dev/api/users/currentuser
```
Example output `{"name":"foo"}`