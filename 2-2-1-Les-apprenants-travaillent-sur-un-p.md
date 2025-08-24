# Travail collaboratif avec Git : gérer les branches, les merges et les conflits sur GitHub/GitLab

Collaborer efficacement sur un projet logiciel repose sur une bonne maîtrise des outils de gestion de version, notamment Git, et des plateformes collaboratives comme GitHub ou GitLab. Travailler chacun sur sa branche, effectuer des merges et gérer les conflits sont des activités centrales lorsque plusieurs développeurs contribuent à un même projet.

Voici un guide pratique et structuré pour comprendre et appliquer ces notions dans un cadre collaboratif.

---

## 1. Travailler sur une branche dédiée

Dans un projet partagé, chaque collaborateur doit créer une branche spécifique pour ses modifications, ce qui évite d’altérer directement la branche principale (`main` ou `master`). Ce fonctionnement garantit une isolation du travail et limite les risques de conflits.

**Commandes clés :**

- Créer une branche locale et s’y positionner :

```bash
git checkout -b feature/nom-de-la-fonctionnalité
```

- Pousser la branche sur le dépôt distant (GitHub/GitLab) :

```bash
git push -u origin feature/nom-de-la-fonctionnalité
```

**Exemple :** Alice crée une branche `feature/login` pour développer un système d’authentification tandis que Bob travaille dans une branche `feature/profile-update`.

---

## 2. Effectuer des commits et pousser régulièrement

Les développeurs effectuent des commits clairs et fréquents pour enregistrer les étapes de leur travail. Ils poussent ensuite leurs modifications pour les sauvegarder sur le dépôt distant.

```bash
git add .
git commit -m "Ajout du formulaire de login"
git push
```

Cette habitude facilite le suivi et la revue du code.

---

## 3. Merging via Pull Requests (GitHub) ou Merge Requests (GitLab)

Une fois qu’une fonctionnalité est terminée, il faut fusionner la branche dans la branche principale. Ce processus se fait par une pull request (PR) ou merge request (MR) sur la plateforme.

- La PR/MR permet :  
  - La revue de code par les pairs.  
  - L’exécution automatique des tests via pipelines CI/CD.  
  - La discussion des éventuels ajustements.

**Exemple dans GitHub :**

Alice ouvre une pull request de `feature/login` vers `main`. Les membres de l’équipe commentent et valident avant fusion.

---

## 4. Résolution de conflits

Quand plusieurs développeurs modifient la même partie d’un fichier, Git détecte un conflit lors du merge. Il faut alors résoudre manuellement ces conflits.

**Processus de résolution :**

- Git signale les conflits lors du `git merge` ou `git pull`.  
- Le fichier concerné contient des marqueurs délimitant les différences entre branches :

```text
<<<<<<< HEAD
Version actuelle (branche cible)
=======
Version depuis la branche source
>>>>>>> feature/nom-de-la-fonctionnalité
```

- L’utilisateur édite le fichier, choisit la version correcte ou fusionne les changements.  
- Après sauvegarde, on marque le conflit comme résolu :

```bash
git add fichier_conflit
git commit
```

**Exemple concret :** Bob modifie la même ligne que Alice dans un fichier README. Lors de la fusion, Git lui demande de résoudre le conflit en acceptant une des versions ou en combinant les deux.

---

## 5. Bonnes pratiques pour le travail collaboratif avec Git

| Pratique                     | Explication                                  |
|------------------------------|----------------------------------------------|
| Puller souvent                | Récupérer régulièrement les changements du dépôt distant avec `git pull` pour éviter les divergences trop importantes. |
| Communiquer                   | Utiliser les commentaires dans les PR/MR pour clarifier les choix de code. |
| Rester sur des branches courtes | Garder les branches actives peu longtemps pour limiter les conflits.           |
| Utiliser des messages clairs | Décrire précisément les commits pour faciliter la revue et le suivi.            |
| Exécuter les tests avant merge | Garantir que la branche est fonctionnelle et ne casse pas la branche principale. |

---

## 6. Environnement pratique : GitHub et GitLab

- **GitHub** est la plateforme la plus utilisée, bénéficiant d’une intégration CI/CD via GitHub Actions.  
- **GitLab** offre un système CI/CD intégré dès l'installation, avec pipelines configurables depuis un fichier `.gitlab-ci.yml`.

Ces plateformes proposent des interfaces web intuitives pour créer, suivre, commenter les PR/MR, et gérer les droits d’accès.

---

## Sources utilisées

- GitHub Docs, *About pull requests* : https://docs.github.com/en/pull-requests  
- GitLab Docs, *Merge requests* : https://docs.gitlab.com/ee/user/project/merge_requests/  
- Atlassian, *How to resolve merge conflicts* : https://www.atlassian.com/git/tutorials/using-branches/merge-conflicts  
- Git SCM Book, *Collaborating with Git* : https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows  

---

Travailler en équipe avec Git via GitHub ou GitLab nécessite de structurer son travail autour des branches, d’utiliser les pull/merge requests pour valider les modifications, et de savoir résoudre rapidement les conflits. Cette pratique collaborative permet d’assurer la qualité et la stabilité du code tout en accélérant les livraisons.