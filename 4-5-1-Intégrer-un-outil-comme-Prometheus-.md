# Intégration d’un monitoring simple avec Prometheus et Grafana : tutoriel pratique

Surveiller les performances et la santé d'une application est une étape essentielle pour garantir sa disponibilité et réactivité. Prometheus et Grafana sont des outils largement utilisés pour ce type de monitoring. Cet article présente comment intégrer rapidement un monitoring simple d’une métrique d’application déployée, depuis l’instrumentation jusqu’à la visualisation.

---

## 1. Principe général

- **Prometheus** collecte régulièrement des métriques exposées par l’application via un endpoint HTTP.  
- Ces données sont stockées dans une base de séries temporelles.  
- **Grafana** se connecte à Prometheus et permet de créer des dashboards graphiques pour visualiser les métriques, identifier les tendances et anomalies.

---

## 2. Étape 1 : Instrumenter l’application

Le but est d’exposer une métrique simple, par exemple le nombre total de requêtes HTTP traitées.

### Exemple en Python avec la bibliothèque `prometheus_client`

```python
from prometheus_client import start_http_server, Counter
from flask import Flask, request

app = Flask(__name__)
REQUEST_COUNT = Counter('http_requests_total', 'Number of HTTP requests')

@app.route('/')
def index():
    REQUEST_COUNT.inc()  # Incrémente à chaque requête
    return "Hello, world!"

if __name__ == '__main__':
    start_http_server(8000)  # Expose les métriques sur localhost:8000/metrics
    app.run(port=5000)
```

- Le serveur Flask répond sur le port 5000.  
- Les métriques Prometheus sont exposées sur le port 8000 à l’URL `/metrics`.

---

## 3. Étape 2 : Configurer Prometheus pour collecter les métriques

Un fichier `prometheus.yml` minimaliste :

```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'my_app'
    static_configs:
      - targets: ['localhost:8000']
```

- Prometheus va interroger toutes les 15 secondes l’endpoint `/metrics` de l’application.  
- Ce fichier de configuration doit être utilisé au lancement de Prometheus.

---

## 4. Étape 3 : Lancer Prometheus et Grafana

### Prometheus

Téléchargez Prometheus depuis https://prometheus.io/download/ puis lancez-le avec la configuration :

```bash
./prometheus --config.file=prometheus.yml
```

Prometheus sera accessible sur http://localhost:9090.

### Grafana

Téléchargez et lancez Grafana : https://grafana.com/grafana/download

Connectez-vous à Grafana (par défaut admin/admin), puis :

- Ajoutez Prometheus comme source de données (URL: `http://localhost:9090`).  
- Créez un nouveau dashboard.  
- Ajoutez un panneau avec la requête PromQL suivante :

```promql
http_requests_total
```

Cette requête affiche le nombre total de requêtes HTTP reçues par l’application.

---

## 5. Résultats et visualisation

- Vous visualisez en temps réel l'évolution du compteur de requêtes.  
- Grafana permet de configurer des alertes ou d'affiner la visualisation (par minute, par endpoint…).

---

## 6. Avantages de cette approche simple

- Mise en place rapide avec peu de dépendances.  
- Monitoring basique pour vérifier la disponibilité et la charge de l’application.  
- Extensible pour ajouter d’autres métriques (temps de réponse, erreurs, ressources).

---

## Sources et documentation

- Prometheus client Python : https://github.com/prometheus/client_python  
- Prometheus documentation : https://prometheus.io/docs/introduction/overview/  
- Grafana documentation : https://grafana.com/docs/grafana/latest/getting-started/getting-started-prometheus/  
- Exemple tutoriel DigitalOcean : https://www.digitalocean.com/community/tutorials/how-to-monitor-applications-with-prometheus-and-grafana-on-ubuntu-18-04  

---

Cette intégration minimale avec Prometheus et Grafana permet d’introduire concrètement un monitoring efficace, posant ainsi les bases d’une observabilité approfondie adaptée à des environnements de production modernes.