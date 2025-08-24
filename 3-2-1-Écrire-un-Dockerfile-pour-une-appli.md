# Conteneuriser une application web simple avec Dockerfile et Docker Compose

La conteneurisation permet de packager une application avec son environnement d'exécution, assurant portabilité et cohérence entre les environnements. Ce guide pratique détaille comment écrire un Dockerfile pour une application web simple (exemple Node.js) puis la déployer avec Docker Compose.

---

## 1. Écrire un Dockerfile pour l’application web

Prenons un exemple d’application Node.js minimaliste, avec un fichier `index.js` exposant un serveur HTTP :

```javascript
// index.js
const http = require('http');
const PORT = 3000;
const server = http.createServer((req, res) => {
  res.end('Hello from Dockerized Node.js app!');
});
server.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

Le fichier `package.json` associé :

```json
{
  "name": "simple-node-app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js"
  },
  "dependencies": {}
}
```

### Dockerfile simple

```dockerfile
# Étape 1 : choisir une image officielle Node.js légère
FROM node:16-alpine

# Étape 2 : définir le répertoire de travail dans le conteneur
WORKDIR /app

# Étape 3 : copier package.json et package-lock.json (si présent)
COPY package*.json ./

# Étape 4 : installer les dépendances (vide dans cet exemple)
RUN npm install

# Étape 5 : copier le reste des fichiers source
COPY . .

# Étape 6 : exposer le port sur lequel l’application écoute
EXPOSE 3000

# Étape 7 : commande lancée au démarrage du conteneur
CMD ["npm", "start"]
```

### Construction de l’image Docker

Depuis le dossier racine contenant le Dockerfile :

```bash
docker build -t simple-node-app .
```

---

## 2. Utiliser Docker Compose pour orchestrer et démarrer le conteneur

Docker Compose facilite la gestion multi-conteneurs et la configuration centralisée.

### Exemple de fichier `docker-compose.yml`

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - ./:/app
    restart: unless-stopped
```

- `build: .` : Docker Compose construit l’image depuis le Dockerfile local  
- `ports` : mappe le port 3000 local au port 3000 du conteneur  
- `volumes` : monte le dossier local pour faciliter le développement en temps réel  
- `restart` : redémarre le conteneur en cas de plantage

---

## 3. Démarrer l’application avec Docker Compose

Lancer la stack :

```bash
docker-compose up
```

L’application est accessible en local à l’URL : [http://localhost:3000](http://localhost:3000)

Pour arrêter et supprimer les conteneurs :

```bash
docker-compose down
```

---

## 4. Résumé des bonnes pratiques

| Étape             | Conseil                                 |
|-------------------|----------------------------------------|
| Dockerfile        | Favoriser une image officielle légère (ex: `node:16-alpine`) |
| Copier les dépendances d’abord | Optimiser le cache Docker en copiant d’abord `package.json` pour éviter une réinstallation inutile |
| Ports exposés     | Toujours exposer les ports utilisés par l’application |
| Volumes pour dev  | Dans Docker Compose, utiliser les volumes pour synchroniser les fichiers source et itérer rapidement |
| Redémarrage automatique | Configurer la politique de restart pour la robustesse |

---

## Sources utilisées

- Docker Docs, *Dockerfile reference* : https://docs.docker.com/engine/reference/builder/  
- Docker Docs, *Get started with Docker Compose* : https://docs.docker.com/compose/gettingstarted/  
- Node.js, *HTTP server example* : https://nodejs.org/en/docs/guides/getting-started-guide/  
- Play with Docker, *Building a Node.js app* : https://labs.play-with-docker.com/

---

Une application web simple peut être facilement conteneurisée en écrivant un Dockerfile propre et modulable, puis pilotée efficacement avec Docker Compose. Cela ouvre la voie à des déploiements reproductibles, des développements plus rapides et un contrôle accru de l’environnement applicatif.