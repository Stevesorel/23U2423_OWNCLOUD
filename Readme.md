Déploiement OwnCloud + Collabora Online avec HTTPS
INF3611 – Administration Systèmes et Réseaux
1. Informations étudiant

Nom : TAMWO MOTCHEYO

Prénom : STEVE SOREL

Matricule : 23U2423

Option : Systèmes et Réseaux

Cours : INF3611

URL de l’application :
https://23U2423.systeme-res30.app

2. Description de l’application

Ce projet consiste au déploiement d’une plateforme collaborative sécurisée composée de :

OwnCloud : solution de stockage cloud open-source auto-hébergée

MariaDB : base de données relationnelle

Collabora Online : suite bureautique collaborative

NGINX : reverse proxy HTTPS

Let’s Encrypt + Certbot : génération de certificat TLS

L’application est accessible via HTTPS conformément aux exigences du TP.

3. Rôle de l’application en entreprise

Cette solution peut être utilisée dans une entreprise pour :

Centraliser les documents internes

Permettre l’édition collaborative en temps réel

Assurer la confidentialité des données

Héberger les données en interne (indépendance cloud public)

Mettre en place un contrôle d’accès précis

Garantir la conformité aux normes de sécurité et protection des données

Elle remplace des services comme Google Drive ou Microsoft 365 tout en gardant le contrôle total des données.

4. Architecture technique
Services Docker déployés
1) MariaDB

Base de données utilisée par OwnCloud pour stocker :

Comptes utilisateurs

Métadonnées

Paramètres système

2) OwnCloud

Application web permettant :

Upload et partage de fichiers

Gestion des utilisateurs

Synchronisation multi-appareils

3) Collabora Online

Suite bureautique permettant :

Modifier des fichiers Word, Excel, PowerPoint

Collaboration en temps réel

Intégration directe dans OwnCloud

4) NGINX (Reverse Proxy)

Gère le trafic HTTPS

Redirige HTTP vers HTTPS

Transmet les requêtes vers OwnCloud et Collabora

5. Réseau Docker

Un réseau bridge personnalisé a été créé :

res30_23U2423_net

Ce réseau permet :

Communication interne sécurisée entre conteneurs

Isolation du projet

Respect de la nomenclature académique

6. Volumes persistants (Bind Mount)

Les données sont persistées grâce à des bind volumes :

./data/db              → /var/lib/mysql
./data/owncloud        → /var/www/html

Rôle :

Empêcher la perte des données si les conteneurs sont supprimés

Assurer la persistance des fichiers utilisateurs

Conserver la configuration

7. Explication complète des variables d’environnement (.env)
Informations générales
DOMAIN

Nom de domaine public utilisé pour accéder à l’application via HTTPS.

Variables Base de données
MYSQL_ROOT_PASSWORD

Mot de passe administrateur root de MariaDB.
Permet d’administrer la base de données.

DB_TYPE

Type de base de données utilisé par OwnCloud (mysql).

DB_NAME

Nom de la base de données créée pour OwnCloud.

DB_USER

Utilisateur dédié à OwnCloud pour accéder à la base.

DB_PASSWORD

Mot de passe associé à DB_USER.

Variables OwnCloud
OWNCLOUD_ADMIN_USERNAME

Nom d’utilisateur administrateur principal de la plateforme.

OWNCLOUD_ADMIN_PASSWORD

Mot de passe de l’administrateur OwnCloud.

OWNCLOUD_DB_HOST

Nom du service Docker correspondant à la base de données (owncloud-db).

OVERWRITEPROTOCOL

Force l’utilisation du protocole HTTPS.

OVERWRITEHOST

Domaine officiel utilisé par OwnCloud.

Variables Collabora
COLLABORA_DOMAIN

Domaine autorisé à communiquer avec Collabora.

COLLABORA_USERNAME

Utilisateur d’authentification pour Collabora.

COLLABORA_PASSWORD

Mot de passe associé à Collabora.

8. Configuration NGINX (Reverse Proxy)

Fichier :

/etc/nginx/sites-available/23U2423.systeme-res30.app

Contenu :

server {
    listen 80;
    server_name 23U2423.systeme-res30.app;


    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }


    location / {
        return 301 https://$host$request_uri;
    }
}


server {
    listen 443 ssl;
    server_name 23U2423.systeme-res30.app;


    ssl_certificate /etc/letsencrypt/live/23U2423.systeme-res30.app/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/23U2423.systeme-res30.app/privkey.pem;


    location / {
        proxy_pass http://localhost:5581;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }


    location /loleaflet {
        proxy_pass http://localhost:9981;
    }
}
9. Rôle de Let’s Encrypt et Certbot
Let’s Encrypt

Autorité de certification gratuite permettant de générer des certificats SSL/TLS reconnus mondialement.

Certbot

Client permettant :

Génération automatique du certificat

Installation automatique dans NGINX

Renouvellement automatique avant expiration

10. Génération du certificat TLS
1) Installation
sudo apt update
sudo apt install certbot python3-certbot-nginx
2) Génération du certificat
sudo certbot --nginx -d 23U2423.systeme-res30.app
3) Test du renouvellement
sudo certbot renew --dry-run
11. Emplacement des certificats

Les certificats sont stockés dans :

/etc/letsencrypt/live/23U2423.systeme-res30.app/

Fichiers importants :

fullchain.pem

privkey.pem

12. Instructions de démarrage
1) Se connecter au serveur
ssh user@ip_du_serveur
2) Aller dans le dossier du projet
cd owncloud_23U2423
3) Lancer les conteneurs
docker compose up -d
4) Vérifier les services
docker compose ps
13. Commandes utiles

Voir les logs :

docker compose logs -f

Redémarrer :

docker compose restart

Arrêter :

docker compose stop

Supprimer complètement :

docker compose down -v
14. Sécurité mise en place

Accès HTTPS obligatoire

Certificat TLS valide

Variables sensibles externalisées dans .env

Réseau Docker isolé

Données persistantes

15. Conclusion

Ce projet met en place une infrastructure complète de collaboration d’entreprise :

Sécurisée (HTTPS + TLS)

Persistante (Volumes bind)

Structurée (Réseau dédié)

Professionnelle (Reverse proxy + Certbot)

L’application est accessible publiquement via HTTPS conformément aux exigences du TP INF3611.