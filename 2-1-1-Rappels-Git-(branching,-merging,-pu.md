# Git et gestion de version : rappel sur branching, merging et pull requests

Git est aujourd’hui le système de gestion de version décentralisé le plus largement utilisé pour gérer le code source dans les projets logiciels. Maîtriser les concepts de branching, merging et pull requests est fondamental pour garantir une collaboration fluide et éviter les conflits. Voici un rappel précis de ces concepts clés.

---

## 1. Branching (Gestion des branches)

La branche (branch) est une version indépendante du code sur laquelle on peut travailler sans impacter la branche principale (souvent nommée `main` ou `master`). Le branching permet de développer de nouvelles fonctionnalités, corriger des bugs ou tester des idées en parallèle.

- **Fonctionnement :**  
  Créer une branche peut se faire simplement avec `git branch <nom>` ou en une commande combinée `git checkout -b <nom>`. Chaque branche contient un pointeur vers une série de commits.

- **Types de branches :**  
  - **Feature branches** : pour développer une fonctionnalité.  
  - **Bugfix branches** : isoler la correction d’un défaut.  
  - **Release branches** : préparer une version finale.

**Exemple :** Un développeur crée une branche `feature/login` pour travailler sur l’implémentation de la connexion utilisateur, sans perturber la branche principale.

---

## 2. Merging (Fusion des branches)

Le merging consiste à récupérer les modifications d’une branche dans une autre. Typiquement, on fusionne une branche de fonctionnalité dans la branche principale une fois le travail validé.

- **Types de merge :**  
  - **Merge commit classique** : crée un commit de fusion qui relie les histoires des deux branches.  
  - **Fast-forward merge** : si la branche cible n’a pas divergé, on avance simplement le pointeur.  
  - **Rebase** : réécrit les commits de la branche courante pour les appliquer au-dessus de la branche cible, ce qui conserve un historique linéaire.

- **Résolution des conflits :**  
  Lorsqu’un même fichier est modifié à deux endroits incompatibles, Git demande une résolution manuelle.

**Exemple :** Après avoir terminé la fonctionnalité sur `feature/login`, le développeur fusionne celle-ci dans `main`, soit via une pull request, soit par ligne de commande :

```bash
git checkout main
git merge feature/login
```

---

## 3. Pull requests (Demandes de fusion)

La pull request (PR) est un mécanisme collaboratif, généralement sur des plateformes en ligne comme GitHub, GitLab ou Bitbucket.

- **Objectif :**  
  Discuter, revoir et valider les modifications proposées avant de les intégrer dans la branche principale.

- **Processus :**  
  1. Le développeur pousse sa branche sur le dépôt distant.  
  2. Il ouvre une PR, décrivant les changements et motivant leur acceptation.  
  3. Les reviewers fournissent leur feedback (commentaires, suggestions).  
  4. Après validation, la PR est fusionnée, souvent via un merge commit automatique ou un rebase.

- **Avantages :**  
  - Contrôle qualité.  
  - Documentation des changements.  
  - Communication entre développeurs.

**Exemple :** Une équipe utilise GitHub pour gérer ses PR. Chaque PR déclenche automatiquement une pipeline CI qui exécute les tests. La fusion est autorisée uniquement si tous les tests passent et si la revue est approuvée.

---

## Points clés à retenir

| Concept       | Description                                        | Commandes ou outils                      |
|---------------|--------------------------------------------------|-----------------------------------------|
| Branching     | Travail parallèle, isolation des fonctionnalités | `git branch`, `git checkout -b`         |
| Merging       | Fusion des modifications                          | `git merge`, `git rebase`                |
| Pull Requests | Revue collaborative avant fusion                  | GitHub, GitLab, Bitbucket interfaces     |

---

## Sources utilisées

- Git documentation, *Branching and Merging* : https://git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging  
- Atlassian, *Git Branching* : https://www.atlassian.com/git/tutorials/using-branches  
- GitHub Docs, *About pull requests* : https://docs.github.com/en/pull-requests  
- GitLab Docs, *What is a Merge Request?* : https://docs.gitlab.com/ee/user/project/merge_requests/

---

Ce rappel synthétique des concepts de branching, merging et pull requests en Git donne les bases nécessaires pour une gestion de version collaborative, stable et efficace, indispensable à toute démarche DevOps.