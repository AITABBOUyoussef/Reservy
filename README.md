# Reservy

Plateforme web de reservation pour cafes et restaurants. Reservy met en relation les clients, les gerants et les administrateurs dans une application React qui consomme une API Laravel.

## Demo en ligne

Visitez la version de demonstration :

**[https://reservy.me](https://reservy.me)**

## Apercu de l'application

### Accueil et etablissements

![Accueil Reservy](<photos/Capture d'écran 2026-09-27 221257.png>)

![Liste des etablissements](<photos/Capture d'écran 2026-09-27 221247.png>)

### Details, galerie et menu

![Details d'un etablissement](<photos/Capture d'écran 2026-09-27 221238.png>)

![Galerie d'images](<photos/Capture d'écran 2026-09-27 221223.png>)

### Espaces de gestion

![Dashboard gerant](<photos/Capture d'écran 2026-09-27 221214.png>)

![Dashboard administrateur](<photos/Capture d'écran 2026-09-27 221157.png>)

![Gestion des reservations](<photos/Capture d'écran 2026-09-27 221136.png>)

## Vue d'ensemble

```text
Reservy/
├── reservy-frontend/    # React + Vite + Tailwind
├── reservy-backend/     # Laravel API + Sanctum
└── README.md            # Documentation generale
```

### Parcours utilisateur

1. Un visiteur consulte les etablissements publics disposant d'au moins un produit.
2. Il ouvre la fiche d'un etablissement, sa galerie et son menu.
3. Il se connecte puis reserve une table.
4. Il peut ajouter des produits a sa reservation.
5. Le gerant gere son etablissement, ses tables, son menu, ses images et les reservations.
6. L'administrateur valide les etablissements soumis et supervise la plateforme.

## Fonctionnalites

### Client

- Inscription et connexion classique.
- Connexion Google configurable par variable d'environnement.
- Mot de passe oublie et reinitialisation.
- Recherche et consultation des etablissements acceptes.
- Affichage de toutes les images et du menu.
- Reservation d'une table.
- Ajout de produits et options a une reservation.
- Consultation des commandes, reservations et profil.

### Gerant

- Creation et modification de son etablissement.
- Gestion des images et de l'image principale.
- Gestion des tables et capacites.
- Gestion des categories, produits, images et options.
- Consultation et traitement des reservations.

### Administrateur

- Retour direct vers son tableau de bord.
- Consultation des etablissements en attente et de la liste globale.
- Acceptation et suppression des etablissements.
- Acces aux fonctions de gestion necessaires a l'administration.

## Stack

| Couche | Technologies |
|---|---|
| Frontend | React 19, Vite, React Router, Axios, Tailwind CSS, Lucide React |
| Backend | PHP 8.2+, Laravel 12, Eloquent |
| Authentification | Laravel Sanctum, Spatie Permission, Google Socialite |
| Donnees | SQLite par defaut ou MySQL |
| Images | Cloudinary Laravel ou filesystem configure |

## Installation locale complete

### Prerequis

- PHP 8.2+
- Composer 2
- Node.js 18+
- npm
- SQLite ou MySQL

### Backend

```powershell
Set-Location .\reservy-backend
composer install
Copy-Item .env.example .env
php artisan key:generate
New-Item -ItemType File -Path database\database.sqlite -Force
php artisan migrate --seed
php artisan serve
```

Si vous utilisez MySQL, renseignez les variables `DB_*` dans `reservy-backend\.env` avant les migrations.

### Frontend

Dans un autre terminal :

```powershell
Set-Location .\reservy-frontend
npm install
Copy-Item .env.example .env
npm run dev
```

Configurez `reservy-frontend\.env` :

```env
VITE_API_URL=http://127.0.0.1:8000/api
VITE_GOOGLE_CLIENT_ID=
```

URLs locales par defaut :

- Frontend : `http://localhost:5173`
- API : `http://127.0.0.1:8000/api`

## Configuration Google Login

Le Client ID Google est public et peut etre expose dans le bundle frontend. Les secrets restent uniquement dans le `.env` du backend.

1. Creez un OAuth Client de type Web dans Google Cloud Console.
2. Ajoutez `http://localhost:5173` et `http://127.0.0.1:5173` aux origines autorisees.
3. Configurez `VITE_GOOGLE_CLIENT_ID` dans le frontend.
4. Configurez `GOOGLE_CLIENT_ID` et `GOOGLE_CLIENT_SECRET` dans le backend.
5. Redemarrez Vite et nettoyez la configuration Laravel si necessaire :

```powershell
Set-Location .\reservy-backend
php artisan config:clear
php artisan cache:clear
```

Si le Client ID frontend est absent, Google Login est desactive sans empecher la connexion classique.

## Verification avant livraison

```powershell
Set-Location .\reservy-frontend
npm run lint
npm run build

Set-Location ..\reservy-backend
php artisan test
```

## Securite et bonnes pratiques

- Ne commitez jamais `.env` ou des secrets.
- Utilisez les middlewares de role du backend comme source de verite.
- Le frontend protege les pages pour l'UX, mais ne remplace pas l'autorisation Laravel.
- Configurez `FRONTEND_URL` avec l'URL publique du frontend pour les liens de reset password.
- En production, remplacez les URLs localhost par les URLs publiques et activez HTTPS.

## Documentation detaillee

- [Documentation Frontend](reservy-frontend/README.md)
- [Documentation Backend](reservy-backend/README.md)
- [Routes API](reservy-backend/routes/api.php)
- [Exemple d'environnement frontend](reservy-frontend/.env.example)
- [Exemple d'environnement backend](reservy-backend/.env.example)

## Licence

Projet de demonstration et de formation. Adaptez la licence selon les besoins du depot avant publication.
