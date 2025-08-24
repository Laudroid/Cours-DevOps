# Réalisation d’un projet DevOps complet : du dépôt Git au monitoring sur Kubernetes

L’exercice proposé consiste à mettre en œuvre une chaîne DevOps complète en binôme ou petit groupe, à partir d’une application simple. Ce travail pratique permet de maîtriser les bases essentielles du développement moderne : gestion de code, intégration continue, déploiement containerisé et surveillance. Voici les étapes clés à réaliser, illustrées par des exemples concrets.

---

## 1. Mise en place d’un dépôt Git structuré

### Objectif

Organiser le projet avec un contrôle de version efficace, permettant la collaboration et les évolutions en parallèle.

### Recommandations

- Créer un dépôt central sur une plateforme comme GitHub, GitLab ou Bitbucket.  
- Adopter une stratégie de branches simple, par exemple :  
  - `main` ou `master` pour la version stable en production  
  - `develop` pour les développements en cours  
  - branches feature spécifiques pour les nouvelles fonctionnalités  

### Commandes Git essentielles

```bash
git init
git remote add origin https://github.com/votreorg/monappli.git
git checkout -b develop
git push -u origin develop
```

---

## 2. Pipeline CI/CD automatisé

### Objectif

Automatiser les étapes de build, test et déploiement pour garantir la qualité et accélérer la livraison.

### Exemple avec GitHub Actions

Fichier `.github/workflows/ci-cd.yaml` simplifié :

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - develop
      - main

jobs:
  build-test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Set up Python
      uses: actions/setup-python@v3
      with:
        python-version: '3.x'
    - name: Install dependencies
      run: pip install -r requirements.txt
    - name: Run tests
      run: pytest

  build-deploy:
    runs-on: ubuntu-latest
    needs: build-test

    steps:
    - uses: actions/checkout@v3
    - name: Build Docker image
      run: docker build -t monappli:${{ github.sha }} .
    - name: Push Docker image
      run: |
        echo "${{ secrets.DOCKER_PASSWORD }}" | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
        docker push monappli:${{ github.sha }}
    - name: Deploy to Kubernetes
      uses: azure/k8s-deploy@v1
      with:
        manifests: ./k8s/deployment.yaml
        images: monappli:${{ github.sha }}
```

---

## 3. Conteneurisation de l’application

### Objectif

Emballer l’application et toutes ses dépendances dans un conteneur Docker portable.

### Exemple de `Dockerfile` simple pour une application Python Flask

```Dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .

EXPOSE 5000
CMD ["python", "app.py"]
```

- Image légère basée sur `python:3.10-slim`.  
- Installation des dépendances puis copie du code source.  
- Lancement de l’application sur le port 5000.

---

## 4. Déploiement Kubernetes

### Extrait d’un fichier `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: monappli-deploy
spec:
  replicas: 2
  selector:
    matchLabels:
      app: monappli
  template:
    metadata:
      labels:
        app: monappli
    spec:
      containers:
      - name: monappli
        image: monappli:latest
        ports:
        - containerPort: 5000
```

- Cette ressource décrit un déploiement avec 2 réplicas.  
- Kubernetes assure la disponibilité et le redémarrage automatique.

---

## 5. Monitoring basique

### Intégration de Prometheus/Grafana pour suivre une métrique simple

1. Instrumenter l’application (exemple Python avec `prometheus_client`) pour exposer un endpoint `/metrics`.  
2. Déployer Prometheus dans le cluster avec un `ServiceMonitor` ciblant l’application.  
3. Configurer Grafana avec Prometheus comme source de données.  
4. Créer un dashboard simple visualisant, par exemple, le nombre de requêtes HTTP.

---

## 6. Optionnalités avancées

### Intégration de sécurité simple

- Scan automatique des images Docker (ex. avec Trivy).  
- Analyse statique de code (SAST) lors du pipeline.  
- Politique RBAC restreignant les droits Kubernetes.

### GitOps

- Déployer via un outil GitOps (ex. ArgoCD, Flux) qui synchronise l’état du cluster avec un dépôt Git.  
- Facilite la traçabilité des modifications et le rollback.

---

## Sources et références

- Documentation GitHub Actions : https://docs.github.com/en/actions  
- Documentation Docker : https://docs.docker.com/  
- Kubernetes official docs : https://kubernetes.io/docs/home/  
- Prometheus & Grafana monitoring : https://prometheus.io/docs/introduction/overview/, https://grafana.com/docs/grafana/latest/getting-started/getting-started-prometheus/  
- Trivy security scanner : https://github.com/aquasecurity/trivy  
- GitOps basics (Flux) : https://fluxcd.io/docs/  

---

Cette réalisation pratique met en œuvre une chaîne DevOps complète, de la gestion du code source à la surveillance en production. Le travail en groupe améliore la collaboration et la maîtrise des outils, essentiels dans les environnements DevOps actuels.