# 🛒 Supermarché Mohamed — Application Web MVC

Application de gestion de commandes en ligne développée en PHP pur selon le patron de conception **MVC (Modèle-Vue-Contrôleur)**.

---

## 📋 Fonctionnalités

- Parcourir les familles de produits (catégories)
- Sélectionner et ajouter des produits au panier
- Gérer le panier : ajouter, supprimer, vider
- Valider une commande et obtenir une confirmation
- Consulter la liste des adhérents
- Interface d'administration des tables BDD

---

## 🗂️ Structure du projet

```
Supermarche/
│
├── index.php                  ← Front Controller (point d'entrée unique)
├── style.css                  ← Feuille de style principale
├── Fond.jpg                   ← Image de fond
│
├── core/
│   ├── Database.php           ← Singleton PDO (connexion BDD)
│   └── Flash.php              ← Gestion des messages flash (session)
│
├── models/
│   ├── AdherentModel.php      ← Données de la table `adherent`
│   ├── CommandeModel.php      ← Création des commandes en BDD
│   ├── FamilleModel.php       ← Données de la table `famille`
│   ├── PanierModel.php        ← Panier en session (pas de BDD)
│   └── ProduitModel.php       ← Données de la table `produit`
│
├── controllers/
│   └── MainController.php     ← Dispatch GET/POST, appel Models + Views
│
└── views/
    ├── layout.php             ← Template HTML commun (navbar, flash, container)
    ├── accueil.php            ← Page d'accueil
    ├── famille.php            ← Choix de la catégorie
    ├── produit.php            ← Choix du produit
    ├── panier.php             ← Panier d'achat
    ├── confirmation.php       ← Confirmation de commande
    ├── compte.php             ← Liste des adhérents
    └── gestion.php            ← Administration BDD
```

---

## ⚙️ Installation (XAMPP)

### 1. Copier le projet
Placer le dossier dans :
```
C:\xampp\htdocs\Supermarche\
```

### 2. Démarrer XAMPP
Dans le **XAMPP Control Panel**, démarrer :
- ✅ **Apache**
- ✅ **MySQL**

### 3. Créer la base de données
Ouvrir **phpMyAdmin** : [http://localhost/phpmyadmin](http://localhost/phpmyadmin)

Créer une base nommée `supermarche` avec le collation **`utf8mb4_unicode_ci`**, puis créer les tables suivantes :

```sql
CREATE DATABASE supermarche CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

USE supermarche;

CREATE TABLE famille (
    id_famille INT AUTO_INCREMENT PRIMARY KEY,
    nomdefamille VARCHAR(100) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE produit (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(150) NOT NULL,
    prix DECIMAL(8,2) DEFAULT NULL,
    famille_id INT NOT NULL,
    FOREIGN KEY (famille_id) REFERENCES famille(id_famille)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE adherent (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nom VARCHAR(100) NOT NULL,
    email VARCHAR(150) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE commande (
    id INT AUTO_INCREMENT PRIMARY KEY,
    adherent_id INT DEFAULT NULL,
    date_commande DATETIME NOT NULL,
    total DECIMAL(10,2) DEFAULT 0
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### 4. Configurer la connexion BDD
Ouvrir `core/Database.php` et vérifier les identifiants :
```php
'mysql:host=localhost;dbname=supermarche;charset=utf8mb4',
'root',   // utilisateur MySQL
'',       // mot de passe (vide par défaut sous XAMPP)
```

### 5. Accéder à l'application
```
http://localhost/Supermarche/index.php
```

---

## 🔗 Pages disponibles

| URL | Description |
|-----|-------------|
| `index.php` | Accueil |
| `index.php?page=famille` | Choisir une catégorie |
| `index.php?page=produit&famille_id=1` | Produits d'une catégorie |
| `index.php?page=panier` | Mon panier |
| `index.php?page=confirmation&id=X` | Confirmation commande |
| `index.php?page=compte` | Liste des adhérents |
| `index.php?page=gestion` | Administration BDD |

---

## 🏗️ Architecture MVC

```
Navigateur
    │
    ▼
index.php  (Front Controller)
    │  valide la page, instancie le contrôleur
    ▼
MainController::dispatch()
    │  lit $_GET / $_POST
    ├──► Model  (requêtes SQL via PDO)
    │       └──► Database (Singleton PDO)
    └──► View   (HTML injecté dans layout.php)
```

**Cycle d'une requête :**
1. `index.php` reçoit `?page=produit`
2. `MainController` appelle `ProduitModel::getByFamille()`
3. Le modèle interroge MySQL via `Database::getInstance()`
4. Le contrôleur injecte les données dans `views/produit.php`
5. `layout.php` encapsule le tout et renvoie le HTML final

---

## 🛡️ Sécurité

- **Whitelist des pages** : seules les pages autorisées sont accessibles
- **Requêtes préparées PDO** : protection contre les injections SQL
- **`htmlspecialchars()`** : protection XSS sur toutes les sorties
- **Validation des types flash** : le type CSS des alertes est validé côté serveur
- **`EXTR_SKIP`** sur `extract()` : évite l'écrasement de variables

---

## 🐛 Problèmes fréquents

| Symptôme | Cause | Solution |
|----------|-------|----------|
| `?` à la place des accents | Collation BDD non UTF-8 | Passer les tables en `utf8mb4_unicode_ci` dans phpMyAdmin |
| CSS non chargé / navbar non stylée | Chemin relatif incorrect | Vérifier l'URL : `http://localhost/Supermarche/index.php` |
| `Erreur BDD : ...` | MySQL éteint ou mauvais identifiants | Démarrer MySQL dans XAMPP, vérifier `Database.php` |
| Page blanche | Erreur PHP silencieuse | Activer `display_errors` dans `php.ini` |

---

## 🧰 Technologies utilisées

- **PHP 8.x** — logique serveur et MVC
- **MySQL / MariaDB** — base de données relationnelle
- **PDO** — accès sécurisé à la base de données
- **HTML5 / CSS3** — interface utilisateur responsive
- **XAMPP** — serveur de développement local

---

## 👨‍💻 Auteur

Projet réalisé dans le cadre d'un apprentissage du développement web PHP MVC.
