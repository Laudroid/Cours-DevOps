# Présentation des outils d’intégration continue (CI) : Jenkins, GitLab CI et GitHub Actions

L’intégration continue (CI) s’appuie sur des outils qui automatisent la construction, le test et la validation du code. Parmi les solutions les plus populaires se trouvent Jenkins, GitLab CI et GitHub Actions. Chacun possède ses spécificités, son mode de fonctionnement et ses avantages. Cet article propose une vision claire et comparative de ces trois outils, pour mieux comprendre leurs usages.

---

## 1. Jenkins : pionnier open source et plateforme extensible

### Description

Jenkins est un serveur d’automatisation open source, né en 2011, qui permet de créer des pipelines CI/CD très flexibles. Indépendant de la plateforme de gestion de code, il s’installe sur un serveur dédié, qu’il soit local ou dans le cloud.

### Fonctionnalités clés

- **Architecture de plugins** : Plusieurs milliers de plugins disponibles pour intégrer tout type d’outil (Git, Docker, notifications, etc.).  
- **Pipelines complexes** : Supporte des workflows multi-étapes via un langage de pipeline déclaratif (Jenkinsfile).  
- **Interface web** : Console accessible via un navigateur pour gérer les jobs, visualiser les builds et logs.  
- **Support multi-environnements** : Capable de répartir les jobs sur plusieurs agents (nodes).

### Exemple d’utilisation

Un projet Java peut disposer d’un job Jenkins qui compile, lance les tests unitaires JUnit, puis génère un rapport de couverture, avant de déployer automatiquement en environnement de test.

---

## 2. GitLab CI : CI/CD intégré à la plateforme GitLab

### Description

GitLab CI est une solution d’intégration et de déploiement continu intégrée nativement dans GitLab. Il s’appuie sur des runners (agents) qui exécutent les pipelines définis dans un fichier `.gitlab-ci.yml` placé à la racine du dépôt.

### Fonctionnalités clés

- **Intégration transparente** à GitLab, gestion des pull/merge requests avec validations CI automatisées.  
- **Définition YAML simple** des pipelines, modulaire et flexible.  
- **Support des environnements multiples** (build, test, staging, production).  
- **Tableau de bord unifié** avec métriques, feedback visuel sur les merges requests.  
- **Gestion des secrets et variables d’environnement** sécurisée.

### Exemple d’utilisation

Un dépôt contient un `.gitlab-ci.yml` qui exécute les tests Python à chaque push sur la branche `develop`. Si les tests passent, le pipeline déploie la version sur un serveur de staging.

```yaml
stages:
  - test
  - deploy

test:
  stage: test
  script:
    - pytest

deploy:
  stage: deploy
  script:
    - ./deploy.sh
  only:
    - develop
```

---

## 3. GitHub Actions : CI/CD natif sur GitHub

### Description

GitHub Actions est la plateforme d’automatisation de workflows intégrée directement dans GitHub. Elle permet de créer des pipelines déclenchés par des événements GitHub (push, pull request, issue…).

### Fonctionnalités clés

- **Workflow définis en YAML** dans `.github/workflows/`.  
- **Large bibliothèque de "actions" réutilisables**, développées par la communauté ou officielle.  
- **Intégration directe avec GitHub**, accès aux événements, commentaires, labels.  
- **Exécution sur runners hébergés par GitHub ou auto-hébergés**.  
- **Support natif des déploiements et des versions** via des artefacts.

### Exemple d’utilisation

Un workflow exécutant des tests Node.js à chaque PR, et déployant sur AWS si la branche est `main` :

```yaml
name: Node.js CI

on: [push, pull_request]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16'
    - run: npm install
    - run: npm test
```

---

## Comparaison synthétique

| Critère            | Jenkins                                     | GitLab CI                                  | GitHub Actions                    |
|--------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Installation       | Auto-hébergé, nécessite gestion serveur    | Intégré à GitLab, auto-hébergé ou cloud   | Intégré à GitHub, runners cloud ou auto-hébergés |
| Configuration       | Jenkinsfile (Groovy)                        | `.gitlab-ci.yml` (YAML)                    | `.github/workflows/*.yml` (YAML) |
| Extensibilité       | Très élevée (plugins multiples)             | Bonne, intégrée à l'écosystème GitLab      | Large choix d’actions communautaires   |
| Intégration Git     | Indépendant, multi-plateformes               | Très étroit avec GitLab                     | Très étroit avec GitHub          |
| Courbe d’apprentissage | Plus technique, paramétrage plus complexe | Simple, ergonomique                         | Simple et rapide à démarrer     |

---

## Sources utilisées

- Jenkins Documentation : https://www.jenkins.io/doc/  
- GitLab CI Docs : https://docs.gitlab.com/ee/ci/  
- GitHub Actions Docs : https://docs.github.com/en/actions  
- Atlassian, *CI/CD Tools Comparison* : https://www.atlassian.com/continuous-delivery/ci-vs-cd

---

Ces trois outils occupent une place clé dans les chaînes CI/CD. Leur choix dépend souvent du contexte projet : Jenkins pour une flexibilité maximale, GitLab CI pour une intégration complète Git+CI, GitHub Actions pour une solution native orientée écosystème GitHub. Maintenir une connaissance claire de leurs forces facilite un workflow automatisé, fiable et efficace.