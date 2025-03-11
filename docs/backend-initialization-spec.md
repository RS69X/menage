# Spécifications d'initialisation du Backend Node.js

## Table des matières

1. [Introduction et objectifs](#introduction-et-objectifs)
2. [Choix technologiques](#choix-technologiques)
   - [Node.js](#nodejs)
   - [Framework : Express vs NestJS](#framework--express-vs-nestjs)
   - [TypeScript](#typescript)
3. [Architecture du projet](#architecture-du-projet)
   - [Structure de dossiers](#structure-de-dossiers)
   - [Organisation des modules](#organisation-des-modules)
4. [Dépendances du projet](#dépendances-du-projet)
   - [Dépendances principales](#dépendances-principales)
   - [Dépendances de développement](#dépendances-de-développement)
5. [Configuration du projet](#configuration-du-projet)
   - [TypeScript (tsconfig.json)](#typescript-tsconfigjson)
   - [ESLint](#eslint)
   - [Prettier](#prettier)
   - [Jest pour les tests](#jest-pour-les-tests)
   - [Variables d'environnement](#variables-denvironnement)
6. [Hooks Git et intégration continue](#hooks-git-et-intégration-continue)
   - [Hooks pre-commit](#hooks-pre-commit)
   - [Husky et lint-staged](#husky-et-lint-staged)
7. [Procédure d'initialisation](#procédure-dinitialisation)
   - [Étapes d'installation](#étapes-dinstallation)
   - [Configuration initiale](#configuration-initiale)
   - [Structure de dossiers initiale](#structure-de-dossiers-initiale)
8. [Standards de développement](#standards-de-développement)
   - [Conventions de nommage](#conventions-de-nommage)
   - [Documentation du code](#documentation-du-code)
   - [Tests](#tests)
9. [Prochaines étapes](#prochaines-étapes)

## Introduction et objectifs

Ce document définit les spécifications techniques pour l'initialisation du backend Node.js de notre application de mise en relation entre professionnels de ménage et hébergeurs. Il sert de référence pour la configuration initiale et l'architecture du projet.

### Objectifs du backend

1. **Évolutivité** : Supporter une croissance du nombre d'utilisateurs et des fonctionnalités
2. **Maintenabilité** : Structure claire et modulaire facilitant les futures modifications
3. **Sécurité** : Fondations solides pour gérer les données sensibles et les paiements
4. **Performance** : Optimisation des requêtes et de la gestion des ressources
5. **Testabilité** : Architecture facilitant les tests unitaires et d'intégration

## Choix technologiques

### Node.js

**Version** : 18.x LTS (ou supérieure)

**Justification** :
- Support à long terme (LTS)
- Performances améliorées par rapport aux versions antérieures
- Compatibilité avec la majorité des bibliothèques modernes
- Support natif des fonctionnalités ES récentes

### Framework : Express vs NestJS

Après analyse comparative, nous optons pour **NestJS** pour les raisons suivantes :

#### Avantages de NestJS
- Architecture modulaire facilitant la maintenance et l'évolutivité
- Support natif de TypeScript
- Injection de dépendances intégrée
- Documentation exhaustive et communauté active
- Structure opinionnée réduisant les décisions d'architecture
- Intégration facile avec ORM (TypeORM, Prisma)
- Outils intégrés pour la validation, l'authentification, et la documentation API

#### Inconvénients à considérer
- Courbe d'apprentissage légèrement plus élevée qu'Express
- Légèrement plus lourd à l'exécution (négligeable pour notre cas d'utilisation)

#### Comparaison avec Express
| Critère | Express | NestJS |
|---------|---------|--------|
| Flexibilité | Très haute (non opinionnée) | Modérée (structure définie) |
| Facilité d'apprentissage | Simple | Modérée |
| Productivité à long terme | Moyenne (beaucoup de décisions à prendre) | Élevée (conventions établies) |
| Support TypeScript | Via configuration | Natif |
| Architecture | Manuelle | Modulaire intégrée |
| Tests | Configuration manuelle | Intégration native |

### TypeScript

**Version** : 5.x (dernière version stable)

**Justification** :
- Typage statique réduisant les erreurs de développement
- Meilleure maintenabilité du code à long terme
- Autocomplétion et documentation intégrée dans les IDE
- Facilite la collaboration en équipe
- S'intègre parfaitement avec NestJS

## Architecture du projet

### Structure de dossiers

L'architecture suivra une organisation modulaire par domaine fonctionnel :

```
backend/
├── src/
│   ├── app.module.ts              # Module principal de l'application
│   ├── main.ts                    # Point d'entrée
│   ├── config/                    # Configuration de l'application
│   │   ├── app.config.ts
│   │   ├── database.config.ts
│   │   └── jwt.config.ts
│   ├── common/                    # Éléments partagés
│   │   ├── constants/
│   │   ├── decorators/
│   │   ├── filters/               # Filtres d'exception
│   │   ├── guards/                # Guards d'authentification
│   │   ├── interceptors/
│   │   ├── interfaces/
│   │   ├── middleware/
│   │   ├── pipes/                 # Validation des données
│   │   └── utils/
│   ├── modules/                   # Modules par domaine fonctionnel
│   │   ├── auth/                  # Module d'authentification
│   │   │   ├── auth.module.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── auth.controller.ts
│   │   │   ├── strategies/        # Stratégies d'authentification
│   │   │   ├── guards/            # Guards spécifiques à l'auth
│   │   │   ├── dto/               # Objets de transfert de données
│   │   │   └── tests/             # Tests du module
│   │   ├── users/                 # Module utilisateurs
│   │   │   ├── users.module.ts
│   │   │   ├── users.service.ts
│   │   │   ├── users.controller.ts
│   │   │   ├── entities/          # Entités de base de données
│   │   │   ├── repositories/      # Repositories personnalisés
│   │   │   ├── dto/
│   │   │   └── tests/
│   │   ├── listings/              # Module annonces
│   │   │   └── ...
│   │   ├── bookings/              # Module réservations
│   │   │   └── ...
│   │   ├── messaging/             # Module messagerie
│   │   │   └── ...
│   │   └── payments/              # Module paiements
│   │       └── ...
│   └── database/                  # Configuration de la base de données
│       ├── migrations/
│       └── seeders/
├── test/                          # Tests e2e
├── dist/                          # Code compilé
├── node_modules/
├── .env                           # Variables d'environnement (non versionné)
├── .env.example                   # Exemple de variables d'environnement
├── .eslintrc.js                   # Configuration ESLint
├── .prettierrc                    # Configuration Prettier
├── nest-cli.json                  # Configuration NestJS
├── package.json
├── tsconfig.json                  # Configuration TypeScript
├── tsconfig.build.json            # Configuration TS pour la build
├── jest.config.js                 # Configuration Jest
└── README.md                      # Documentation
```

### Organisation des modules

Chaque module suit une structure cohérente :

- **Module** (`*.module.ts`) : Définit les imports, providers et exports
- **Controller** (`*.controller.ts`) : Gère les routes HTTP
- **Service** (`*.service.ts`) : Contient la logique métier
- **Entities** (`entities/*.entity.ts`) : Définit les modèles de données
- **DTOs** (`dto/*.dto.ts`) : Définit les objets de transfert de données
- **Tests** (`tests/*.spec.ts`) : Tests unitaires et d'intégration

## Dépendances du projet

### Dépendances principales

| Dépendance | Version | Rôle |
|------------|---------|------|
| @nestjs/common | ^10.x | Fonctionnalités de base NestJS |
| @nestjs/core | ^10.x | Core framework NestJS |
| @nestjs/platform-express | ^10.x | Intégration Express |
| @nestjs/typeorm | ^10.x | Intégration TypeORM |
| @nestjs/passport | ^10.x | Authentification |
| @nestjs/jwt | ^10.x | Support JWT |
| @nestjs/config | ^3.x | Gestion de configuration |
| @nestjs/swagger | ^7.x | Documentation API |
| typeorm | ^0.3.x | ORM pour la base de données |
| passport | ^0.6.x | Authentification |
| passport-jwt | ^4.x | Stratégie JWT |
| passport-local | ^1.x | Stratégie locale |
| bcrypt | ^5.x | Hachage de mots de passe |
| class-validator | ^0.14.x | Validation |
| class-transformer | ^0.5.x | Transformation d'objets |
| helmet | ^7.x | Sécurité HTTP |
| dotenv | ^16.x | Variables d'environnement |
| reflect-metadata | ^0.1.x | Métadonnées pour décorateurs |
| rxjs | ^7.x | Programmation réactive |

### Dépendances de développement

| Dépendance | Version | Rôle |
|------------|---------|------|
| @nestjs/cli | ^10.x | CLI NestJS |
| @nestjs/schematics | ^10.x | Génération de code |
| @nestjs/testing | ^10.x | Utilitaires de test |
| @types/node | ^20.x | Types Node.js |
| @types/express | ^4.x | Types Express |
| @types/passport-jwt | ^3.x | Types Passport JWT |
| @typescript-eslint/eslint-plugin | ^6.x | Plugin ESLint pour TS |
| @typescript-eslint/parser | ^6.x | Parser ESLint pour TS |
| eslint | ^8.x | Linting du code |
| eslint-config-prettier | ^9.x | Compatibilité ESLint/Prettier |
| eslint-plugin-prettier | ^5.x | Intégration Prettier dans ESLint |
| jest | ^29.x | Framework de test |
| prettier | ^3.x | Formatage du code |
| supertest | ^6.x | Tests HTTP |
| ts-jest | ^29.x | Support Jest pour TS |
| ts-loader | ^9.x | Loader TS pour webpack |
| ts-node | ^10.x | Exécution TS sans compilation |
| typescript | ^5.x | Langage TypeScript |
| husky | ^8.x | Hooks Git |
| lint-staged | ^15.x | Linting des fichiers en pre-commit |

## Configuration du projet

### TypeScript (tsconfig.json)

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "declaration": true,
    "removeComments": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "allowSyntheticDefaultImports": true,
    "target": "ES2021",
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": "./",
    "incremental": true,
    "skipLibCheck": true,
    "strictNullChecks": true,
    "noImplicitAny": true,
    "strictBindCallApply": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "paths": {
      "@src/*": ["src/*"],
      "@common/*": ["src/common/*"],
      "@modules/*": ["src/modules/*"],
      "@config/*": ["src/config/*"]
    }
  }
}
```

### ESLint

Configuration dans `.eslintrc.js` incluant:
- TypeScript ESLint
- Prettier
- Règles de formatage strictes
- Import sorting

### Prettier

Configuration dans `.prettierrc`:
```json
{
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 100,
  "tabWidth": 2,
  "semi": true,
  "bracketSpacing": true,
  "endOfLine": "lf"
}
```

### Jest pour les tests

Configuration dans `jest.config.js` pour:
- Tests unitaires
- Tests d'intégration
- Couverture de code
- Mocking

### Variables d'environnement

Utilisation de `.env` (non versionné) avec `.env.example` comme modèle, contenant:
- Configuration de la base de données
- Clés secrètes pour JWT
- Ports et configuration du serveur
- Environnement (dev/prod/test)

## Hooks Git et intégration continue

### Hooks pre-commit

Utilisation de Husky pour exécuter:
- Lint-staged pour vérifier uniquement les fichiers modifiés
- Validation des tests unitaires
- Vérification du formatage

### Husky et lint-staged

Configuration dans `package.json`:
```json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write",
      "jest --findRelatedTests"
    ]
  }
}
```

## Procédure d'initialisation

### Étapes d'installation

```bash
# Créer le projet NestJS
$ npm i -g @nestjs/cli
$ nest new backend
$ cd backend

# Installer les dépendances principales
$ npm install @nestjs/typeorm typeorm pg @nestjs/passport passport passport-jwt passport-local @nestjs/jwt @nestjs/config class-validator class-transformer bcrypt helmet

# Installer les dépendances de développement
$ npm install --save-dev @types/passport-jwt @types/passport-local @types/bcrypt husky lint-staged
```

### Configuration initiale

1. Configurer TypeScript (`tsconfig.json`)
2. Configurer ESLint et Prettier
3. Configurer Jest
4. Configurer Husky et lint-staged
5. Mettre en place les variables d'environnement

### Structure de dossiers initiale

Création de la structure de base définie dans la section Architecture.

## Standards de développement

### Conventions de nommage

- **Fichiers**: kebab-case (ex: `auth.service.ts`)
- **Classes**: PascalCase (ex: `AuthService`)
- **Variables/Fonctions**: camelCase (ex: `getUserById`)
- **Constantes**: UPPER_SNAKE_CASE (ex: `MAX_LOGIN_ATTEMPTS`)
- **Interfaces**: PascalCase avec préfixe I (ex: `IUser`)
- **Types**: PascalCase (ex: `UserRole`)
- **Modules**: camelCase (ex: `authModule`)

### Documentation du code

- Commentaires JSDoc pour les classes, méthodes et fonctions
- Description des paramètres, retours et exceptions
- Mise à jour du README.md avec les instructions d'installation et d'utilisation

### Tests

- Tests unitaires pour les services et utilities
- Tests d'intégration pour les contrôleurs
- Tests e2e pour les flux complets
- Maintenir une couverture de code > 80%

## Prochaines étapes

Après l'initialisation du backend, les prochaines étapes seront:

1. Configuration de la base de données (MongoDB ou PostgreSQL)
2. Implémentation du système d'authentification
3. Développement des premiers endpoints API
4. Mise en place des validations de données
5. Configuration des middlewares de sécurité

Ces étapes seront détaillées dans des documents de spécifications dédiés.