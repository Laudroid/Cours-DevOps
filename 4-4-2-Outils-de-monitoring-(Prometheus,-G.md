# Outils de monitoring : découverte de Prometheus et Grafana

Le monitoring est une composante clé de l’observabilité, garantissant la surveillance continue des performances, de la santé des systèmes et la détection rapide d’anomalies. Parmi les outils les plus largement adoptés dans l’écosystème cloud natif, **Prometheus** et **Grafana** occupent une place centrale. Cet article présente leurs fonctionnalités, complémentarités, et illustre leur usage avec des exemples pratiques.

---

## 1. Prometheus : collecte et gestion de métriques

### a) Fonctionnement

Prometheus est un système open source de monitoring et d’alerte conçu pour collecter, stocker et requêter des données métriques dans un format temporel.

- **Modèle Pull** : Prometheus interroge périodiquement des endpoints HTTP exposant des métriques au format textuel (exporters).  
- **Base de données temporelle interne** optimisée pour les analyses de séries temporelles.  
- **Langage de requêtes flexible (PromQL)**, permettant d’écrire des expressions complexes pour explorer les métriques.  
- **Alertmanager** intégré pour gérer la notification des alertes (email, Slack, PagerDuty, etc.).

### b) Exemples de métriques collectées

- CPU, mémoire, I/O des serveurs et containers  
- Nombre de requêtes HTTP, taux d’erreur, latence  
- Statistiques propres aux applications (ex : nombre d’utilisateurs actifs)

---

## 2. Grafana : visualisation et tableaux de bord

Grafana est une solution open source de **visualisation** qui se connecte à diverses sources de données, dont Prometheus, pour créer des dashboards interactifs.

- Supporte plusieurs sources (Prometheus, Elasticsearch, InfluxDB, etc.).  
- Permet de construire des graphiques, tables, heatmaps, alertes visuelles.  
- Interface web intuitive avec options de filtre, annotations, et partage.  
- Possibilité d’alerte basée sur des métriques visualisées.

---

## 3. Exemple de mise en œuvre Prometheus + Grafana

### Étape 1 : Exposer des métriques dans une application

Avec une application en Go, on peut utiliser la bibliothèque client officielle Prometheus :

```go
import (
  "github.com/prometheus/client_golang/prometheus"
  "github.com/prometheus/client_golang/prometheus/promhttp"
  "net/http"
)

var (
  httpRequests = prometheus.NewCounterVec(
    prometheus.CounterOpts{
      Name: "http_requests_total",
      Help: "Nombre total de requêtes HTTP.",
    },
    []string{"path"},
  )
)

func main() {
  prometheus.MustRegister(httpRequests)
  http.Handle("/metrics", promhttp.Handler())
  // Incrémenter compteur dans les handlers HTTP...
  http.ListenAndServe(":2112", nil)
}
```

### Étape 2 : Configurer Prometheus pour scruter l’application

Extrait du fichier `prometheus.yml` :

```yaml
scrape_configs:
  - job_name: 'my-app'
    static_configs:
      - targets: ['localhost:2112']
```

### Étape 3 : Créer un dashboard Grafana

- Ajouter Prometheus comme source de données dans Grafana.  
- Créer un graphique avec une requête PromQL :

```promql
sum(rate(http_requests_total[5m])) by (path)
```

Ce graphique affiche le taux de requêtes HTTP par chemin durant les 5 dernières minutes.

---

## 4. Avantages de cette combinaison

| Aspect                 | Prometheus                      | Grafana                        |
|------------------------|--------------------------------|-------------------------------|
| Collecte métriques      | Modèle pull, scalable          | N/A                           |
| Stockage               | Base temporelle intégrée       | N/A                           |
| Analyse                | Requêtes via PromQL            | N/A                           |
| Visualisation          | Basique dans l’interface       | Richesse graphique et flexibilité |
| Alerting               | Alertmanager configurable      | Alertes visuelles sur dashboards |

---

## 5. Sources et documentation

- Prometheus Official Docs: https://prometheus.io/docs/introduction/overview/  
- Grafana Official Docs: https://grafana.com/docs/grafana/latest/  
- *Monitoring your application with Prometheus and Grafana* (DigitalOcean tutorial): https://www.digitalocean.com/community/tutorials/how-to-set-up-prometheus-monitoring-on-ubuntu-18-04  
- Blog Grafana Labs, *Getting Started with Grafana and Prometheus*: https://grafana.com/blog/2020/12/02/getting-started-with-grafana-and-prometheus/  

---

Prometheus et Grafana forment un duo puissant dans la mise en place d’une stratégie de monitoring efficace et flexible. Prometheus collecte et gère les métriques avec une précision granulaire et scalabilité, tandis que Grafana offre une interface intuitive et robuste pour visualiser ces données, facilitant la prise de décisions éclairées et la gestion proactive des incidents.