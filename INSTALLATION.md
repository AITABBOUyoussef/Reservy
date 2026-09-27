# Guide d'installation de Reservy

Ce guide explique comment installer et lancer Reservy en local sous Windows avec XAMPP.

## 1. Prérequis

Installer les outils suivants :

| Outil | Version recommandée | Vérification |
|---|---|---|
| PHP | 8.2 ou supérieur | `php -v` |
| Composer | Version récente | `composer -V` |
| Node.js | 18 ou supérieur | `node -v` |
| npm | Fourni avec Node.js | `npm -v` |
| MySQL | Fourni avec XAMPP | Via XAMPP Control Panel |
| Git | Optionnel | `git --version` |

Le projet utilise PHP, Composer et Node.js. Vérifier que leurs répertoires sont bien ajoutés au `PATH` Windows.

## 2. Démarrer XAMPP

Dans XAMPP Control Panel :

1. Démarrer **MySQL**.
2. Apache n'est pas obligatoire pour le développement, car Laravel et Vite utilisent leurs propres serveurs.
3. Ouvrir phpMyAdmin avec `http://localhost/phpmyadmin`.

## 3. Créer la base de données

Dans phpMyAdmin :

1. Cliquer sur **New**.
2. Créer une base nommée `reservy`.
3. Choisir l'interclassement `utf8mb4_unicode_ci` si cette option est proposée.

La base peut aussi être créée avec MySQL :

```sql
CREATE DATABASE reservy
CHARACTER SET utf8mb4
COLLATE utf8mb4_unicode_ci;
```

## 4. Installer et configurer le backend

Ouvrir PowerShell et exécuter :

```powershell
cd C:\xampp\htdocs\Reservy\reservy-backend
composer install
Copy-Item .env.example .env
php artisan key:generate
```

> Si le fichier `.env` existe déjà, ne pas l'écraser. Modifier uniquement les valeurs nécessaires.

Ouvrir `reservy-backend/.env` et configurer la base de données :

```env
APP_NAME=Reservy
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://127.0.0.1:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=reservy
DB_USERNAME=root
DB_PASSWORD=
```

Si MySQL possède un mot de passe root, renseigner sa valeur dans `DB_PASSWORD`.

Nettoyer le cache de configuration puis exécuter les migrations et les seeders :

```powershell
php artisan config:clear
php artisan cache:clear
php artisan migrate --seed
```

Les migrations créent les tables nécessaires : utilisateurs, rôles, établissements, tables, catégories, produits, réservations, commande items et reviews.

## 5. Configurer les services optionnels du backend

### Mail

En local, le mailer `log` permet de tester sans serveur SMTP. Les messages sont écrits dans les logs Laravel :

```env
MAIL_MAILER=log
```

Pour un vrai envoi, remplacer cette configuration par les paramètres SMTP du fournisseur utilisé.

### Google Login

La connexion Google nécessite les identifiants OAuth configurés dans le backend et une application Google autorisée pour l'environnement local. Sans cette configuration, l'inscription et la connexion classiques restent utilisables.

### Cloudinary

Si le stockage Cloudinary est utilisé, renseigner uniquement les variables Cloudinary prévues par la configuration du projet. Ne jamais publier ces clés dans Git ou dans ce guide.

## 6. Lancer le backend

Dans le terminal backend :

```powershell
cd C:\xampp\htdocs\Reservy\reservy-backend
php artisan serve --host=127.0.0.1 --port=8000
```

L'API est disponible à :

```text
http://127.0.0.1:8000/api
```

Pour vérifier que Laravel répond, ouvrir :

```text
http://127.0.0.1:8000
```

Garder ce terminal ouvert.

## 7. Installer et configurer le frontend

Ouvrir un deuxième PowerShell :

```powershell
cd C:\xampp\htdocs\Reservy\reservy-frontend
npm install
```

Créer ou modifier `reservy-frontend/.env` :

```env
VITE_API_URL=http://127.0.0.1:8000/api
```

Le nom de variable doit rester exactement `VITE_API_URL`, car il est lu par le client Axios du frontend.

Lancer Vite :

```powershell
npm run dev
```

Ouvrir ensuite l'adresse affichée par Vite, généralement :

```text
http://localhost:5173
```

## 8. Vérification fonctionnelle

Effectuer les vérifications suivantes :

1. Ouvrir la page d'accueil.
2. Créer un compte client avec `/register`.
3. Se connecter avec `/login`.
4. Vérifier que le token est enregistré dans le navigateur.
5. Afficher les établissements.
6. Tester la création d'un établissement avec un compte gérant.
7. Tester une réservation avec un compte client.
8. Vérifier la réservation dans `/MesReservations`.
9. Tester les accès d'un administrateur.

## 9. Commandes utiles

### Backend

```powershell
php artisan route:list
php artisan migrate:status
php artisan migrate:fresh --seed
php artisan optimize:clear
php artisan storage:link
php artisan test
```

`migrate:fresh --seed` supprime toutes les tables puis les recrée. Cette commande est réservée au développement et supprime les données existantes.

### Frontend

```powershell
npm run dev
npm run lint
npm run build
npm run preview
```

## 10. Dépannage

### Erreur de connexion MySQL

Vérifier :

- MySQL est démarré dans XAMPP.
- La base `reservy` existe.
- `DB_DATABASE`, `DB_USERNAME` et `DB_PASSWORD` sont corrects.
- Le port configuré correspond à celui de MySQL.

Puis exécuter :

```powershell
php artisan config:clear
php artisan migrate --seed
```

### Le frontend retourne une erreur réseau

Vérifier :

- Laravel tourne sur `http://127.0.0.1:8000`.
- `VITE_API_URL` contient `/api`.
- Le serveur Vite a été redémarré après la modification du `.env`.
- Le token envoyé par le navigateur est encore valide.

### Les migrations échouent

Vérifier que la base est vide ou utiliser, uniquement en développement :

```powershell
php artisan migrate:fresh --seed
```

### Les images ne s'affichent pas

Vérifier le disque de stockage utilisé par Laravel, la configuration des URLs d'images et, si nécessaire :

```powershell
php artisan storage:link
```

### Les dépendances sont absentes

Backend :

```powershell
composer install
```

Frontend :

```powershell
npm install
```

## 11. Installation rapide

Une fois MySQL démarré et la base créée :

```powershell
# Backend
cd C:\xampp\htdocs\Reservy\reservy-backend
composer install
Copy-Item .env.example .env
php artisan key:generate
# Configurer DB_* dans .env avant la commande suivante
php artisan migrate --seed
php artisan serve --host=127.0.0.1 --port=8000
```

Dans un deuxième terminal :

```powershell
# Frontend
cd C:\xampp\htdocs\Reservy\reservy-frontend
npm install
# Créer .env avec VITE_API_URL=http://127.0.0.1:8000/api
npm run dev
```

## 12. Préparation pour la production

Avant un déploiement :

- Mettre `APP_ENV=production`.
- Mettre `APP_DEBUG=false`.
- Utiliser une base de données dédiée.
- Configurer HTTPS.
- Configurer le mailer et le stockage d'images.
- Ne pas exposer les fichiers `.env`.
- Exécuter les migrations avec précaution.
- Construire le frontend avec `npm run build`.
- Configurer le serveur web pour servir le dossier `dist` du frontend et le dossier `public` du backend selon l'architecture de déploiement.
