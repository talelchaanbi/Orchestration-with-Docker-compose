# Rapport TP3 - Orchestration avec Docker Compose

## Informations
- Etablissement: Institut Superieur d'Informatique
- Departement: GTR
- Module: Cloud Computing et Virtualisation
- Groupe: M1 SSII
- Enseignant: Safa Rejichi
- Etudiant: Talel Chaanbi
- Date: 19/04/2026

## Objectifs du TP
- Manipuler Docker Compose.
- Orchestrer deux services: Application Web et Base de donnees.

## Partie 1 - Installation de Docker Compose
### Commandes utilisees
- sudo apt-get update
- sudo apt-get install docker-compose-plugin
- docker compose version

### Verification
Docker Compose est installe et la commande docker compose est disponible.

Capture a inserer:
![Capture 01 - docker compose version](captures/01-docker-compose-version.png)

## Partie 2 - Orchestration avec docker-compose

## I. Creation et demarrage des conteneurs
Arborescence du projet:
- sources/app/db-config.php
- sources/app/index.php
- sources/app/validation.php
- sources/db/articles.sql
- sources/db/Dockerfile
- docker-compose.yml

### Dockerfile Base de donnees
Le service db est construit avec un Dockerfile dedie:
- Base image: mysql:5.7
- Script init: articles.sql copie dans /docker-entrypoint-initdb.d

Capture a inserer:
![Capture 02 - arborescence du projet](captures/02-arborescence-projet.png)

### Explication du fichier docker-compose.yml
Le fichier orchestre deux services sur le meme reseau bridge app_net:
- service db:
  - build depuis sources/db
  - variables MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD
  - volume nomme db_data pour persister les donnees MySQL
  - healthcheck mysqladmin ping pour verifier la disponibilite de la base
  - port publie 3306:3306
- service app:
  - build depuis sources/app
  - port publie 8080:80
  - depends_on avec condition service_healthy pour attendre db
  - connecte au reseau app_net
- reseau:
  - app_net avec driver bridge
- volume:
  - db_data

Capture a inserer:
![Capture 03 - docker-compose.yml](captures/03-docker-compose-yml.png)

### Changement dans db-config.php
Le host de connexion a ete change de localhost vers db:
- DSN utilise: mysql:host=db;dbname=test

Explication:
- localhost pointe vers le conteneur courant.
- la base est dans un autre conteneur.
- le nom de service db devient un nom DNS interne sur app_net.
- app se connecte donc a db via le reseau Docker.

Capture a inserer:
![Capture 04 - db-config.php host db](captures/04-db-config-host-db.png)

### Demarrage
Commande executee:
- docker compose up -d --build

Verification:
- docker compose ps

Capture a inserer:
![Capture 05 - docker compose up](captures/05-compose-up.png)
![Capture 06 - docker compose ps](captures/06-compose-ps.png)

## II. Test de la page web
URL testee:
- http://localhost:8080

Resultat:
- La page Articles s'affiche correctement.

Capture a inserer:
![Capture 07 - page accueil/articles](captures/07-page-web.png)

## III. Ajout d'un article et verification
Etapes:
- Remplir le formulaire (titre, auteur, contenu)
- Valider
- Verifier la presence de l'article dans la liste

Resultat:
- L'article est bien ajoute et affiche.

Captures a inserer:
![Capture 08 - formulaire rempli](captures/08-formulaire-rempli.png)
![Capture 09 - article ajoute](captures/09-article-ajoute.png)

## IV. Adresses IP des conteneurs et relation avec app_net
Commandes:
- docker inspect tp3_app --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
- docker inspect tp3_db --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'
- docker network inspect tp3orchestrationavecdockercompose_app_net

Resultats observes:
- IP conteneur app: 172.18.0.3
- IP conteneur db: 172.18.0.2
- Reseau app_net:
  - driver: bridge
  - subnet: 172.18.0.0/16
  - gateway: 172.18.0.1

Explication de la relation avec app_net:
- app et db sont attaches au meme reseau bridge.
- ils communiquent en IP privee interne.
- le service app joint db par son nom DNS db et non par localhost.
- app_net isole ce trafic du reste de l'hote.

Capture a inserer:
![Capture 10 - docker inspect IP](captures/10-docker-inspect-ip.png)
![Capture 11 - docker network inspect](captures/11-network-inspect.png)

## Conclusion
Ce TP a permis de:
- mettre en place une orchestration multi-conteneurs avec Docker Compose,
- separer clairement application web et base de donnees,
- valider la communication reseau inter-conteneurs via app_net,
- verifier l'ajout d'articles avec persistance des donnees.

## Capture generale pour le depot
Inserer ici une capture finale qui montre en meme temps:
- docker compose ps avec app Up et db Up (healthy)
- le navigateur sur la page Articles avec un article present
- optionnel: sortie docker inspect des IP

![Capture 12 - capture generale depot](captures/12-capture-generale-depot.png)

## Instructions export PDF
- Ouvrir ce fichier Markdown dans VS Code.
- Exporter en PDF avec une extension Markdown PDF ou via impression PDF.
- Deposer le PDF final sur la plateforme demandee.
