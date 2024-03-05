# Lab de démo Portworx - Duonyx

Avec cette petite démo, vous entrez dans le monde du stockage hyper-convergé de `Portworx`

## Pré-requis
Pour cela vous n'aurez besoin que des pré-requis suivants :

- un accès à Internet depuis un navigateur moderne
- un accès à [GitHub](https://github.com/).
- un compte [GitPod.io](https://gitpod.io) (poste de travail gratuit dans le _Cloud_)
- un compte chez un fournisseur de _Cloud_ parmi ceux indiqués [ici](https://github.com/jpetazzo/container.training/tree/main/prepare-labs/terraform/providers) (ou bien des serveurs ou VMs dédié(e)s, _on-premise_).

:warning: rien ne sera installé sur votre poste de travail. Tout se passe dans le _Cloud_.

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/lpiot/gitpod-workspace/tree/test)


## Déploiement d'un cluster Kubernetes

Nous utilisons l'outil de déploiement de lab `Docker` / `Kubernetes` mis au point pour la formation _open source_ [container.training](https://github.com/jpetazzo/container.training), dont **Duonyx** est contributeur.

```
cd /workspace/container.training/prepare-labs
./labctl create --students 1 --provider digitalocean --settings settings/storage-cluster.env
```
 A la fin de cette exécution, on dispose donc d'un _cluster_ `Kubernetes` déployé sur 4 nœuds :
 
 - 1 _control plane_ `Kubernetes` _Vanilla_ (déployé par `KubeAdm`)
 - et 3 _worker nodes_ équipés chacun d'un "disque" non partitionné non formaté, qui sera utilisé par `Portworx` comme capacité de stockage.

On peut vérifier que le _cluster_ `Kubernetes` répond bien.

```
# La version du cluster
kubectl version

# Les nœuds du cluster
kubectl get nodes -o wide

# Les namespaces existants
kubectl get namespaces

# Les différents composants du Control Plane
kubectl get all -n kube-system
```

# Installation de Portworx

Déployons l'opérateur `Portworx`, qui va prendre en charge l'installation de tous les composants `Portworx`.

```
cd /workspace/portworx-lab/portworx-install

kubectl apply -f portworx-operator.yml

kns portworx
kubectl get all
```

Déployons le _cluster_ `Portworx` proprement dit.

```
kubectl apply -f portworx-essentials.yml

kubectl get all -n portworx

kubectl get pods -n portworx -o wide
```
