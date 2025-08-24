# Stratégies de branching Git : GitFlow et Trunk-Based Development

Dans la gestion de version avec Git, la stratégie de branching (gestion des branches) structure la manière dont les développeurs créent, modifient et intègrent le code. Deux approches largement utilisées sont **GitFlow** et **Trunk-Based Development (TBD)**, chacune adaptée à des contextes et objectifs différents. Cet article compare ces méthodes pour vous aider à choisir ou mieux comprendre la stratégie adaptée à votre projet.

---

## 1. GitFlow : un modèle structuré pour projets avec versions officielles

GitFlow, proposé par Vincent Driessen en 2010, est une stratégie qui formalise le cycle de vie des branches autour de concepts clairs.

### Caractéristiques principales

- **Branches principales** :  
  - `main` (ou master) : code en production, stable.  
  - `develop` : branche d’intégration, regroupant les fonctionnalités validées.

- **Branches secondaires** :  
  - **Feature branches** : créées à partir de `develop` pour développer des fonctions isolées et fusionnées dans `develop` une fois terminées.  
  - **Release branches** : créées à partir de `develop` pour préparer une version (test, corrections). Fusionnées ensuite dans `main` et `develop`.  
  - **Hotfix branches** : créées à partir de `main` pour corriger urgemment un bug en production.

### Avantages

- Processus clair et bien défini.  
- Permet de gérer plusieurs versions simultanément (par exemple maintenances).  
- Bon contrôle qualité avant déploiement.

### Inconvénients

- Complexité avec plusieurs branches actives.  
- Moins adapté aux cycles de livraison en continu car les merges peuvent s’accumuler.

### Exemple de flux GitFlow classique

1. Création d’une branche feature : `git checkout -b feature/login develop`  
2. Développement et commits sur `feature/login`.  
3. Merge dans `develop` après validation via pull request.  
4. Une fois prêt, création d’une `release/1.0` pour tests et corrections.  
5. Merge final dans `main` (production) et dans `develop`.

---

## 2. Trunk-Based Development : développement continu et intégration fréquente

Le TBD est une méthode plus simple et légère qui encourage à travailler directement sur une branche unique, souvent nommée `main` ou `trunk`.

### Principes clés

- Les développeurs créent de très courtes branches (ou travaillent directement sur `main`) avec des commits fréquents.  
- Les branches de courte durée sont fusionnées plusieurs fois par jour dans `main`.  
- L’intégration continue est fortement encouragée avec tests automatisés systématiques.  
- Les fonctionnalités non terminées peuvent être intégrées via des flags de fonctionnalité (feature toggles) pour déployer du code incomplet sans impact.

### Avantages

- Cycle très court, favorisant la livraison rapide.  
- Moins de conflits complexes liés à de longues branches.  
- Découverte rapide des bugs via intégration continue.

### Inconvénients

- Moins adapté aux équipes ayant des processus très rigides.  
- Nécessite une bonne culture de tests automatisés et de qualité.

### Exemple dans un contexte TBD

- Le développeur crée une branche `fix/login-bug` à partir de `main`.  
- Il pousse sa correction rapidement via une pull request.  
- L’équipe valide puis fusionne dans `main` dans la journée.  
- Le code est déployé automatiquement via la pipeline CI/CD.

---

## Comparaison synthétique

| Critères             | GitFlow                                  | Trunk-Based Development                   |
|----------------------|-----------------------------------------|-------------------------------------------|
| Nombre de branches   | Plusieurs (main, develop, feature, release, hotfix) | Principalement `main` + courtes branches |
| Fréquence d’intégration | Moins fréquente, synchronisation des branches | Quotidienne ou plusieurs fois/jour         |
| Complexité          | Plus complexe, adapté aux releases planifiées | Simple, adapté à l’agilité et au CI/CD     |
| Adapté pour          | Projets avec versions stables, release planifiées | Projets exigeant rapidité et feedback rapide |
| Gestion des fonctionnalités | Branches dédiées, versionnage clair      | Feature toggles, intégration progressive |

---

## Sources utilisées

- Git SCM Book, *Branching Strategies* : https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows  
- Atlassian, *GitFlow vs GitHub flow vs GitLab flow* : https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow  
- ThoughtWorks, *Trunk-Based Development* : https://trunkbaseddevelopment.com/  
- Martin Fowler, *Feature Toggles* : https://martinfowler.com/articles/feature-toggles.html  

---

Les choix entre GitFlow et Trunk-Based Development reposent sur la nature du projet, la taille de l’équipe et les exigences en termes de fréquence de livraison. GitFlow offre un contrôle structuré pour des versions planifiées, tandis que TBD privilégie la simplicité et la rapidité dans un contexte d’intégration continue et livraison continue (CI/CD).