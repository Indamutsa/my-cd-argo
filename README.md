# Argocd demo
This is a sample of how to use the argocd CI/CD.

Let's create a cluster for demo
`kind create cluster --name argo-cd`

Head to argo cd website
https://argo-cd.readthedocs.io/en/stable/getting_started/

Here is the installation:
`kubectl create namespace argocd`

Let us copy the installation file locally
`curl https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml >> install.yaml`

Run this locally
`kubectl apply -n argocd -f argo-cd/install.yaml`

Port forward it because it is more secure.
access ArgoCD UI
`kubectl get svc -n argocd`
`kubectl port-forward svc/argocd-server 8080:443 -n argocd`


Get the password from the secret
`k get secret -n argocd argocd-initial-admin-secret --template={{.data.password}} | base64 -d`

or 
login with admin user and below token (as in documentation):
`kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo`

The username is `admin`



Firstly, I like to do most of my work in containers so everything is reproducable <br/>
and my machine remains clean.

#### Get a container to work in

Run a small `ubuntu linux` container where we can install and play with `argo`: <br/>


```
docker run -it --rm -v ${HOME}:/root/ -v ${PWD}:/work -w /work --net host ubuntu sh

# install curl & kubectl
apt update && apt upgrade -y && apt install curl -y && apt install wget -y
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
mv ./kubectl /usr/local/bin/kubectl
```




There are two ways we can deploy argo, using jenkins to deploy to the external cluster with Jenkins or internally in the cluster.

This one will use the cluster configuration
`k apply -f argo-cd/app.yaml`


It does not update cluster right away, it polls the repository every three minutes and updates the cluster.
If you want to right away update it, you can use refresh or hard refresh inside or configure argocd with github webhooks.
