# Tracing distribué : comprendre et utiliser Jaeger et OpenTelemetry

Le tracing distribué est une technique d’observabilité essentielle pour analyser le parcours d’une requête à travers un système distribué, comme une architecture microservices. Il permet d'identifier les latences, les erreurs et les goulets d’étranglement en suivant les appels interservices. Jaeger et OpenTelemetry sont deux outils majeurs qui facilitent cette pratique. Cet article explique leur rôle, leur fonctionnement et comment les utiliser concrètement.

---

## 1. Qu’est-ce que le tracing distribué ?

Dans un contexte distribué, une requête utilisateur traverse plusieurs services avant de retourner une réponse. Le tracing distribué collecte des **traces**, composées de **spans** qui représentent les opérations ou appels individuels. Ces spans sont liés entre eux et horodatés, créant une carte détaillée du traitement.

Le tracing aide à :

- Diagnostiquer les causes de lenteurs ou erreurs dans les flux complexes  
- Identifier les services les plus sollicités ou ralentis  
- Analyser les dépendances entre composants

---

## 2. OpenTelemetry : la norme d’instrumentation

OpenTelemetry est un projet open source de la Cloud Native Computing Foundation (CNCF). Il propose une **boîte à outils unifiée** pour collecter des métriques, logs et traces.

- **Instrumentation** : bibliothèques pour instrumenter des applications dans plusieurs langages (Go, Java, Python, etc.)  
- **Exporters** : formats pour envoyer les données vers des backend de monitoring/tracing  
- Supporte des formats compatibles avec des outils comme Jaeger, Zipkin, Prometheus.

### Exemple d’instrumentation simple en Python

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.jaeger.thrift import JaegerExporter

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)

jaeger_exporter = JaegerExporter(
    agent_host_name='localhost',
    agent_port=6831,
)
span_processor = BatchSpanProcessor(jaeger_exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("operation-xyz") as span:
    # Traiter la requête ou appel interne
    pass
```

---

## 3. Jaeger : système de collecte et visualisation

Jaeger, développé originellement par Uber, est un backend de tracing distribué permettant :

- Collecte, stockage et indexation des traces  
- Agrégation par service, latence, erreurs  
- Visualisation graphique des traces et descentes dans les spans

### Fonctionnement

Jaeger ingère les traces envoyées par les SDK OpenTelemetry ou client natifs, via plusieurs protocoles (gRPC, HTTP thrift, etc.). Il stocke les traces dans une base comme Cassandra, Elasticsearch ou mémoire locale.

### Interface utilisateur

L’interface Jaeger permet d’interroger :

- Les traces les plus lentes  
- L’analyse des dépendances entre services  
- Les erreurs au niveau de chaque span

---

## 4. Cas d’usage concret

Dans une application de commande en ligne :

- Un utilisateur soumet un achat  
- La requête traverse le service frontend, puis un service de validation, un service de paiement et un service d’inventaire  
- OpenTelemetry instrumente chaque service pour générer des spans  
- Jaeger visualise la trace complète affichant les délais et erreurs  
- L’équipe détecte que le service paiement est le goulot d’étranglement et s’optimise en conséquence

---

## 5. Références et sources

- OpenTelemetry official docs : https://opentelemetry.io/docs/  
- Jaeger official docs : https://www.jaegertracing.io/docs/latest/  
- CNCF Blog, *An Introduction to Distributed Tracing* : https://www.cncf.io/blog/2020/01/28/an-introduction-to-distributed-tracing/  
- Medium article, *Getting started with OpenTelemetry and Jaeger*: https://medium.com/opentelemetry/getting-started-with-opentelemetry-jaeger-instrumentation-413de4321b77  

---

Le tracing distribué avec Jaeger et OpenTelemetry offre une visibilité approfondie sur les opérations internes des architectures complexes. En suivant précisément chaque étape d’une requête, ils permettent d’optimiser la performance et la fiabilité des systèmes distribués.