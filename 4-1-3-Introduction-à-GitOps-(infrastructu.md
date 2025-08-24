# Introduction à GitOps : Infrastructure as Code via Git

GitOps est une approche moderne de gestion et d’automatisation des infrastructures et des déploiements applicatifs qui s’appuie sur Git comme source unique de vérité. Cette méthode permet de piloter les systèmes en définissant leur état désiré dans des fichiers versionnés, associés à un processus d’automatisation garantissant la cohérence et la traçabilité.

---

## 1. Qu’est-ce que GitOps ?

GitOps combine **Infrastructure as Code (IaC)** avec les mécanismes de gestion de versions et de workflows collaboratifs offerts par Git.

- **Git comme source de vérité** : toute définition de l’infrastructure (configurations Kubernetes, manifestes, scripts Terraform, etc.) est versionnée dans un dépôt Git.  
- **Automatisation déclenchée par Git** : des agents ou outils surveillent les modifications sur Git et appliquent automatiquement ces changements dans l’environnement cible (clusters Kubernetes, clouds, etc.).  
- **Observabilité et auditabilité accrues** : chaque modification est traçable, facilitant la remontée des erreurs et la conformité.

---

## 2. Principes fondamentaux

### a) Déclarativité de l’état

L’infrastructure est décrite de manière déclarative, exprimant le **"quoi"** plutôt que le **"comment"**. Par exemple, un fichier YAML Kubernetes définit le nombre de pods souhaité, sans indiquer les étapes du déploiement.

### b) Automatisation continue

Tout changement validé dans Git déclenche un pipeline automatisé qui synchronise l’environnement réel avec l’état décrit dans Git, évitant ainsi toute dérive.

### c) Reproductibilité et rollback

Puisque Git conserve l’historique complet des configurations, il est facile de revenir à un état précédent en cas de problème, assurant un déploiement fiable et sécurisé.

---

## 3. Outils et exemples d’implémentation

### Outils populaires

- **Argo CD** : outil Kubernetes natif qui synchronise le cluster avec un dépôt Git.  
- **Flux CD** : agent GitOps qui applique automatiquement les modifications Git sur Kubernetes.

### Exemple simple avec Argo CD

1. Déposer les manifests Kubernetes dans un dépôt Git :  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: app-container
        image: my-app:latest
        ports:
        - containerPort: 80
```

2. Argo CD surveille ce dépôt et applique automatiquement l’état décrit dans le cluster Kubernetes.  

3. Pour modifier l’application, on modifie simplement ce fichier dans Git, Argo CD détecte la modification et met à jour la version déployée.

---

## 4. Avantages de GitOps

| Avantages               | Description                                       |
|-------------------------|-------------------------------------------------|
| Simplicité et cohérence | Git centralise la configuration et historique.  |
| Traçabilité             | Toutes les modifications sont versionnées et auditables.  |
| Restauration facile     | Retour à une version précédente immédiat.        |
| Automatisation          | Moins d’erreurs humaines, déploiements beaucoup plus sûrs. |
| Collaboration           | Utilisation des workflows Git (pull request, revue) |

---

## 5. Cas d’usage étendu

GitOps n’est pas limité à Kubernetes. Il peut piloter tout type d’infrastructure cloud ou on-premise via des outils IaC comme Terraform, Helm, Kustomize, et peut intégrer les pipelines CI/CD pour une gestion complète du cycle de vie des applications.

---

## Sources utilisées

- CNCF, *GitOps - Operating Kubernetes as if it was Git* : https://www.cncf.io/blog/2020/07/06/what-is-gitops/  
- Argo CD Documentation : https://argo-cd.readthedocs.io/en/stable/  
- Flux CD Documentation : https://fluxcd.io/docs/  
- Weaveworks, *An Introduction to GitOps*: https://www.weave.works/technologies/gitops/  

---

GitOps transforme la gestion des infrastructures en un processus versionné, automatisé et déclaratif. S’appuyer sur Git comme source unique de vérité permet un contrôle précis des déploiements, simplifie les opérations et augmente la fiabilité des systèmes.