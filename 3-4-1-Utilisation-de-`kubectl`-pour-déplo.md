# Utiliser `kubectl` pour déployer une application conteneurisée sur Minikube ou Kind

Déployer une application conteneurisée sur un cluster Kubernetes local est une étape clé pour tester et développer en conditions proches de la production. Minikube et Kind (Kubernetes IN Docker) sont des solutions populaires permettant de lancer un cluster Kubernetes léger localement. Ce guide précis montre comment utiliser la commande `kubectl` pour déployer une application sur ces environnements.

---

## 1. Préparer le cluster local Minikube ou Kind

### Démarrage de Minikube

```bash
minikube start
```

Cette commande lance une machine virtuelle Kubernetes dans laquelle vous pourrez déployer vos ressources.

### Démarrage de Kind

```bash
kind create cluster
```

Kind crée un cluster Kubernetes dans un conteneur Docker, simplifiant l’installation sur tout OS doté de Docker.

---

## 2. Construire et charger l’image Docker dans le cluster local

Si votre application est conteneurisée via une image Docker construite localement, il faut la rendre accessible au cluster.

### Avec Minikube

Minikube possède son propre daemon Docker.

```bash
eval $(minikube docker-env)
docker build -t mon-app:v1 .
```

Cela construit l’image dans l’environnement Docker de Minikube, directement disponible pour le cluster.

### Avec Kind

Kind ne partage pas le daemon Docker de l’hôte, il faut importer l’image dans le cluster.

```bash
docker build -t mon-app:v1 .
kind load docker-image mon-app:v1
```

---

## 3. Déployer l’application avec `kubectl`

### Définir un fichier YAML `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mon-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: mon-app
  template:
    metadata:
      labels:
        app: mon-app
    spec:
      containers:
      - name: mon-app-container
        image: mon-app:v1
        ports:
        - containerPort: 8080
```

Déployer avec :

```bash
kubectl apply -f deployment.yaml
```

### Vérifier les pods en cours d’exécution

```bash
kubectl get pods
```

---

## 4. Exposer l’application

Pour accéder à l’application, créer un service :

#### Service NodePort

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mon-app-service
spec:
  type: NodePort
  selector:
    app: mon-app
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30007
```

Déployer :

```bash
kubectl apply -f service.yaml
```

---

## 5. Accéder à l’application localement

### Avec Minikube

Récupérer l’IP du cluster Minikube :

```bash
minikube ip
```

L’application sera accessible à l’adresse : `http://<minikube-ip>:30007`

### Avec Kind

Kind n’expose pas automatiquement les ports, il faut utiliser `kubectl port-forward` :

```bash
kubectl port-forward svc/mon-app-service 8080:8080
```

Puis accéder via `http://localhost:8080`.

---

## 6. État, logs et nettoyage

- Pour surveiller les ressources :

```bash
kubectl get all
```

- Pour voir les logs d’un pod :

```bash
kubectl logs <nom-du-pod>
```

- Pour supprimer le déploiement et le service :

```bash
kubectl delete -f deployment.yaml
kubectl delete -f service.yaml
```

- Pour arrêter Minikube :

```bash
minikube stop
```

- Pour supprimer le cluster Kind :

```bash
kind delete cluster
```

---

## Sources utilisées

- Minikube Official Docs: https://minikube.sigs.k8s.io/docs/  
- Kind Official Docs: https://kind.sigs.k8s.io/docs/user/quick-start/  
- Kubernetes Docs - kubectl: https://kubernetes.io/docs/reference/kubectl/overview/  
- Kubernetes Tutorials by Katacoda: https://www.katacoda.com/courses/kubernetes  

---

Utiliser `kubectl` avec Minikube ou Kind permet de simuler un environnement Kubernetes complet localement, simplifiant le développement, les tests et la prise en main du déploiement d’applications conteneurisées. La manipulation des images Docker et la gestion des services via NodePort ou port-forwarding sont des notions essentielles dans cet écosystème.