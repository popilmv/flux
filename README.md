# flux
Install flux CLI on Ubuntu:
```
curl -s https://fluxcd.io/install.sh | sudo bash
```
Or 
```
curl -s https://raw.githubusercontent.com/fluxcd/flux2/main/install/flux.sh | sudo bash
```

Check cluster:
```
flux check --pre
```

We will see smth like that: 

![image](https://github.com/popilmv/flux/assets/115075056/aabe4061-eff6-4724-90a4-1ea75d63572c)

Export credentials: 
export GITHUB_TOKEN=<oken>
export GITHUB_USER=<username>

Install Flux:
```
flux bootstrap github --owner=$GITHUB_USER --repository=flux --branch=main --path=./clusters/my-cluster --personal
```
Checking: **kubectl get pods -A**
We will see flux:

flux-system       helm-controller-5f964c6579-nld6q                    1/1     Running   
flux-system       kustomize-controller-9c588946c-mb5mf                1/1     Running   
flux-system       notification-controller-76dc5d768-grnqg             1/1     Running  
flux-system       source-controller-6c49485888-rsx8v                  1/1     Running             

For image update: (https://fluxcd.io/flux/guides/image-update/) 
```
flux bootstrap git --components-extra=image-reflector-controller,image-automation-controller --owner=$GITHUB_USER --repository=flux-system --branch=main --path=clusters/my-cluster --read-write-key --personal
```

Add in ./clusters/my-cluster/podinfo-deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: podinfo
  namespace: default
spec:
  selector:
    matchLabels:
      app: podinfo
  template:
    metadata:
      labels:
        app: podinfo
    spec:
      containers:
        - name: podinfod
          image: docker.io/marina1327/app1-go:dev-2023-11-09-15-26-51 # {"$imagepolicy": "flux-system:podinfo"}
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 9898
              protocol: TCP
```

Do to apply in Flux:
```
flux reconcile kustomization flux-system --with-source
```

Check img in podinfo

```
kubectl get deployment/podinfo -oyaml | grep 'image:'
```

Imgrep: **flux create image repository podinfo --image=docker.io/marina1327/app1-go --interval=5m --export > ./clusters/my-cluster/podinfo-registry.yaml**

ImagePolicy ./clusters/my-cluster/podinfo-policy.yaml***

Check tags: 
```
flux get image repository podinfo
```

List of all tags:
```
kubectl -n flux-system describe imagerepositories podinfo
```
ImageUpdateAutomation
```
flux create image update flux-system --interval=10m --git-repo-ref=flux --git-repo-path="./clusters/my-cluster" --checkout-branch=main --push-branch=main --author-name=fluxcdbot --author-email=fluxcdbot@users.noreply.github.com --commit-template="{{range .Updated.Images}}{{println .}}{{end}}" --export > ./flux-system-automation.yaml
```


Helm

``
helm repo add nginx-stable https://helm.nginx.com/stable && helm repo update
```


