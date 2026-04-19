# TP3 - Orchestration avec Docker Compose

## 1) Demarrer Docker (si necessaire)

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Verifie:

```bash
docker ps
```

## 2) Lancer le TP

Depuis la racine du dossier TP:

```bash
docker compose up -d --build
```

Si ton environnement n'a que l'ancien binaire:

```bash
docker-compose up -d --build
```

Si la commande docker compose n'existe pas:

```bash
mkdir -p ~/.docker/cli-plugins
curl -SL https://github.com/docker/compose/releases/download/v2.29.7/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose
chmod +x ~/.docker/cli-plugins/docker-compose
docker compose version
```

Si tu as une erreur permission denied sur /var/run/docker.sock:

```bash
sudo usermod -aG docker $USER
newgrp docker
docker ps
```

Ensuite relance:

```bash
docker compose up -d --build
```

## 3) Tester

- Ouvre http://localhost:8080
- Ajoute un article via le formulaire
- Verifie qu'il apparait dans la liste

## 4) Verifications utiles

```bash
docker compose ps
docker compose logs -f app
docker compose logs -f db
docker network inspect tp3_app_net
```

## 5) Arreter

```bash
docker compose down
```

## 6) Depot Git (si ton enseignant demande un repo)

```bash
git init
git add .
git commit -m "TP3 Docker Compose"
```

Puis lie ton depot distant:

```bash
git remote add origin <URL_DU_REPO>
git branch -M main
git push -u origin main
```

## Fichiers principaux

- docker-compose.yml
- sources/app/Dockerfile
- sources/app/db-config.php
- sources/app/index.php
- sources/app/validation.php
- sources/db/articles.sql
