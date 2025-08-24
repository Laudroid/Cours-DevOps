# Modifier un pipeline CI/CD pour déployer une application conteneurisée sur un environnement de staging après réussite des tests

Mettre en place un pipeline d’intégration et de déploiement continu (CI/CD) permettant de déployer automatiquement une application conteneurisée sur un environnement de **staging** après validation des tests est une étape clé pour garantir la qualité avant mise en production.

Cet article présente comment modifier un pipeline existant afin d’y intégrer cette étape intermédiaire, avec des exemples concrets utilisant des outils populaires.

---

## 1. Contexte : Pipeline CI de base

Un pipeline CI standard contient souvent ces étapes :

- **Checkout** du code source  
- **Build** de l’image Docker  
- **Tests** (unitaires, intégration)  
- **Push** de l’image vers un registry  

Pour passer au CD, il faut intégrer un déploiement automatique dans un environnement de staging uniquement si les tests réussissent.

---

## 2. Ajouter le déploiement dans le pipeline

### Étape clé : conditionner le déploiement au succès des tests

La logique consiste à enchaîner les étapes suivantes :

```
Build → Tests → Push → Déploiement en staging
```

Le déploiement s’exécutera uniquement si les étapes précédentes sont OK.

### Exemple avec GitLab CI

Voici un exemple de `.gitlab-ci.yml` simplifié :

```yaml
stages:
  - build
  - test
  - deploy-staging

build:
  stage: build
  script:
    - docker build -t registry.example.com/my-app:$CI_COMMIT_SHA .
  tags:
    - docker
  only:
    - main

test:
  stage: test
  script:
    - docker run --rm registry.example.com/my-app:$CI_COMMIT_SHA pytest tests/
  only:
    - main

deploy_staging:
  stage: deploy-staging
  script:
    - kubectl apply -f k8s/staging/deployment.yaml
    - kubectl rollout status deployment/my-app-deployment -n staging
  only:
    - main
  when: on_success
```

- La construction et les tests utilisent l’image Docker avec un tag unique basé sur le commit.  
- Le déploiement cible le namespace `staging` dans Kubernetes via des manifests Kubernetes versionnés.  
- `when: on_success` garantit que cette étape ne démarre que si les tests passent.

---

## 3. Découpage de l’environnement staging

Il est recommandé d’isoler cet environnement pour :

- Valider en conditions proches de la production.  
- Effectuer des tests manuels, exploratoires, ou tests utilisateurs internes.  
- Eviter tout impact sur les utilisateurs finaux.

Le fichier `deployment.yaml` doit pointer vers la nouvelle image-tag et être adapté à ce namespace.

---

## 4. Gestion des secrets et accès

Pour déployer, le pipeline doit avoir les bonnes permissions :

- Configuration des credentials `kubectl` (kubeconfig).  
- Accès au cluster staging.  
- Authentification au registre Docker pour pull/push des images.

Ces informations sont souvent injectées via des variables sécurisées CI/CD.

---

## 5. Surveillance et rollback

Il est possible d’étendre le pipeline avec des étapes de validation post-déploiement (smoke tests, health checks).

Les orchestrateurs comme Kubernetes facilitent le rollback avec la commande :

```bash
kubectl rollout undo deployment/my-app-deployment -n staging
```

---

## Sources et références

- GitLab CI/CD Documentation - *Build, test, and deploy container images*: https://docs.gitlab.com/ee/ci/examples/docker_build.html  
- Kubernetes Docs - *Rollout and Rollback*: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment  
- DigitalOcean - *How To Set Up CI/CD Pipelines for Kubernetes*: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-ci-cd-pipeline-for-kubernetes-applications  
- Atlassian - *CI/CD best practices with pipelines*: https://www.atlassian.com/continuous-delivery/ci-vs-cd  

---

## Conclusion

Modifier un pipeline CI existant pour intégrer un déploiement automatique sur un environnement de staging après succès des tests demande d’organiser les étapes de manière conditionnelle et sécurisée. L’usage d’un environnement de staging isolé garantit que seules des versions validées progressent vers la production, limitant ainsi les risques et améliorant la qualité des livraisons.