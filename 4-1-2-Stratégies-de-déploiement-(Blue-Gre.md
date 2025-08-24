# Stratégies de déploiement : Blue/Green, Canary et Rolling Update

Lors du déploiement continu, choisir une stratégie adaptée pour mettre à jour une application en production est clé pour garantir la disponibilité, la résilience et la qualité du service. Cet article présente les trois stratégies les plus répandues : Blue/Green, Canary et Rolling Update, leurs fonctionnements, avantages, inconvénients, ainsi que des exemples d'implémentation.

---

## 1. Blue/Green Deployment

### Principe

La stratégie Blue/Green consiste à maintenir deux environnements de production identiques mais isolés :

- **Blue** : l’environnement actuel en production.  
- **Green** : une nouvelle version de l’application déployée et testée avant la mise en production.

La bascule se fait simplement au niveau du routeur ou du service d’équilibrage de charge, qui redirige le trafic du Blue vers le Green.

### Avantages

- Basculement instantané, quasi zéro downtime.  
- Possibilité de rollback immédiat en revenant vers Blue.  
- Tests complets réalisables sur l’environnement Green avant la mise en production effective.

### Inconvénients

- Double infrastructure coûteuse (ressources dupliquées).  
- Complexité parfois accrue dans la synchronisation des données entre Blue et Green.

### Exemple

Un Ingress Controller Kubernetes route le trafic vers `service-blue` (version 1). Quand la version 2 (`service-green`) est prête, la règle d’Ingress est changée pour pointer vers cette dernière.

---

## 2. Canary Deployment

### Principe

Le déploiement Canary introduit progressivement la nouvelle version sur une partie restreinte de la production.

Par exemple, 5% du trafic est dirigé vers la version nouvelle (Canary), tandis que 95% continue sur la version stable. En l’absence de problèmes, le pourcentage est augmenté jusqu’à 100%.

### Avantages

- Risque réduit en limitant l’impact initial aux seuls utilisateurs canarisés.  
- Possibilité de monitorer précisément la nouvelle version avec un retour d’expérience réel.  
- Résilience : rollback granulaire.

### Inconvénients

- Complexité de gestion du routage et du monitoring.  
- Nécessite des outils d’observabilité avancés (metrics, tracing).

### Exemple

Sur Kubernetes, Istio ou Linkerd permettent la mise en place aisée de canary en ajustant les règles de routage du service mesh.

---

## 3. Rolling Update

### Principe

Le Rolling Update remplace progressivement les instances de l’application, sans interruption du service. Les pods de l’ancienne version sont arrêtés au fur et à mesure que les nouveaux pods démarrent.

Dans Kubernetes, c’est la stratégie par défaut des Deployments.

### Avantages

- Pas besoin d’infrastructures duplicatas.  
- Processus entièrement automatisé avec intégration des probes de santé pour éviter la propagation d’erreurs.

### Inconvénients

- Pas de contrôle fin sur le trafic comme avec Canary.  
- Le rollback peut être plus complexe en production si le problème n’est détecté qu’après bascule.

### Exemple

Déploiement Kubernetes avec Rolling Update :

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
```

Cette configuration remplace un pod à la fois, en gardant toujours au moins (nombre total - 1) pods disponibles.

---

## 4. Synthèse comparative

| Stratégie      | Downtime   | Risque       | Complexité implémentation   | Coût infrastructure  | Monitoring nécessaire |
|----------------|------------|--------------|-----------------------------|----------------------|----------------------|
| Blue/Green     | quasi nul  | faible       | modérée                    | élevé (double infra) | standard             |
| Canary         | nul        | très faible  | élevée (routage fin, métriques) | moyen               | avancé               |
| Rolling Update | nul à faible | moyen       | faible                      | faible               | standard             |

---

## Sources utilisées

- Kubernetes Docs - *Rolling Updates*: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-update-deployment  
- Martin Fowler - *Blue/Green Deployment*: https://martinfowler.com/bliki/BlueGreenDeployment.html  
- The New Stack - *What is Canary Deployment?*: https://thenewstack.io/what-is-canary-deployment/  
- CNCF Blog, *Service Mesh and Canary Deployments*: https://www.cncf.io/blog/2019/07/23/introduction-to-canary-releases-with-istio/  

---

La sélection de la stratégie dépend du contexte opérationnel, des moyens techniques et des exigences métiers. Blue/Green privilégie la simplicité et la sécurité à coût plus élevé, Canary offre rapidité et granularité aux utilisateurs, tandis que Rolling Update combine efficacité et automatisation pour la majorité des cas. Connaître ces approches permet d’adapter le déploiement continu pour un maximum de fiabilité et de performance.