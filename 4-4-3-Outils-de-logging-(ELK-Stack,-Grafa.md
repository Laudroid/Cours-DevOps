# Outils de logging : découverte de ELK Stack et Grafana Loki

Le logging joue un rôle central dans l’observabilité, en permettant la collecte, la recherche et l’analyse des événements produits par les applications et infrastructures. Deux outils largement utilisés sont **ELK Stack** (Elasticsearch, Logstash, Kibana) et **Grafana Loki**. Cet article présente leurs caractéristiques, usages, et exemples concrets pour intégrer ces solutions dans votre pipeline de surveillance.

---

## 1. ELK Stack : la solution classique de gestion des logs

### a) Composition

- **Elasticsearch** : moteur de recherche et base de données distribuée optimisée pour l’indexation et la recherche de texte.  
- **Logstash** : pipeline de collecte, transformation et enrichissement des logs provenant de sources diverses.  
- **Kibana** : interface graphique pour visualiser, analyser et explorer les journaux.

### b) Fonctionnement

Les logs sont collectés par Logstash via pipelines configurables (fichiers, syslog, agents), transformés (filtrage, normalisation, parsing), puis envoyés vers Elasticsearch pour stockage et indexation. Kibana permet la création de dashboards et de requêtes analytiques en temps réel.

### c) Exemple d’usage

- Collecte des logs applicatifs JSON.  
- Parsing des champs spécifiques (timestamp, niveau, message).  
- Alertes basées sur des requêtes Kibana (ex : détection d’erreurs répétées).

### d) Atouts et limites

| Points forts                         | Limites                             |
|------------------------------------|-----------------------------------|
| Très robuste et évolutif            | Consommation mémoire et CPU élevée|
| Recherche full-text puissante       | Complexité du déploiement          |
| Écosystème riche de plugins         | Indexation parfois coûteuse en stockage |

---

## 2. Grafana Loki : 'Prometheus des logs', une alternative légère

### a) Concept

Grafana Loki se positionne comme un système de collecte et stockage de logs **étiquetés (label-based)**, conçu pour être simple et économiser en ressources. Inspiré de Prometheus, Loki n’indexe pas le contenu complet des logs mais seulement leurs labels, ce qui accélère les ingestions et réduit les coûts.

### b) Architecture

- **Promtail** : agent léger qui collecte les logs et les étiquette.  
- **Loki** : serveur qui stocke et propose une recherche basée sur labels.  
- **Grafana** : visualisation et corrélation avec métriques et traces.

### c) Cas d’usage

Loki est particulièrement adapté aux environnements cloud natifs, microservices et Kubernetes où les logs sont volumineux et nécessitent une corrélation avec les métriques.

---

## 3. Exemple d’intégration Loki avec Promtail

`promtail-config.yaml` simplifié :

```yaml
server:
  http_listen_port: 9080

clients:
  - url: http://loki:3100/loki/api/v1/push

scrape_configs:
  - job_name: system
    static_configs:
      - targets:
          - localhost
        labels:
          job: varlogs
          __path__: /var/log/*.log
```

Dans Grafana, une requête Loki type pour rechercher des erreurs :

```logql
{job="varlogs"} |= "error"
```

---

## 4. Comparaison ELK vs Loki

| Critères             | ELK Stack                         | Grafana Loki                      |
|----------------------|----------------------------------|----------------------------------|
| Indexation complète  | Oui (texte intégral)              | Non, indexing par labels         |
| Complexité déploiement | Assez élevé                     | Plus simple                      |
| Coût en ressources    | Important                        | Optimisé                        |
| Corrélation métriques | Nécessite intégration externe   | Native avec Grafana              |
| Cas d’usage           | Analyse log exhaustive, historique | Logs conteneurisés en production |

---

## 5. Sources et documentation

- Elastic.co, *ELK Stack Overview* : https://www.elastic.co/elk-stack  
- Grafana Labs, *Introducing Loki* : https://grafana.com/docs/loki/latest/  
- Promtail documentation : https://grafana.com/docs/loki/latest/clients/promtail/  
- CNCF Observability Landscape : https://landscape.cncf.io/category=logging  

---

L’ELK Stack reste une solution complète et performante pour centraliser et analyser des logs avec toutes les richesses fonctionnelles du moteur Elasticsearch. Loki propose une alternative plus légère et adaptée aux environnements modernes où la corrélation rapide avec les métriques et traces est indispensable. Choisir entre ces outils se fera en fonction du contexte, volumétrie des logs, besoins d’analyse et ressources disponibles.