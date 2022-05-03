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

5. Build a Docker image for `auth`, `tickets-client` and `tickets` services (if you want to use your own images)
```shell
cd auth
docker build -t your_docker_id/auth .
docker push your_docker_id/auth
```
Same thing could be done for `tickets-client` and `tickets`.
Note that the image name, `your_docker_id/auth` in this case, will be used in e.g. [`auth-deployment`](./k8s/auth-deployment.yaml) config. 

6. Start the development environment:
```shell
skaffold dev
```

Example output:
<img width="718" alt="image" src="https://user-images.githubusercontent.com/31971259/153806649-8e624b9c-81b3-4ffd-bbbe-80e8fa9ce48d.png">
