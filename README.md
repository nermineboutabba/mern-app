# TP2 – Conteneurisation d'une Application MERN avec Docker et Docker Compose

## Vue d'Ensemble
Ce projet est une application full-stack **MERN** composée de trois services conteneurisés et orchestrés avec **Docker** et **Docker Compose** :
- **`client`** (Frontend React) : construit avec `npm run build` et servi sur le port `3000` via `serve`.
- **`server`** (API REST Express / Node.js) : exposé sur le port `9000` et connecté à MongoDB via `MONGO_URI`.
- **`mongodb`** (Base de données MongoDB) : persistance assurée par le volume nommé `mern-data` et contrôlée par un `healthcheck`.

---

## Architecture et Configuration
- **Réseau Docker** : `mern-network` (driver `bridge`) permettant la résolution DNS par nom de service (`mongodb`, `server`, `client`).
- **Volume persistant** : `mern-data` monté sur `/data/db` dans le conteneur `mongodb`.
- **Variables d'environnement** (voir `.env.example`) :
  - `MONGO_URI=mongodb://mongodb:27017/mern`
  - `REACT_APP_API_URL=http://localhost:9000`

---

## Commandes Utiles (Construction, Démarrage, Diagnostic et Arrêt)

### 1. Construction manuelle des images
```bash
# Construire l'image du serveur
cd server
docker build -t mern-server .

# Construire l'image du client
cd ../client
docker build -t mern-client .
```

### 2. Démarrage avec Docker Compose
```bash
# Construire et lancer tous les services en arrière-plan
docker compose up -d --build
```
- **Interface React (Client)** : [http://localhost:3000](http://localhost:3000)
- **API Express (Serveur)** : [http://localhost:9000](http://localhost:9000)

### 3. Diagnostic et surveillance
```bash
# Vérifier l'état des conteneurs et du healthcheck
docker compose ps

# Consulter les 50 dernières lignes de journaux du serveur
docker compose logs --tail=50 server

# Suivre les journaux de tous les services en temps réel
docker compose logs -f
```

### 4. Arrêt des services
```bash
# Arrêter et supprimer les conteneurs (tout en conservant le volume mern-data)
docker compose down
```

---

## Étapes Réalisées, Tests et Difficultés Résolues

### Étapes réalisées et tests effectués
1. Création des fichiers `Dockerfile` et `.dockerignore` pour `server` et `client`.
2. Déploiement manuel sur le réseau `mern-network` avec le volume `mern-data`.
3. Orchestration complète via `docker-compose.yml` avec `healthcheck` sur MongoDB (`mongosh`) et dépendance `condition: service_healthy`.
4. Test fonctionnel de bout en bout (ajout, affichage, modification et suppression d'employés depuis React vers MongoDB) et vérification de la persistance des données après `docker compose down` puis `docker compose up -d`.

### Difficultés résolues
- **Script `npm start` manquant dans `server/package.json`** : ajout de `"start": "node server.js"` dans la section `scripts`.
- **Communication navigateur ↔ API vs réseau interne Docker** : configuration de `REACT_APP_API_URL=http://localhost:9000` pour le navigateur client et de `MONGO_URI=mongodb://mongodb:27017/mern` pour la communication interne entre conteneurs.
- **Ordre de démarrage entre `server` et `mongodb`** : ajout d'un `healthcheck` sur `mongodb` et de `condition: service_healthy` dans `depends_on` pour éviter que l'API ne démarre avant que MongoDB ne soit prêt.
