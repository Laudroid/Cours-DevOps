# Les concepts clés de Docker : Image, Conteneur, Dockerfile et Docker Compose

Docker est devenu un standard dans la conteneurisation grâce à des concepts simples mais puissants. Pour maîtriser Docker, il est important de comprendre quatre notions fondamentales : les images, les conteneurs, les Dockerfiles et Docker Compose. Cet article explique ces concepts en les illustrant par des exemples pratiques.

---

## 1. Docker Image : modèle immuable d’une application

Une **image Docker** est un template en lecture seule qui contient tout le nécessaire pour exécuter une application : le code, les bibliothèques, les dépendances et la configuration système.

- Les images sont construites en couches immuables, ce qui optimise leur stockage et la rapidité des transferts.  
- Elles sont versionnées et stockées dans des registres (Docker Hub, GitLab Container Registry, etc.).  
- Elles servent de base pour créer des conteneurs.

### Exemple

L’image officielle `node:16` contient Node.js 16 préinstallé sur un système léger Linux. Vous pouvez la récupérer avec :

```bash
docker pull node:16
```

---

## 2. Conteneur : instance exécutable d’une image

Un **conteneur** est une instance active d’une image. C’est un environnement isolé dans lequel tourne un processus.

- Les conteneurs utilisent les ressources du noyau de l’hôte.  
- Ils démarrent rapidement, conservent l’état volatile (sauf volumes montés).  
- Plusieurs conteneurs peuvent être lancés simultanément à partir d’une même image.

### Exemple

Lancer un conteneur Node.js interactif :

```bash
docker run -it node:16 bash
```

Cela démarre un shell dans un conteneur basé sur l’image `node:16`.

---

## 3. Dockerfile : script de construction d’image

Un **Dockerfile** est un fichier texte décrivant la manière de construire une image Docker. Il spécifie les étapes, comme installer des paquets, copier des fichiers ou exposer des ports.

### Structure simple d’un Dockerfile

```dockerfile
FROM node:16
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["node", "index.js"]
```

- `FROM node:16` : point de départ avec l’image Node.js 16.  
- `WORKDIR` : définit le répertoire de travail dans le conteneur.  
- `COPY` et `RUN` : copie les fichiers puis installe les dépendances.  
- `CMD` : commande lancée lorsque le conteneur démarre.

### Pour construire l’image

```bash
docker build -t mon-app-node .
```

---

## 4. Docker Compose : orchestrer plusieurs conteneurs

**Docker Compose** permet de définir et gérer plusieurs conteneurs (services) dans un fichier YAML. Il simplifie le déploiement d’applications multi-conteneurs (ex : app Node.js + base de données).

### Exemple de `docker-compose.yml`

```yaml
version: "3"
services:
  app:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/app
    depends_on:
      - db

  db:
    image: postgres:14
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: appdb
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

- Définit un service `app` construit depuis un Dockerfile local.  
- Définit un service `db` basé sur PostgreSQL.  
- Gère les dépendances et les volumes.

### Démarrage

```bash
docker-compose up -d
```

---

## Sources utilisées

- Docker Docs - Concepts clés : https://docs.docker.com/get-started/overview/  
- Docker Docs - Dockerfile reference : https://docs.docker.com/engine/reference/builder/  
- Docker Docs - Docker Compose : https://docs.docker.com/compose/  
- DigitalOcean, *Docker for Beginners* : https://www.digitalocean.com/community/tutorial_series/docker-for-beginners  

---

Docker se distingue par son écosystème structuré autour d’images immuables, conteneurs légers, Dockerfiles modulaires et la gestion simplifiée de multi-conteneurs avec Docker Compose. Maîtriser ces concepts est indispensable pour construire, déployer et scaler des applications conteneurisées.