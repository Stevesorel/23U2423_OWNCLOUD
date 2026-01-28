# Déploiement OwnCloud + Collabora Online

## 1. Informations étudiant
- **Nom** : TAMWO MOTCHEYO
- **Prénom** : STEVE SOREL
- **Matricule** : 23U2423
- **Option** : Systèmes et Réseaux
- **Cours** : INF3611 - Administration Systèmes

## 2. URL de l'application
**https://23U2423.systeme-res30.app**

## 3. Description de l'application et cas d'utilisation en entreprise

### OwnCloud
OwnCloud est une plateforme de stockage cloud open-source auto-hébergée qui permet le partage et la synchronisation de fichiers. Elle offre des fonctionnalités similaires à Dropbox ou Google Drive, mais avec un contrôle total sur les données.

### Collabora Online
Collabora Online est une suite bureautique collaborative basée sur LibreOffice, permettant l'édition en temps réel de documents Word, Excel et PowerPoint directement dans le navigateur.

### Cas d'utilisation en entreprise
1. **Stockage sécurisé** : Hébergement interne de documents sensibles
2. **Collaboration** : Édition simultanée de documents par plusieurs utilisateurs
3. **Contrôle d'accès** : Gestion fine des permissions de fichiers
4. **Synchronisation** : Accès aux fichiers depuis n'importe quel appareil
5. **Conformité** : Respect des réglementations sur la protection des données (RGPD, etc.)

## 4. Instructions pour démarrer l'application
```bash
# 1. Se connecter au VPS
ssh [votre_user]@37.60.250.220

# 2. Se rendre dans le dossier de déploiement
cd ~/owncloud_23U2423

# 3. Lancer les conteneurs Docker
docker compose up -d

# 4. Vérifier l'état des services
docker compose ps

# 5. Installer OwnCloud (première fois seulement)
# Accéder à https://23U2423.systeme-res30.app et suivre l'assistant d'installation
# ou utiliser la CLI :
docker exec owncloud_app_23U2423 php occ maintenance:install \
  --database=mysql \
  --database-name=owncloud \
  --database-user=owncloud \
  --database-pass=DbPass123 \
  --database-host=owncloud-db \
  --admin-user=admin \
  --admin-pass=admin
```

## 5. Identifiants de connexion

### Interface Web OwnCloud
- **URL** : https://23U2423.systeme-res30.app
- **Utilisateur** : admin
- **Mot de passe** : admin

## 6. Commandes utiles
```bash
# Voir les logs en temps réel
docker compose logs -f

# Redémarrer les services
docker compose restart

# Arrêter les services
docker compose stop

# Supprimer complètement l'installation
docker compose down -v
sudo rm -rf owncloud_collabora.app/

# Vérifier les ports utilisés
docker compose ps
```

## 7. Architecture technique

### Services déployés
- **MariaDB 10.6** : Base de données (port interne)
- **OwnCloud** : Plateforme de stockage (port 5581 → HTTPS via Nginx)
- **Collabora Online** : Suite bureautique (port 9981 → HTTPS via Nginx)

### Volumes persistants
- `./owncloud_collabora.app/mysql` : Données MariaDB
- `./owncloud_collabora.app/owncloud_data` : Fichiers utilisateurs
- `./owncloud_collabora.app/config` : Configuration OwnCloud
- `./owncloud_collabora.app/apps` : Applications OwnCloud

### Réseau
- Réseau Docker bridge : `owncloud_network`
- Reverse proxy Nginx avec certificat SSL wildcard

## 8. Dépannage

### Les conteneurs ne démarrent pas
```bash
docker compose logs
```

### Problème de permissions
```bash
sudo chown -R www-data:www-data owncloud_collabora.app/owncloud_data
sudo chown -R 999:999 owncloud_collabora.app/mysql
```

### Réinitialiser l'installation
```bash
docker compose down -v
sudo rm -rf owncloud_collabora.app/
mkdir -p owncloud_collabora.app/{mysql,owncloud_data,config,apps}
docker compose up -d
```

---

**Date de déploiement** : 28 Janvier 2026  
**Version** : OwnCloud latest + Collabora latest  
**Étudiant** : TAMWO MOTCHEYO STEVE SOREL (23U2423)