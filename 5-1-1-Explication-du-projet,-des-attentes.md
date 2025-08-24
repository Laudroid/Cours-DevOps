# Présentation du projet pratique de fin de cours DevOps : objectifs, attentes et livrables

Le projet pratique de fin de cours est une opportunité de consolider les connaissances acquises en mettant en œuvre un workflow complet de déploiement continu et d’observabilité. Il s’agit de concevoir, déployer et monitorer une application dans un environnement proche de la production, en appliquant les concepts théoriques vus dans les sections précédentes. Cet article détaille le cadre du projet, les objectifs à atteindre, ainsi que les livrables attendus.

---

## 1. Objectifs du projet

Le projet vise à :

- Appliquer les principes de **CI/CD** pour automatiser les phases de build, test, déploiement.  
- Intégrer un outil de **monitoring** (ex. Prometheus/Grafana) afin de collecter et visualiser des métriques applicatives.  
- Mettre en place une solution de **logging** centralisé (ex. ELK Stack, Grafana Loki) permettant l’analyse des logs.  
- Piloter la configuration et le déploiement via un gestionnaire d'infrastructure (ex. Kubernetes, Docker Compose).  
- Expérimenter le **tracing distribué** pour suivre et optimiser les appels entre services.

---

## 2. Déroulement et étapes recommandées

Le projet peut être structuré en étapes suivantes :

### a) Conception de l’application

- Choix d’une application simple en microservices ou monolithique avec endpoints HTTP.  
- Instrumentation basique pour exposer des métriques (nombre de requêtes, erreurs).  
- Intégration d’un système de logs structuré (format JSON recommandé).

### b) Mise en place du pipeline CI/CD

- Automatisation des builds et tests via un outil comme Jenkins, GitLab CI ou GitHub Actions.  
- Déploiement automatisé vers un environnement cible (cluster Kubernetes, VM, conteneur local).

### c) Intégration de la surveillance

- Configuration de Prometheus pour le scraping des métriques exposées.  
- Création de dashboards Grafana pour visualiser ces métriques.  
- Mise en place du système de collecte, agrégation et recherche de logs.  
- Ajout du tracing distribué avec OpenTelemetry ou Jaeger.

### d) Validation et optimisation

- Tests de charge simples pour observer le comportement sous contrainte.  
- Analyse des données de monitoring pour identifier des points critiques.  
- Ajustements sur l’application ou la plateforme.

---

## 3. Livrables attendus

- **Code source** complet avec fichier d’instrumentation des métriques et logs.  
- **Fichiers de configuration** pour CI/CD et déploiement (YAML Kubernetes, Docker Compose, pipeline CI).  
- **Configurations des outils de monitoring et logging** (Prometheus, Grafana, ELK/Loki).  
- **Rapport final** documentant la démarche, les choix, les résultats et les axes d’amélioration.  
- **Démonstration vidéo ou live** de l’application fonctionnelle et des outils de supervision en action.

---

## 4. Exemple concret : mini application en Flask

Un exemple souvent rencontré est une application Flask exposant un endpoint `/` et un `/metrics`, instrumentée avec Prometheus client. La CI déclenche les tests unitaires, construit l’image Docker, puis déploie sur un cluster Kubernetes avec déploiement de Prometheus et Grafana intégrés. Les dashboards affichent le nombre de requêtes par endpoint et la latence.

---

## Sources et références

- CNCF, *Cloud Native DevOps with Kubernetes* : https://www.cncf.io/blog/2020/03/24/cloud-native-devops-with-kubernetes/  
- Kubernetes official docs, *Monitoring applications* : https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/  
- Prometheus and Grafana integration guide : https://prometheus.io/docs/visualization/grafana/  
- OpenTelemetry project overview : https://opentelemetry.io/docs/  
- ELK Stack usage examples : https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-elastic-stack.html  

---

Ce projet pratique vise à mettre en application une chaîne DevOps complète et moderne, allant de l’écriture du code au retour d’information sur l’état et les performances en production. Il permet de développer une compréhension concrète des interactions entre développement, automatisation, déploiement et supervision.