# Documentation complete du projet Reservy

## 1. Presentation generale

Reservy est une plateforme web de reservation pour cafes et restaurants. Elle permet aux clients de decouvrir des etablissements, consulter leurs informations et leurs menus, reserver une table et ajouter des produits a leur reservation.

La plateforme comprend trois types d'utilisateurs :

- **Client** : consulte les etablissements, reserve une table et passe une pre-commande.
- **Gerant** : gere son etablissement, ses tables, son menu, ses images et ses reservations.
- **Administrateur** : controle les etablissements inscrits et supervise la plateforme.

Le projet est divise en deux applications :

```text
Reservy/
├── reservy-frontend/    # Application React/Vite
├── reservy-backend/     # API Laravel
├── README.md            # Documentation rapide
└── DOCUMENTATION_PROJET.md
```

Le frontend communique avec le backend au moyen d'une API REST JSON.

---

## 2. Objectif fonctionnel

Reservy repond a plusieurs problemes :

1. Les clients ont besoin de trouver rapidement un cafe ou un restaurant.
2. Ils doivent pouvoir consulter les photos, les tables disponibles et le menu avant de se deplacer.
3. Les gerants ont besoin d'un espace unique pour administrer leur etablissement.
4. L'administrateur doit pouvoir verifier les etablissements avant leur publication.
5. Les produits peuvent etre ajoutes a une reservation afin de preparer la commande a l'avance.

Le parcours general est le suivant :

```text
Visiteur
   │
   ├── Consulte les etablissements publics
   ├── Ouvre une fiche et consulte la galerie et le menu
   └── Se connecte ou cree un compte
          │
          └── Client
                ├── Choisit une date, une heure et une table
                ├── Ajoute éventuellement des produits
                └── Suit l'etat de sa reservation

Gerant
   ├── Cree son etablissement
   ├── Gere les tables et le menu
   └── Accepte ou traite les reservations

Administrateur
   ├── Consulte les demandes
   ├── Accepte ou refuse les etablissements
   └── Administre la plateforme
```

---

## 3. Technologies utilisees

### Frontend

- **React 19** : construction de l'interface utilisateur.
- **Vite** : serveur de developpement et build de production.
- **React Router** : navigation entre les pages.
- **Axios** : communication avec l'API Laravel.
- **Tailwind CSS** : styles responsive et composants visuels.
- **Lucide React** : icones.
- **React OAuth Google** : connexion Google.

### Backend

- **PHP 8.2+**.
- **Laravel 12** : framework principal de l'API.
- **Eloquent ORM** : communication avec la base de donnees.
- **Laravel Sanctum** : tokens d'authentification.
- **Spatie Laravel Permission** : roles et permissions.
- **Laravel Socialite** : integration Google.
- **Cloudinary Laravel** : gestion des images selon la configuration.
- **PHPUnit** : tests automatises.

### Base de donnees

Le projet peut fonctionner avec :

- SQLite pour le developpement local.
- MySQL pour un environnement partage ou de production.

---

## 4. Architecture technique

### 4.1 Communication entre les applications

```text
Navigateur
    │
    │ React + Axios + token Bearer
    ▼
reservy-frontend
    │
    │ Requetes HTTP JSON vers VITE_API_URL
    ▼
reservy-backend
    │
    ├── Middleware auth:sanctum
    ├── Middleware role
    ├── Controllers
    ├── Form Requests
    ├── Services
    ├── Models Eloquent
    └── Base de donnees / stockage images
```

### 4.2 Responsabilite du frontend

Le frontend est responsable de :

- l'affichage des pages ;
- la navigation ;
- la validation visuelle des formulaires ;
- l'envoi des requetes HTTP ;
- le stockage local du token et des informations utilisateur ;
- la protection des pages selon le role ;
- l'affichage des messages d'erreur et de succes ;
- l'adaptation de l'interface aux mobiles et aux ordinateurs.

Le frontend ne doit pas etre considere comme la seule couche de securite. Un utilisateur peut envoyer une requete manuellement ; les controles importants doivent donc rester dans le backend.

### 4.3 Responsabilite du backend

Le backend est responsable de :

- l'authentification ;
- l'autorisation ;
- la validation des donnees ;
- la logique metier ;
- les controles de propriete ;
- la gestion des reservations ;
- la gestion des produits et des images ;
- l'acces a la base de donnees ;
- les reponses JSON de l'API.

---

## 5. Organisation du frontend

```text
reservy-frontend/
├── public/                  # Fichiers publics
├── src/
│   ├── api/
│   │   └── axios.js         # Instance Axios et URL de l'API
│   ├── assets/              # Images et ressources
│   ├── components/          # Composants reutilisables
│   │   ├── AuthShell.jsx
│   │   ├── Navbar.jsx
│   │   └── ProtectedRoute.jsx
│   ├── pages/               # Pages de l'application
│   ├── App.jsx              # Routes React
│   ├── main.jsx             # Point d'entree
│   └── index.css            # Styles globaux
├── .env.example             # Variables frontend documentees
└── package.json
```

### 5.1 Pages d'authentification

- `Login.jsx` : connexion classique et Google.
- `Register.jsx` : creation d'un compte et inscription Google.
- `ForgotPassword.jsx` : demande de lien de reinitialisation.
- `ResetPassword.jsx` : changement du mot de passe.
- `AuthShell.jsx` : structure visuelle partagee par les pages d'authentification.

Si `VITE_GOOGLE_CLIENT_ID` est absent, les boutons Google sont desactives et un message clair est affiche. La connexion classique reste disponible.

### 5.2 Protection des pages

`ProtectedRoute.jsx` verifie :

1. la presence du token ;
2. le role stocke localement ;
3. la liste des roles autorises pour la route.

Un visiteur non connecte est redirige vers `/login`. Un utilisateur ayant un role insuffisant est redirige vers l'accueil.

Cette protection est une protection d'interface. Le backend verifie de nouveau le token, le role et la propriete de la ressource.

### 5.3 Interfaces principales

#### Espace client

- accueil et liste des etablissements ;
- details d'un etablissement ;
- galerie d'images ;
- menu et produits ;
- reservation ;
- commandes ;
- profil.

#### Espace gerant

- tableau de bord gerant ;
- informations de l'etablissement ;
- images ;
- tables ;
- categories ;
- produits ;
- images et options des produits ;
- gestion des reservations.

#### Espace administrateur

- tableau de bord admin ;
- liste globale des etablissements ;
- etablissements en attente ;
- validation ou suppression d'un etablissement.

---

## 6. Organisation du backend

```text
reservy-backend/
├── app/
│   ├── Http/Controllers/      # Controleurs des endpoints
│   ├── Models/                # Modeles Eloquent
│   ├── Requests/              # Validation des formulaires
│   └── Services/              # Logique metier
├── database/
│   ├── migrations/            # Structure des tables
│   └── seeders/               # Roles et donnees initiales
├── resources/lang/en/         # Messages de validation en francais
├── routes/api.php             # Routes REST
├── storage/                   # Logs et fichiers locaux
├── tests/                     # Tests PHPUnit
├── .env.example               # Configuration modele
├── artisan
└── composer.json
```

### 6.1 Controllers

Les controllers recoivent les requetes HTTP, appellent les Form Requests et deleguent la logique aux services ou aux modeles.

Exemples de domaines couverts :

- authentification ;
- etablissements ;
- images ;
- produits ;
- options ;
- categories ;
- tables ;
- reservations ;
- commandes ;
- profils.

### 6.2 Form Requests

Les Form Requests centralisent :

- les regles de validation ;
- les messages d'erreur en francais ;
- les formats attendus par l'API.

Cela evite de dupliquer les validations dans les controllers et garantit des messages coherents.

### 6.3 Services

Les services regroupent la logique metier plus complexe, notamment :

- creation et modification des etablissements ;
- filtrage des etablissements publics ;
- gestion des images ;
- authentification ;
- verification de propriete ;
- produits et commandes.

---

## 7. Roles et autorisations

### Client

Le client peut :

- consulter les etablissements publics ;
- consulter les produits ;
- creer une reservation ;
- ajouter des produits a une reservation ;
- consulter ses donnees ;
- modifier son profil.

### Gerant

Le gerant peut :

- creer un etablissement ;
- modifier son propre etablissement ;
- gerer ses tables ;
- gerer ses categories ;
- gerer ses produits ;
- gerer les images et options ;
- traiter les reservations de son etablissement.

Un gerant ne doit pas pouvoir modifier l'etablissement d'un autre gerant. Le backend verifie `gerant_id` avant les actions sensibles.

### Administrateur

L'administrateur peut :

- voir les etablissements en attente ;
- voir la liste globale ;
- accepter un etablissement ;
- supprimer un etablissement ;
- acceder aux fonctions d'administration prevues.

### Regle de securite importante

Le frontend masque les liens non autorises, mais le backend reste la source de verite :

```text
Token valide
    │
    ▼
auth:sanctum
    │
    ▼
role:admin ou role:gerant
    │
    ▼
Verification de propriete
    │
    ▼
Action autorisee
```

---

## 8. Modele fonctionnel des donnees

Les principales entites sont :

| Entite | Role |
|---|---|
| `User` | Utilisateur de la plateforme |
| `Role` | Role client, gerant ou admin |
| `Etablissement` | Cafe ou restaurant |
| `EtablissementImage` | Image d'un etablissement |
| `TableResto` | Table et capacite |
| `Categorie` | Categorie du menu |
| `Produit` | Produit vendu |
| `ProduitImage` | Image d'un produit |
| `ProduitOption` | Supplement ou option d'un produit |
| `Reservation` | Reservation d'un client |
| `CommandeItem` | Produit ajoute a une reservation |
| `Review` | Note et commentaire d'un client |

### Relations principales

```text
User 1 ──── N Etablissement
User 1 ──── N Reservation
Etablissement 1 ──── N Images
Etablissement 1 ──── N Tables
Etablissement 1 ──── N Categories
Etablissement 1 ──── N Produits
Etablissement 1 ──── N Reservations
Categorie 1 ──── N Produits
Produit 1 ──── N Images
Produit 1 ──── N Options
Reservation 1 ──── N CommandeItems
Table 1 ──── N Reservations
```

### Statut d'un etablissement

```text
en_attente
acceptee
refusee
```

Seuls les etablissements `acceptee` et ayant au moins un produit sont affiches dans la liste publique.

### Statut d'une reservation

```text
en_attente
acceptee
refusee
terminee
```

### Statut d'un paiement

```text
en_attente
paye_en_ligne
paye_sur_place
```

---

## 9. Fonctionnement d'une reservation

1. Le client ouvre la fiche d'un etablissement.
2. Le frontend charge les informations, les images, les tables et les produits.
3. Le client choisit une date, une heure, une table et un nombre de personnes.
4. Le frontend envoie la reservation au backend avec le token.
5. Le backend verifie :
   - que l'utilisateur est authentifie ;
   - que l'etablissement existe ;
   - que la table appartient a l'etablissement ;
   - que la table est disponible ;
   - que les donnees sont valides.
6. La reservation est creee avec le statut `en_attente`.
7. Les produits choisis sont enregistres dans `commande_items`.
8. Le gerant consulte la demande et met a jour son statut.
9. Le client retrouve le nouveau statut dans son espace.

Le prix unitaire d'un produit doit etre conserve dans la ligne de commande afin de garder l'historique du prix utilise lors de la reservation.

---

## 10. API principale

Toutes les routes sont prefixees par :

```text
/api
```

### Routes publiques

| Methode | Endpoint | Fonction |
|---|---|---|
| `POST` | `/login` | Connexion classique |
| `POST` | `/Register` | Inscription |
| `POST` | `/auth/google` | Connexion Google |
| `POST` | `/forgot-password` | Demande de reinitialisation |
| `POST` | `/reset-password` | Reinitialisation |
| `GET` | `/GetEtablissement` | Liste publique |
| `POST` | `/GetEtablissementDet` | Details d'un etablissement |

### Routes authentifiees

| Methode | Endpoint | Fonction |
|---|---|---|
| `POST` | `/logout` | Deconnexion |
| `POST` | `/editProfil` | Modifier le profil |
| `POST` | `/destroy` | Supprimer le compte |
| `GET/POST/PUT/PATCH/DELETE` | `/reservations` | Gerer les reservations |
| `POST` | `/commande-items` | Ajouter une ligne de commande |
| `GET` | `/Mescommandes` | Consulter ses commandes |
| `POST` | `/CreeEtablissement` | Soumettre un etablissement |

### Routes gerant/admin

| Methode | Endpoint | Fonction |
|---|---|---|
| `POST` | `/EditEtablissement` | Modifier un etablissement |
| `POST` | `/AddImage` | Ajouter une image |
| `POST` | `/EditImage` | Modifier une image |
| `POST` | `/DaleteImage` | Supprimer une image |
| `GET` | `/MonEtablissment` | Consulter son etablissement |
| `POST` | `/AddTabl` | Ajouter une table |
| `POST` | `/EditTabl` | Modifier une table |
| `POST` | `/DaleteTabl` | Supprimer une table |
| `POST` | `/AddCategorie` | Ajouter une categorie |
| `POST` | `/DeletCategorie` | Supprimer une categorie |
| `GET` | `/GetProduits/{etablissementId}` | Lister les produits |
| `POST` | `/AddProduit` | Ajouter un produit |
| `POST` | `/EditProduit` | Modifier un produit |
| `POST` | `/DeletProduit` | Supprimer un produit |
| `POST` | `/AddProduitImage` | Ajouter une image produit |
| `POST` | `/EditProduitImage` | Definir l'image principale |
| `POST` | `/DeletProduitImage` | Supprimer une image produit |
| `POST` | `/AddProduitOption` | Ajouter une option |
| `POST` | `/DeletProduitOption` | Supprimer une option |

### Routes admin

| Methode | Endpoint | Fonction |
|---|---|---|
| `GET` | `/EtablissementAttente` | Etablissements en attente |
| `GET` | `/AllEtablissement` | Liste globale |
| `POST` | `/AcceptEtablissement` | Accepter un etablissement |
| `POST` | `/DestroyEtablissement` | Supprimer un etablissement |

La liste exacte et le comportement definitif des endpoints se trouvent dans [routes/api.php](reservy-backend/routes/api.php).

---

## 11. Authentification

### Connexion classique

Le frontend envoie l'e-mail et le mot de passe à `/api/login`. Le backend :

1. valide les donnees ;
2. recherche l'utilisateur ;
3. verifie le mot de passe ;
4. cree un token Sanctum ;
5. renvoie l'utilisateur, le token et le role.

Le frontend stocke les informations necessaires dans `localStorage` :

```text
token
user
role
```

Les appels proteges utilisent :

```http
Authorization: Bearer <token>
Accept: application/json
```

### Connexion Google

Le frontend obtient un access token Google puis l'envoie au backend. Le backend verifie le token aupres de Google avec Socialite.

Le Client ID frontend est configure avec :

```env
VITE_GOOGLE_CLIENT_ID=
```

Les secrets backend sont configures avec :

```env
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
```

Le role existant d'un utilisateur est conserve lors d'une connexion Google. Un utilisateur sans role recoit le role `client`.

### Reinitialisation du mot de passe

1. Le client saisit son e-mail.
2. Le backend genere un token temporaire.
3. Un lien contenant le token et l'e-mail est envoye.
4. Le lien utilise `FRONTEND_URL`.
5. Le frontend affiche la page `ResetPassword`.
6. Le backend verifie le token puis met a jour le mot de passe.

---

## 12. Configuration locale

### Backend

Dans `reservy-backend/.env` :

```env
APP_NAME=Reservy
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost
FRONTEND_URL=http://localhost:5173

DB_CONNECTION=sqlite
DB_DATABASE=database/database.sqlite

MAIL_MAILER=log

GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=http://localhost:5173
```

### Frontend

Dans `reservy-frontend/.env` :

```env
VITE_API_URL=http://127.0.0.1:8000/api
VITE_GOOGLE_CLIENT_ID=
```

Les fichiers `.env` reels ne doivent jamais etre commits. Seuls les fichiers `.env.example` doivent etre partages.

---

## 13. Installation et lancement

### Backend

```powershell
Set-Location C:\xampp\htdocs\Reservy\reservy-backend
composer install
Copy-Item .env.example .env
php artisan key:generate
New-Item -ItemType File -Path database\database.sqlite -Force
php artisan migrate --seed
php artisan serve
```

### Frontend

Dans un autre terminal :

```powershell
Set-Location C:\xampp\htdocs\Reservy\reservy-frontend
npm install
Copy-Item .env.example .env
npm run dev
```

Adresses locales :

```text
Frontend : http://localhost:5173
Backend  : http://127.0.0.1:8000
API      : http://127.0.0.1:8000/api
```

---

## 14. Tests et verification

### Frontend

```powershell
Set-Location C:\xampp\htdocs\Reservy\reservy-frontend
npm run lint
npm run build
```

`npm run lint` verifie la qualite du code JavaScript/JSX. `npm run build` verifie que l'application peut etre compilee pour la production.

### Backend

```powershell
Set-Location C:\xampp\htdocs\Reservy\reservy-backend
php artisan test
```

Les zones qui meritent des tests fonctionnels supplementaires sont :

- connexion Google ;
- conservation des roles ;
- controle d'appartenance d'un gerant ;
- filtrage des etablissements ayant des produits ;
- validation des tables dupliquees ;
- protection des routes selon les roles.

---

## 15. Deploiement

### Frontend

1. Configurer `VITE_API_URL` avec l'URL publique de l'API.
2. Configurer `VITE_GOOGLE_CLIENT_ID`.
3. Installer les dependances.
4. Executer `npm run build`.
5. Publier le contenu de `dist/` sur le serveur web.
6. Configurer le serveur pour rediriger les routes SPA vers `index.html`.

### Backend

1. Installer les dependances Composer.
2. Configurer les variables de production dans `.env`.
3. Generer une `APP_KEY` unique.
4. Configurer MySQL ou la base de production.
5. Executer les migrations.
6. Configurer le stockage des images.
7. Configurer le service mail.
8. Configurer les workers de queue si necessaire.
9. Desactiver `APP_DEBUG`.
10. Activer HTTPS.

Configuration minimale de production :

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://api.exemple.com
FRONTEND_URL=https://reservy.exemple.com
```

---

## 16. Securite

- Ne jamais partager `APP_KEY`.
- Ne jamais mettre `GOOGLE_CLIENT_SECRET` dans le frontend.
- Ne jamais commiter `.env`.
- Utiliser HTTPS en production.
- Garder les controles de role dans le backend.
- Verifier la propriete de chaque ressource avant modification ou suppression.
- Valider toutes les donnees entrantes.
- Ne pas faire confiance au role transmis uniquement par le frontend.
- Nettoyer le cache de configuration apres une modification de `.env` :

```powershell
php artisan config:clear
php artisan cache:clear
```

Si un secret a ete expose, il doit etre remplace dans le fournisseur concerne.

---

## 17. Depannage

### Le frontend ne trouve pas l'API

Verifier :

```env
VITE_API_URL=http://127.0.0.1:8000/api
```

Puis redemarrer Vite.

### Google Login ne fonctionne pas

Verifier :

- `VITE_GOOGLE_CLIENT_ID` ;
- `GOOGLE_CLIENT_ID` et `GOOGLE_CLIENT_SECRET` ;
- les origines autorisees dans Google Cloud Console ;
- l'URL utilisee par le navigateur ;
- les logs du backend.

### Les changements `.env` backend ne sont pas pris en compte

Executer :

```powershell
php artisan config:clear
php artisan cache:clear
```

### Le lien de reset est incorrect

Verifier :

```env
FRONTEND_URL=http://localhost:5173
```

La valeur doit etre l'URL du frontend, sans chemin `/api`.

### Un gerant recoit une erreur d'autorisation

Verifier que :

1. l'utilisateur possede le role `gerant` ;
2. l'etablissement lui appartient ;
3. le token n'est pas expire ou invalide ;
4. le frontend utilise le bon `VITE_API_URL`.

---

## 18. Limites actuelles et evolutions possibles

Les evolutions suivantes peuvent etre ajoutees :

- tests feature plus complets ;
- politiques Laravel dediees par ressource ;
- documentation OpenAPI/Swagger ;
- pagination des listes importantes ;
- notifications temps reel ;
- paiement en ligne ;
- gestion avancee des avis ;
- historique detaille des changements ;
- validation plus stricte de la configuration au demarrage ;
- pipeline CI pour lint, build et tests.

---

## 19. Documents associes

- [README general](README.md)
- [README frontend](reservy-frontend/README.md)
- [README backend](reservy-backend/README.md)
- [Routes API](reservy-backend/routes/api.php)
- [Variables frontend](reservy-frontend/.env.example)
- [Variables backend](reservy-backend/.env.example)

---

# 20. Explication detaillee du code

Cette partie explique le fonctionnement interne du projet et la maniere dont les fichiers collaborent. L'objectif est de comprendre le chemin complet d'une action, depuis le clic dans l'interface jusqu'a la reponse de la base de donnees.

## 20.1 Demarrage de l'application

### Demarrage frontend

Le fichier `reservy-frontend/src/main.jsx` est le point d'entree React :

```text
main.jsx
   │
   ├── Charge index.css
   ├── Lit VITE_GOOGLE_CLIENT_ID
   ├── Cree la racine React
   └── Monte App dans GoogleOAuthProvider
```

`App.jsx` contient ensuite le routeur React. Il affiche `Navbar` puis choisit la page selon l'URL :

```jsx
<Route path="/" element={<Dashboard />} />
<Route path="/login" element={<Login />} />
<Route path="/etablissment/:id" element={<Etablissment />} />
```

Lorsqu'une route est protegee, `ProtectedRoute` est place autour de la page :

```jsx
<Route element={<ProtectedRoute roles={["admin"]} />}>
    <Route path="/dashboardAdmin" element={<DashboardAdmin />} />
</Route>
```

Le composant `ProtectedRoute` :

1. lit le token dans `localStorage` ;
2. lit le role de l'utilisateur ;
3. redirige vers `/login` si le token est absent ;
4. redirige vers `/` si le role ne correspond pas ;
5. affiche la page avec `Outlet` si les controles frontend passent.

### Demarrage backend

Laravel demarre avec `public/index.php`, charge les routes et enregistre les services. Les endpoints de l'application sont declares dans `routes/api.php`.

Le prefixe `/api` est ajoute aux routes API. Ainsi :

```text
Route Laravel : /login
URL complete  : http://127.0.0.1:8000/api/login
```

Le frontend ne connait donc pas directement les controllers. Il connait uniquement les URLs de l'API.

---

## 20.2 Fonctionnement de l'instance Axios

Le fichier `reservy-frontend/src/api/axios.js` cree une instance Axios commune :

```text
axiosInstance
   ├── baseURL = VITE_API_URL
   ├── Content-Type = application/json
   ├── Accept = application/json
   └── Intercepteur de requete
```

L'intercepteur est execute avant chaque requete :

```text
Requete frontend
       │
       ▼
Lire localStorage.token
       │
       ├── Token present : ajouter Authorization Bearer
       └── Token absent  : envoyer la requete sans token
       │
       ▼
Envoyer la requete a Laravel
```

Ce mecanisme evite de repeter manuellement le header dans chaque page.

Exemple conceptuel :

```js
axiosInstance.get('/Mescommandes');
```

devient une requete HTTP equivalente a :

```http
GET /api/Mescommandes
Authorization: Bearer <token>
Accept: application/json
```

Le backend utilise ensuite `auth:sanctum` pour retrouver l'utilisateur lie au token.

---

## 20.3 Cycle complet d'une requete API

Chaque action suit globalement ce cycle :

```text
1. Action utilisateur
      │
2. Page React prepare les donnees
      │
3. axiosInstance envoie la requete
      │
4. Route Laravel identifie le controller
      │
5. Middleware verifie token et role
      │
6. Form Request valide les donnees
      │
7. Controller appelle le Service
      │
8. Service applique la logique metier
      │
9. Model Eloquent lit ou modifie la base
      │
10. Controller renvoie une reponse JSON
      │
11. React met a jour l'interface
```

### Exemple de lecture publique

Pour afficher les etablissements :

```text
Dashboard.jsx
   │
   └── GET /GetEtablissement
          │
          ▼
      api.php
          │
          ▼
      CreeEtablissementController@getEtablissement
          │
          ▼
      CreeEtablissementService@getEtablissement
          │
          ▼
      Etablissement::with(...)->whereHas('produits')
          │
          ▼
      Base de donnees
          │
          ▼
      JSON avec etablissements, images et note moyenne
```

La requete publique ne retourne que les etablissements :

- dont le statut est `acceptee` ;
- qui possedent au moins un produit ;
- avec leurs images ;
- avec la moyenne des avis lorsque des avis existent.

---

## 20.4 Flux de connexion classique

### Etape 1 : formulaire frontend

Dans `Login.jsx`, l'utilisateur saisit son e-mail et son mot de passe. La page execute :

```text
POST /api/login
{
    email: "...",
    password: "..."
}
```

### Etape 2 : route et controller

`routes/api.php` associe `/login` a `AuthController@login`.

Le controller recoit un `LoginRequest`. Le Form Request valide la structure des donnees, puis le controller appelle :

```text
AuthController@login
       │
       └── AuthService@login($request->validated())
```

### Etape 3 : service d'authentification

`AuthService@login` :

1. recherche l'utilisateur par e-mail ;
2. compare le mot de passe avec `Hash::check` ;
3. rejette la requete avec une erreur de validation si les identifiants sont faux ;
4. cree un token Sanctum ;
5. recupere le premier role ;
6. renvoie l'utilisateur, le token et le role.

### Etape 4 : reponse et redirection

`AuthController` transforme le resultat du service en JSON :

```json
{
  "success": true,
  "message": "Connexion réussie.",
  "token": "...",
  "user": {},
  "role": "client"
}
```

`Login.jsx` stocke ensuite :

```text
localStorage.token
localStorage.user
localStorage.role
```

Enfin, la page redirige :

```text
client  -> /
gerant  -> /dashboardGarant
admin   -> /dashboardAdmin
```

---

## 20.5 Flux d'inscription

```text
Register.jsx
   │
   └── POST /Register
          │
          ▼
      InscriptionRequest
          │
          ▼
      AuthController@inscription
          │
          ▼
      AuthService@inscription
          │
          ├── Cree User
          ├── Hash du mot de passe
          ├── Assigne le role client
          └── Cree un token Sanctum
          │
          ▼
      JSON user + token + role
          │
          ▼
      localStorage puis accueil
```

Tous les utilisateurs inscrits depuis le formulaire public commencent avec le role `client`.

---

## 20.6 Flux Google Login

Le projet utilise un flux avec access token :

```text
Bouton Google
   │
   ▼
Google OAuth dans le navigateur
   │
   ▼
Google renvoie access_token
   │
   ▼
Frontend POST /auth/google
   │
   ▼
Backend Socialite::userFromToken()
   │
   ├── Utilisateur existant : recuperer le compte
   └── Utilisateur absent  : creer un compte
   │
   ▼
Conserver le role existant
ou attribuer client si aucun role
   │
   ▼
Creer token Sanctum
   │
   ▼
Frontend stocke token, user et role
```

Le `VITE_GOOGLE_CLIENT_ID` est necessaire au frontend. Il est public et ne doit pas etre confondu avec `GOOGLE_CLIENT_SECRET`, qui reste uniquement dans le backend.

Si le Client ID frontend est absent :

- le provider recoit une valeur interne non fonctionnelle ;
- les boutons Google sont desactives ;
- un message francais indique que Google Login est indisponible ;
- la connexion classique continue de fonctionner.

---

## 20.7 Flux mot de passe oublie

### Demande du lien

```text
ForgotPassword.jsx
   │
   └── POST /forgot-password
          │
          ▼
      ForgotPasswordRequest
          │
          ▼
      AuthService@forgotPassword
          │
          ├── Genere un token aleatoire
          ├── Stocke le token par e-mail
          ├── Construit le lien avec FRONTEND_URL
          └── Envoie l'e-mail
```

Le lien ressemble a :

```text
https://frontend.exemple/reset-password?token=...&email=...
```

### Reinitialisation

```text
ResetPassword.jsx
   │
   └── POST /reset-password
          │
          ▼
      ResetPasswordRequest
          │
          ▼
      AuthService@resetPassword
          │
          ├── Verifie email + token
          ├── Hash le nouveau mot de passe
          ├── Met a jour User
          └── Supprime le token utilise
```

Le token est donc a usage unique apres une reinitialisation reussie.

---

## 20.8 Creation d'un etablissement

### Cote frontend

Le gerant ou le client autorise ouvre `AddEtablissment.jsx` et remplit :

```text
nom
description
adresse
ville
telephone
```

La page envoie les informations a :

```http
POST /api/CreeEtablissement
Authorization: Bearer <token>
```

### Cote backend

Le flux est :

```text
Route middleware auth:sanctum
   │
   ▼
Middleware role client|gerant|admin
   │
   ▼
CreeEtablissementController@store
   │
   ├── CreeEtablissementRequest valide les champs
   ├── Ajoute gerant_id depuis l'utilisateur connecte
   └── Appelle CreeEtablissementService
          │
          ├── Cree l'etablissement
          └── Initialise statut = en_attente
```

Un etablissement nouvellement cree n'est donc pas public immediatement.

---

## 20.9 Validation par l'administrateur

```text
Admin Dashboard
   │
   └── GET /EtablissementAttente
          │
          ▼
      Liste des demandes en_attente
          │
          └── POST /AcceptEtablissement
                 │
                 ▼
             AcceptEtablissementRequest
                 │
                 ▼
             CreeEtablissementService
                 │
                 ├── Met a jour statut
                 └── Si acceptee, attribue le role gerant
```

La validation admin fait donc deux choses importantes :

1. elle rend l'etablissement publiable selon son statut ;
2. elle transforme le compte du proprietaire en compte `gerant` lorsqu'il est accepte.

---

## 20.10 Lecture d'un etablissement et de son menu

Quand l'utilisateur ouvre `/etablissment/:id`, `Etablissment.jsx` recupere l'identifiant depuis l'URL et envoie :

```http
POST /api/GetEtablissementDet
{
    "etablissementId": 12
}
```

Le controller valide que l'identifiant existe, puis appelle le service.

Le service charge les relations Eloquent :

```text
Etablissement
├── images
├── tables
├── produits
│   ├── categorie
│   ├── produitOptions
│   └── produitImages
└── reviews.client
```

Le frontend utilise ensuite :

- l'image principale comme grande image de couverture ;
- les autres images dans la galerie ;
- les tables pour le formulaire de reservation ;
- les produits pour afficher le menu ;
- les options pour afficher les supplements ;
- les avis et la moyenne pour la notation.

Le backend charge les relations en une requete preparee avec `with(...)` afin de fournir au frontend un objet complet et coherent.

---

## 20.11 Gestion des images

### Images d'etablissement

```text
Dashboard gerant
   │
   ├── POST /AddImage
   ├── POST /EditImage
   └── POST /DaleteImage
          │
          ▼
      ImageEtablissement controller/service
          │
          ├── Valide le fichier
          ├── Verifie l'etablissement
          ├── Enregistre ou supprime l'image
          └── Definir une image principale si necessaire
```

### Images de produit

Le principe est similaire avec :

- `/AddProduitImage` ;
- `/EditProduitImage` pour definir l'image principale ;
- `/DeletProduitImage`.

La page detail charge toutes les images, puis choisit d'abord celle dont `est_principale` est vrai. Si aucune image principale n'existe, la premiere image disponible est utilisee comme solution de presentation.

---

## 20.12 Gestion des tables

Le gerant peut ajouter, modifier ou supprimer une table :

```text
AddTabl
   │
   ├── Validation du numero et de la capacite
   ├── Verification de l'etablissement
   ├── Verification d'une table identique
   └── Enregistrement
```

Le numero d'une table doit etre unique dans le meme etablissement. Le message utilisateur est :

```text
Ce numéro de table existe déjà dans cet établissement.
```

Lors d'une reservation, le backend doit rattacher la table a l'etablissement concerne afin d'eviter qu'un client reserve une table appartenant a un autre etablissement.

---

## 20.13 Creation d'une reservation et ajout des produits

### Reservation

```text
Etablissment.jsx
   │
   ├── Client choisit date, heure et table
   ├── Frontend verifie les champs visibles
   └── POST /reservations
          │
          ▼
      ReservationRequest
          │
          ▼
      ReservationController
          │
          ▼
      ReservationService
          │
          ├── Verifie le client authentifie
          ├── Verifie l'etablissement
          ├── Verifie la table
          ├── Verifie la disponibilite
          └── Cree statut en_attente
```

### Produits associes

Pour chaque produit choisi, le frontend envoie une ligne vers `/commande-items` :

```text
produit_id
reservation_id
quantite
prix_unitaire
```

Le backend verifie les donnees puis cree `CommandeItem`. La reservation et ses lignes de commande peuvent ensuite etre affichees dans l'espace client et dans l'espace gerant.

---

## 20.14 Comment les roles se completent

Les roles ne sont pas trois applications independantes. Ils utilisent les memes ressources mais avec des droits differents :

```text
Même Etablissement
   │
   ├── Visiteur : lecture publique si accepte + produits
   ├── Client   : lecture + reservation
   ├── Gerant   : gestion de son etablissement
   └── Admin    : moderation et administration
```

Exemple :

1. Le gerant cree l'etablissement.
2. L'admin accepte la demande.
3. Le client voit l'etablissement dans le dashboard.
4. Le client reserve une table.
5. Le gerant voit la reservation dans son dashboard.
6. Le gerant met a jour le statut.
7. Le client retrouve la mise a jour.

La meme base de donnees permet donc de partager l'information, tandis que les middlewares et les controles de propriete limitent les actions.

---

## 20.15 Gestion des erreurs

Les erreurs peuvent apparaitre a plusieurs niveaux :

```text
Frontend
   ├── Champs manquants
   └── Affichage du message API

Form Request
   └── Erreur 422 avec messages de validation

Middleware
   ├── 401 si token absent ou invalide
   └── 403 si role insuffisant

Service
   └── AuthorizationException si ressource non possedee

Controller
   └── Reponse JSON standardisee
```

Le frontend doit toujours afficher un message simple en francais et ne doit pas remplacer une erreur backend par un faux message de succes.

---

## 20.16 Exemple de lecture du code pour ajouter une nouvelle fonctionnalite

Pour ajouter une fonctionnalite proprement :

1. Identifier la page frontend qui declenche l'action.
2. Verifier l'instance Axios utilisee.
3. Ajouter ou identifier la route dans `routes/api.php`.
4. Ajouter un Form Request si les donnees doivent etre validees.
5. Ajouter la methode du controller.
6. Mettre la logique complexe dans un service.
7. Utiliser un model ou une relation Eloquent.
8. Retourner une reponse JSON coherente.
9. Gerer le chargement, le succes et l'erreur dans React.
10. Ajouter un controle de role et de propriete.
11. Tester lint, build et tests Laravel.

Le chemin recommande est donc :

```text
Page React
  -> axios.js
  -> routes/api.php
  -> Controller
  -> Form Request
  -> Service
  -> Model
  -> Database
  -> JSON
  -> Page React
```

---

## 20.17 Exemple concret de modification d'un produit

Supposons qu'un gerant modifie le prix d'un produit :

```text
DashboardGarant.jsx
   │
   └── POST /EditProduit
          │
          ▼
      auth:sanctum
          │
          ▼
      role:admin|gerant
          │
          ▼
      ProduitController@editProduit
          │
          ├── Valide les champs
          ├── Charge le produit
          ├── Charge son etablissement
          ├── Verifie que le gerant est proprietaire
          └── Met a jour le prix
          │
          ▼
      Reponse JSON
          │
          ▼
      Dashboard recharge les donnees
```

Si le gerant tente de modifier un produit d'un autre etablissement, le backend doit refuser l'action meme si le frontend a ete contourne.

---

## 20.18 Regles de maintenance du code

Pour garder le projet organise :

- utiliser les Form Requests pour les validations ;
- utiliser les Services pour la logique metier partagee ;
- ne pas mettre de secrets dans le code ;
- garder les messages utilisateur en francais ;
- utiliser des noms de routes coherents lors des prochaines evolutions ;
- ne pas supprimer une verification backend sous pretexte que le frontend protege deja la page ;
- garder les composants visuels reutilisables ;
- tester chaque changement frontend avec lint et build ;
- tester chaque changement backend avec PHPUnit ;
- documenter les nouvelles variables `.env` dans les deux fichiers `.env.example` et les README.

Cette separation rend le projet plus facile a comprendre, a tester et a faire evoluer.
