# Déploiement d’une application sur Kubernetes : approche simplifiée

Kubernetes facilite la gestion et le déploiement d’applications conteneurisées à grande échelle. Pour débuter avec Kubernetes, il est utile de comprendre une méthode simple de déploiement d’une application. Cet article présente cette approche à travers la création d’un déploiement, l’exposition via un service et l’utilisation des commandes essentielles.

---

## 1. Pré-requis : une application conteneurisée et un cluster Kubernetes

Avant de déployer une application sur Kubernetes, il faut disposer :

- D’une image Docker de l’application stockée dans un registre accessible.  
- D’un cluster Kubernetes configuré et accessible (local avec Minikube, KIND, ou cloud comme GKE, EKS, AKS).  
- De l’outil en ligne de commande `kubectl`.

---

## 2. Créer un déploiement (Deployment)

Le **Deployment** est la ressource Kubernetes utilisée pour gérer un ensemble de pods identiques.

### Exemple de déploiement d’une application Nginx

Pour déployer Nginx avec 3 réplicas, la commande suivante suffit :

```bash
kubectl create deployment nginx-deploy --image=nginx
kubectl scale deployment nginx-deploy --replicas=3
```

Ce déploiement lance 3 pods exécutant l’image Nginx.

### Définir un déploiement via un fichier YAML

Voici un fichier `deployment.yaml` équivalent, plus flexible :

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deploy
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

Lancer ce déploiement :

```bash
kubectl apply -f deployment.yaml
```

---

## 3. Exposer l’application avec un Service

Pour rendre les pods accessibles, on crée un service Kubernetes qui fournit :

- Une adresse IP stable dans le cluster (ClusterIP).  
- Ou une accessibilité externe (NodePort, LoadBalancer).

### Exemple de service NodePort (exposition sur le port 30000 du nœud)

Fichier `service.yaml` :

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  type: NodePort
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
    nodePort: 30000
```

Déployer le service :

```bash
kubectl apply -f service.yaml
```

L’application sera accessible sur le port 30000 de chaque nœud du cluster (pour un cluster local, sur `localhost:30000`).

---

## 4. Vérifier le déploiement

- Pour lister les déploiements :  
  ```bash
  kubectl get deployments
  ```
- Pour voir les pods :  
  ```bash
  kubectl get pods
  ```
- Pour obtenir le détail du service :  
  ```bash
  kubectl get services
  ```
- Pour vérifier les logs d’un pod :  
  ```bash
  kubectl logs <nom-du-pod>
  ```

---

## 5. Nettoyer

Pour supprimer les ressources créées :

```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

---

## Sources utilisées

- Kubernetes Docs - *Deployments*: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/  
- Kubernetes Docs - *Services*: https://kubernetes.io/docs/concepts/services-networking/service/  
- Katacoda Kubernetes Tutorials: https://www.katacoda.com/courses/kubernetes  
- DigitalOcean - *How to Deploy Applications with Kubernetes*: https://www.digitalocean.com/community/tutorials/how-to-deploy-applications-with-kubernetes  

---

Utiliser un **Deployment** pour gérer les pods et un **Service** pour les exposer est la méthode standard et recommandée pour déployer des applications sur Kubernetes. Cette approche simplifiée permet de démarrer rapidement et d’évoluer vers des configurations plus complexes.