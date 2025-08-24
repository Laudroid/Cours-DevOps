# Configurer un pipeline CI simple avec GitLab CI ou GitHub Actions pour un projet Node.js ou Python

Automatiser la compilation, les tests et la génération d’artefacts via un pipeline d’intégration continue (CI) est une étape clé dans un projet logiciel. Ce guide pratique présente comment mettre en place un pipeline simple sur GitLab CI et GitHub Actions pour un projet Node.js ou Python. L’objectif : compiler le code, exécuter les tests unitaires et générer un artefact utilisé pour le déploiement ultérieur.

---

## 1. Prérequis

- Projet versionné avec Git sur GitLab ou GitHub.  
- Tests unitaires déjà présents (exemple : Jest pour Node.js, pytest pour Python).  
- Un fichier de dépendances (`package.json` pour Node.js, `requirements.txt` pour Python).

---

## 2. Pipeline GitLab CI pour un projet Node.js

### Fichier `.gitlab-ci.yml`

```yaml
stages:
  - build
  - test
  - package

variables:
  NODE_ENV: development

cache:
  paths:
    - node_modules/

build:
  stage: build
  image: node:16
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour

test:
  stage: test
  image: node:16
  script:
    - npm install
    - npm test

package:
  stage: package
  image: node:16
  script:
    - tar -czf myapp.tar.gz dist/
  artifacts:
    paths:
      - myapp.tar.gz
    expire_in: 1 hour
```

### Explications

- **build** : Installe les dépendances, compile le projet (supposons une compilation dans `dist/`).  
- **test** : Reprend les dépendances et lance les tests unitaires (`npm test`).  
- **package** : Archive le dossier compilé en tarball, générant un artefact.  
- Le cache `node_modules/` accélère les installations successives.

---

## 3. Pipeline GitHub Actions pour un projet Python

### Fichier `.github/workflows/ci.yml`

```yaml
name: Python CI

on: [push, pull_request]

jobs:
  build-test-package:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Set up Python 3.9
      uses: actions/setup-python@v4
      with:
        python-version: 3.9

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Run tests
      run: pytest

    - name: Package application
      run: |
        mkdir -p dist
        cp -r src dist/
        tar -czf dist/myapp.tar.gz dist/
      
    - name: Upload artifact
      uses: actions/upload-artifact@v3
      with:
        name: myapp-package
        path: dist/myapp.tar.gz
```

### Explications

- Checkout du code source.  
- Setup de Python 3.9.  
- Installation des dépendances.  
- Exécution des tests unitaires via `pytest`.  
- Création d’un répertoire `dist/` et archivage.  
- Upload de l’artefact compressé pour usage ultérieur (déploiement, analyse).

---

## 4. Points clés pour réussir son pipeline CI

| Étape        | Recommandations                    |
|--------------|----------------------------------|
| Compilation  | S’assurer que la compilation produit un dossier clairement identifié (ex : `dist/` ou `build/`) |
| Tests        | Couvrir un maximum de cases critiques, garantir que `npm test` ou `pytest` renvoient 0 en succès |
| Artefacts    | Utiliser les fonctionnalités natives (`artifacts` GitLab, `upload-artifact` GitHub) pour stocker les builds |
| Rapidité     | Utiliser cache et images légères pour accélérer les étapes répétitives |
| Sécurité    | Éviter d’inclure de secrets dans les scripts, privilégier les variables sécurisées |

---

## 5. Conclusion

Mettre en place un pipeline CI simple sur GitLab ou GitHub est aujourd’hui à la portée de tous. Il automatise la compilation, la validation et la préparation des artefacts, facilitant nettement la livraison fiable et répétable. Ces exemples minimalistes peuvent être enrichis avec des étapes supplémentaires (analyse statique, déploiement) selon les besoins.

---

## Sources utilisées

- GitLab CI/CD Documentation : https://docs.gitlab.com/ee/ci/  
- GitHub Actions Documentation : https://docs.github.com/en/actions  
- Node.js CI example (GitLab) : https://docs.gitlab.com/ee/ci/examples/#example-nodejs  
- Python CI example (GitHub Actions) : https://docs.github.com/en/actions/guides/building-and-testing-python

---

Ce guide fournit une base solide pour automatiser efficacement votre processus d’intégration continue dans des environnements Node.js ou Python, en exploitant les fonctionnalités natives de GitLab CI et GitHub Actions.