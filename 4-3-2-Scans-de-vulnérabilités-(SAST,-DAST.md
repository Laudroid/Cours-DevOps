# Scans de vulnérabilités (SAST, DAST) et gestion des secrets en DevSecOps

Intégrer la sécurité dans le cycle de vie des applications nécessite des outils et pratiques systématiques pour détecter et corriger les vulnérabilités, ainsi que pour protéger les informations sensibles. Deux types de scans s’imposent dans un pipeline de sécurité Shift-Left : le **SAST (Static Application Security Testing)** et le **DAST (Dynamic Application Security Testing)**. Parallèlement, la **gestion sécurisée des secrets** est indispensable pour éviter des fuites critiques.

---

## 1. Scans de vulnérabilités : SAST vs DAST

### a) SAST : Analyse statique

Le SAST analyse le code source ou les artefacts (bytecode, binaires) sans exécution du programme. Il identifie des failles potentielles comme les injections SQL, XSS, buffer overflows ou les mauvaises pratiques en matière de gestion des entrées.

- **Avantages** : détection précoce, intégration dans IDE/pipeline CI, large couverture des règles.  
- **Limites** : faux positifs, ne détecte pas les vulnérabilités liées à l’exécution réelle.

**Exemples d’outils SAST** : SonarQube, Checkmarx, Veracode, Semgrep.

---

### b) DAST : Analyse dynamique

Le DAST teste l’application en fonctionnement, généralement via des requêtes HTTP, pour détecter des vulnérabilités opérationnelles (failles d’authentification, erreurs de configuration, vulnérabilités serveur).

- **Avantages** : identifie les failles en conditions réelles, découvre les failles de configuration et celles liées à l’environnement.  
- **Limites** : ne peut pas analyser le code source, nécessite une application déployée et accessible.

**Exemples d’outils DAST** : OWASP ZAP, Burp Suite, Acunetix.

---

### c) Complémentarité

SAST et DAST sont complémentaires :

| Caractéristique              | SAST                       | DAST                       |
|-----------------------------|----------------------------|----------------------------|
| Analyse                     | Statique (code)            | Dynamique (runtime)        |
| Moment d’exécution          | Avant compilation/lancement| En production/staging      |
| Détection                   | Vulnérabilités code        | Vulnérabilités fonctionnelles et environnementales |
| Fausse alarme possible      | Oui                        | Moins                      |

---

## 2. Gestion des secrets

### Pourquoi sécuriser les secrets ?

Secrets comme les clés API, mots de passe, certificats exposés dans le code ou la configuration peuvent être exploités pour compromettre un système.

### Bonnes pratiques

- Ne jamais stocker les secrets en clair dans les dépôts Git ou dans les conteneurs.  
- Utiliser des outils spécialisés comme **HashiCorp Vault, AWS Secrets Manager, Azure Key Vault** ou encore des solutions intégrées Kubernetes (Secrets, Sealed Secrets).  
- Automatiser la rotation régulière des secrets.  
- Contrôler strictement les accès aux secrets via des politiques IAM (Identity and Access Management).

---

### Exemple : intégration d’un secret dans un pod Kubernetes via un Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  username: bXl1c2Vy # base64 de "myuser"
  password: bXlwYXNzd29yZA== # base64 de "mypassword"
```

Le pod peut alors utiliser ce secret monté en volume ou via des variables d’environnement.

---

## 3. Intégration dans CI/CD

- Ajouter des scans SAST et DAST dans le pipeline automatisé pour bloquer les builds contenant des vulnérabilités critiques.  
- Scanners comme Trivy peuvent combiner analyse de vulnérabilités d’image container (SCA) et détection de secrets exposés.  
- Utiliser des outils d’analyse de secrets statiques (ex : GitGuardian, TruffleHog) pour détecter les données sensibles dans l’historique Git.

---

## Sources utilisées

- OWASP, *Static Application Security Testing (SAST)* : https://owasp.org/www-community/Source_Code_Analysis  
- OWASP, *Dynamic Application Security Testing (DAST)* : https://owasp.org/www-project-dynamic-application-security-testing/  
- HashiCorp Vault Docs : https://www.vaultproject.io/docs  
- Kubernetes Docs, *Secrets* : https://kubernetes.io/docs/concepts/configuration/secret/  
- Aqua Security, *Trivy – Vulnerability Scanner*: https://aquasecurity.github.io/trivy/  

---

Automatiser les scans de vulnérabilités combinant SAST et DAST, tout en assurant une gestion rigoureuse des secrets, fait partie des piliers d’une chaîne DevSecOps robuste. Cette approche prévient les incidents liés à la sécurité applicative, tout en facilitant la conformité et la fiabilité des déploiements.