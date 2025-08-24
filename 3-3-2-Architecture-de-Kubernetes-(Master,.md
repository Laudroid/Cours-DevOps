# Architecture de Kubernetes : Master et Workers

Kubernetes est une plateforme d’orchestration largement adoptée pour gérer des applications conteneurisées à grande échelle. Pour comprendre son fonctionnement, il est essentiel de saisir l’architecture qui repose sur une séparation claire entre les composants maîtres (Master) et les nœuds de travail (Workers). Cet article présente cette architecture, décrit les rôles de chacun et illustre leur interaction.

---

## 1. Vue d’ensemble de l’architecture Kubernetes

Un cluster Kubernetes est constitué de :

- **Master (Plan de contrôle)** : gère l’état du cluster, orchestre les tâches et prend les décisions.  
- **Workers (Nœuds)** : exécutent les applications conteneurisées.

Cette séparation enrichit Kubernetes d’une haute disponibilité et d’une capacité d’évolution horizontale.

---

## 2. Les composants du Master

Le Master agit comme le cerveau qui contrôle l’ensemble des opérations dans le cluster. Ses principaux composants sont :

### a) API Server

Interface principale de Kubernetes exposée aux administrateurs, développeurs et aux autres composants. Toutes les commandes passent par elle (via `kubectl` ou API).

### b) Scheduler

Assigne les pods aux nœuds en fonction de contraintes (ressources, affinités, tolérances). Garantit un équilibre et une optimisation des ressources.

### c) Controller Manager

Exécute les controllers, qui régulent l’état du cluster (par exemple, s’assurer que le nombre de pods désirés est bien créé).

### d) etcd

Base de données clé-valeur stockant l’état et la configuration du cluster de manière distribuée et fiable.

---

## 3. Les composants des Workers (Nœuds)

Les workers sont responsables de l’exécution effective des conteneurs.

### a) Kubelet

Agent tournant sur chaque nœud, il surveille les pods affectés et communique avec le Master pour recevoir les directives.

### b) Kube-proxy

Gère la mise en réseau, responsable du routage des paquets vers les pods adaptés en respectant les règles de service Kubernetes. Il implémente en particulier le load balancing interne.

### c) Conteneur runtime

C’est la couche qui exécute réellement les conteneurs (ex : Docker, containerd, CRI-O).

---

## 4. Exemple simple d’interaction

- L’administrateur lance une commande `kubectl create deployment`.  
- Cette requête est traitée par l’API Server.  
- Le Scheduler attribue les pods aux nœuds libres.  
- Kubelet du nœud ciblé récupère la définition du pod, télécharge l’image et lance les conteneurs via le runtime.  
- Kube-proxy configure le réseau pour permettre la communication avec le pod.

---

## 5. Schéma résumé

```
     +---------------------+
     |       Master        |
     | +-----------------+ |
     | | API Server      | |
     | | Scheduler       | |
     | | Controller Mgr  | |
     | | etcd            | |
     | +-----------------+ |
     +----------|----------+
                |
   +------------+------------+
   |                         |
+----+                   +----+
|Node|                   |Node|
|Kube|                   |Kube|
|let |                   |let |
|Proxy|                   |Proxy|
|CRI  |                   |CRI  |
+----+                   +----+
```

---

## Sources utilisées

- Kubernetes Official Documentation, *Cluster Architecture*: https://kubernetes.io/docs/concepts/overview/components/  
- CNCF, *Introduction to Kubernetes Components* : https://www.cncf.io/blog/2019/02/22/introduction-to-kubernetes-architecture/  
- DigitalOcean, *Understanding Kubernetes Architecture* : https://www.digitalocean.com/community/tutorials/understanding-kubernetes-architecture-components  

---

L’architecture Master-Worker de Kubernetes sépare clairement les responsabilités entre gestion et exécution, assurant scalabilité et robustesse. La maîtrise de ces composants facilite la compréhension de l’orchestration de conteneurs et des mécanismes de déploiement automatisés.