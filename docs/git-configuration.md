# Configuration du système de contrôle de version (Git)

## 1. Introduction

### 1.1 Objectif
Ce document détaille la stratégie et les conventions Git adoptées pour le développement de l'application de mise en relation entre professionnels de ménage et hébergeurs. Une utilisation cohérente de Git permettra de :
- Maintenir un historique clair et structuré du développement
- Faciliter la collaboration entre les membres de l'équipe
- Assurer la traçabilité des fonctionnalités et des corrections
- Simplifier le déploiement et les mises à jour de l'application

### 1.2 Importance pour le projet
Notre application implique plusieurs composants (frontend mobile, backend, API, intégrations tierces) qui seront développés en parallèle selon les phases définies dans la feuille de route. Une stratégie Git bien définie est essentielle pour coordonner ces développements et maintenir la cohérence du code.

## 2. Structure du repository

### 2.1 Organisation générale
Le projet sera organisé en deux repositories distincts :
- `menage-connect-mobile` : Application mobile React Native avec Expo
- `menage-connect-api` : Backend Node.js (Express/NestJS) et API

### 2.2 Structure des dossiers
Pour chaque repository, nous adopterons une structure claire :

#### 2.2.1 Repository Frontend (menage-connect-mobile)
```
/
├── assets/            # Images, fonts et autres ressources statiques
├── src/
│   ├── components/    # Composants React réutilisables
│   ├── screens/       # Écrans de l'application
│   │   ├── host/      # Écrans spécifiques aux hébergeurs
│   │   ├── cleaner/   # Écrans spécifiques aux professionnels de ménage
│   │   └── common/    # Écrans communs (authentification, etc.)
│   ├── navigation/    # Configuration de la navigation
│   ├── services/      # Services (API, authentification, etc.)
│   ├── store/         # État global (Redux ou Context)
│   ├── utils/         # Fonctions utilitaires
│   └── constants/     # Constantes et configuration
├── docs/              # Documentation du projet
├── tests/             # Tests unitaires et d'intégration
└── [fichiers de configuration] # package.json, babel.config.js, etc.
```

#### 2.2.2 Repository Backend (menage-connect-api)
```
/
├── src/
│   ├── controllers/   # Contrôleurs pour les endpoints API
│   ├── models/        # Modèles de données
│   ├── services/      # Logique métier
│   ├── routes/        # Définition des routes API
│   ├── middlewares/   # Middlewares Express/NestJS
│   ├── utils/         # Fonctions utilitaires
│   └── config/        # Configuration
├── docs/              # Documentation du projet et de l'API
├── tests/             # Tests unitaires et d'intégration
└── [fichiers de configuration] # package.json, tsconfig.json, etc.
```

### 2.3 Fichiers de configuration et documentation
Chaque repository contiendra :
- `README.md` : Instructions d'installation et d'utilisation
- `.gitignore` : Liste des fichiers/dossiers à ignorer
- `.env.example` : Exemple de variables d'environnement (sans données sensibles)
- `docs/` : Documentation technique et fonctionnelle

## 3. Stratégie de branching

### 3.1 Modèle de flux de travail
Nous adopterons une version adaptée du modèle GitFlow, optimisée pour notre cycle de développement incrémental par phases.

### 3.2 Branches principales
- `main` : Code en production, stable et déployé
- `develop` : Branche d'intégration pour le développement en cours
- `release/*` : Préparation des releases (ex: `release/v1.0.0`)
- `feature/*` : Développement de nouvelles fonctionnalités
- `hotfix/*` : Corrections urgentes en production
- `bugfix/*` : Corrections de bugs non urgents

### 3.3 Protection des branches
Les branches `main` et `develop` seront protégées :
- Interdiction de push direct
- Validation obligatoire des pull requests par au moins un reviewer
- CI doit passer avec succès avant merge

## 4. Conventions de nommage

### 4.1 Branches de fonctionnalités
Format : `feature/[phase-number]-[feature-name]`

Exemples :
- `feature/3-dashboard-navigation`
- `feature/4-create-listing-form`

### 4.2 Branches de correction
Format pour bugs non urgents : `bugfix/[issue-number]-[short-description]`
Format pour hotfixes : `hotfix/[issue-number]-[short-description]`

Exemples :
- `bugfix/42-fix-profile-image-upload`
- `hotfix/57-fix-payment-processing`

### 4.3 Messages de commit
Format : `[type]: [description concise] [#issue-number]`

Types :
- `feat` : Nouvelle fonctionnalité
- `fix` : Correction de bug
- `docs` : Documentation
- `style` : Formatting, missing semi colons, etc.
- `refactor` : Refactorisation de code
- `test` : Ajout ou modification de tests
- `chore` : Maintenance, mise à jour de dépendances, etc.

Exemples :
- `feat: ajouter formulaire de création d'annonce #23`
- `fix: corriger calcul du prix total des missions #45`
- `docs: mettre à jour la documentation de l'API de paiement`

### 4.4 Tags et versions
Format : `v[major].[minor].[patch]`

Exemples :
- `v0.1.0` : Version initiale avec authentification
- `v0.2.0` : Ajout des tableaux de bord
- `v1.0.0` : Version de production complète

## 5. Workflow de développement

### 5.1 Développement d'une nouvelle fonctionnalité
1. Créer une branche depuis `develop` : `git checkout -b feature/x-feature-name develop`
2. Développer la fonctionnalité avec des commits réguliers
3. Pousser la branche sur le repository distant : `git push origin feature/x-feature-name`
4. Créer une Pull Request vers `develop`
5. Après review et validation, merger dans `develop`

### 5.2 Correction de bugs
1. Créer une branche depuis `develop` : `git checkout -b bugfix/issue-number-description develop`
2. Corriger le bug avec des commits explicatifs
3. Pousser la branche sur le repository distant
4. Créer une Pull Request vers `develop`
5. Après review et validation, merger dans `develop`

### 5.3 Hotfixes (corrections urgentes en production)
1. Créer une branche depuis `main` : `git checkout -b hotfix/issue-number-description main`
2. Corriger le bug
3. Créer une Pull Request vers `main` ET `develop`
4. Après review, merger dans les deux branches
5. Créer un tag pour la nouvelle version

### 5.4 Préparation d'une release
1. Créer une branche release depuis `develop` : `git checkout -b release/vX.Y.Z develop`
2. Effectuer les derniers ajustements, tests et corrections
3. Mettre à jour les numéros de version et la documentation
4. Créer une Pull Request vers `main` ET `develop`
5. Après validation, merger dans les deux branches
6. Créer un tag pour la nouvelle version

## 6. Code review et Pull Requests

### 6.1 Contenu d'une Pull Request
Chaque Pull Request doit inclure :
- Un titre clair décrivant la fonctionnalité ou la correction
- Une description détaillée des changements apportés
- Références aux issues concernées
- Screenshots/captures d'écran pour les changements d'UI/UX

### 6.2 Critères de validation
Une Pull Request est prête à être mergée quand :
- Tous les reviewers ont approuvé les changements
- Les tests automatisés passent avec succès
- Le code respecte les standards de qualité définis
- La documentation a été mise à jour si nécessaire

### 6.3 Checklist de review
- Le code est-il lisible et bien structuré ?
- Les noms de variables et fonctions sont-ils explicites ?
- La logique métier est-elle correcte ?
- Les tests couvrent-ils suffisamment le code ?
- Y a-t-il des problèmes de sécurité potentiels ?
- Le code est-il optimisé pour les performances ?

## 7. Intégration avec CI/CD

### 7.1 Hooks Git
Des hooks pre-commit seront configurés pour :
- Linter (ESLint) : vérification du style de code
- Prettier : formatage automatique
- Tests unitaires : exécution des tests essentiels

### 7.2 Pipeline CI/CD
Nous utiliserons GitHub Actions pour :
- Exécuter les tests à chaque push
- Vérifier la qualité du code
- Générer des builds de test pour chaque PR
- Déployer automatiquement sur les environnements de test/staging
- Préparer les releases pour production

### 7.3 Environnements
- `development` : Déploiement automatique depuis `develop`
- `staging` : Déploiement automatique depuis les branches `release/*`
- `production` : Déploiement manuel validé depuis `main`

## 8. Gestion des dépendances

### 8.1 Stratégie pour .gitignore
Les fichiers suivants seront ignorés :
- Dossiers `node_modules/`
- Fichiers de build (`build/`, `dist/`, `.expo/`)
- Fichiers d'environnement (`.env`, `.env.local`, etc.)
- Fichiers de logs
- Fichiers temporaires et caches
- Fichiers spécifiques à l'IDE/éditeur

### 8.2 Gestion des packages
- Les versions des dépendances seront fixées (exact versions)
- Package-lock.json ou yarn.lock seront versionnés
- Les mises à jour des dépendances seront effectuées via des branches dédiées

## 9. Commandes Git essentielles pour le projet

### 9.1 Initialisation du repository
```bash
# Cloner le repository
git clone [url-du-repository]

# Configurer les informations utilisateur
git config user.name "Prénom Nom"
git config user.email "email@example.com"
```

### 9.2 Commandes quotidiennes
```bash
# Mettre à jour sa copie locale
git pull

# Créer une branche de fonctionnalité
git checkout -b feature/x-feature-name develop

# Ajouter des modifications
git add .

# Créer un commit
git commit -m "feat: description de la fonctionnalité #issue-number"

# Pousser les changements
git push origin feature/x-feature-name
```

### 9.3 Synchronisation avec develop
```bash
# Mettre à jour la branche develop locale
git checkout develop
git pull

# Rebaser sa branche de fonctionnalité
git checkout feature/x-feature-name
git rebase develop
```

## 10. Premiers pas

Pour initier le processus Git pour ce projet, voici les étapes à suivre :

1. Créer les repositories sur GitHub/GitLab
2. Configurer les branches principales
3. Configurer les protections de branches
4. Mettre en place les templates de PR et d'issues
5. Configurer les intégrations CI/CD
6. Inviter les membres de l'équipe et définir les droits d'accès

Ces étapes seront réalisées comme première tâche de la Phase 1 du plan d'implémentation.