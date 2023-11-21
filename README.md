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

