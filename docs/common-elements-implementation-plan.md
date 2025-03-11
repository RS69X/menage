# Plan d'implémentation des éléments communs

Ce document décrit le plan d'implémentation détaillé des éléments communs de l'application backend, qui font partie de la Phase 1 du développement.

## Table des matières
1. [Aperçu général](#aperçu-général)
2. [Architecture des éléments communs](#architecture-des-éléments-communs)
3. [Spécifications détaillées](#spécifications-détaillées)
   - [Guards](#guards)
   - [Filters](#filters)
   - [Interceptors](#interceptors)
   - [Decorators](#decorators)
   - [Pipes](#pipes)
   - [Middleware](#middleware)
   - [Constants](#constants)
   - [Interfaces](#interfaces)
   - [Utils](#utils)
4. [Plan d'implémentation](#plan-dimplémentation)
5. [État d'avancement](#état-davancement)
6. [Relations avec les autres modules](#relations-avec-les-autres-modules)
7. [Tests](#tests)
8. [Documentation](#documentation)

## Aperçu général

Les éléments communs (common elements) constituent l'infrastructure de base partagée entre les différents modules de l'application. Ils assurent la cohérence du système, permettent la réutilisation du code et simplifient le développement des fonctionnalités métier. 

Ces éléments sont organisés dans le dossier `src/common` et comprennent:
- **Guards**: Protection des routes et gestion des autorisations
- **Filters**: Gestion uniforme des exceptions
- **Interceptors**: Traitement transversal des requêtes et réponses
- **Decorators**: Annotations personnalisées pour simplifier l'utilisation des fonctionnalités
- **Pipes**: Validation et transformation des données
- **Middleware**: Traitements intermédiaires des requêtes
- **Constants**: Valeurs partagées entre les modules
- **Interfaces**: Types communs utilisés dans toute l'application
- **Utils**: Fonctions utilitaires

## Architecture des éléments communs

L'architecture des éléments communs suit une structure organisée qui facilite la maintenance et l'évolution du code:

```
src/
└── common/
    ├── constants/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── app.constants.ts         # Constantes générales de l'application
    │   ├── roles.constants.ts       # Types d'utilisateurs et rôles
    │   ├── error-codes.constants.ts # Codes d'erreur standardisés
    │   └── validation.constants.ts  # Constantes pour les règles de validation
    │
    ├── decorators/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── public.decorator.ts      # Marque les routes comme publiques
    │   ├── roles.decorator.ts       # Définit les rôles requis pour une route
    │   ├── current-user.decorator.ts # Accès à l'utilisateur dans les contrôleurs
    │   └── api-pagination.decorator.ts # Pagination standardisée pour Swagger
    │
    ├── filters/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── http-exception.filter.ts # Gestion des exceptions HTTP
    │   ├── validation.filter.ts     # Traitement des erreurs de validation
    │   └── database-exception.filter.ts # Gestion des erreurs de base de données
    │
    ├── guards/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── roles.guard.ts           # Vérification des rôles utilisateur
    │   └── api-key.guard.ts         # Protection pour les API publiques (si nécessaire)
    │
    ├── interceptors/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── logging.interceptor.ts   # Journalisation des requêtes/réponses
    │   ├── transform.interceptor.ts # Normalisation des réponses API
    │   ├── timeout.interceptor.ts   # Gestion des timeouts de requêtes
    │   └── cache.interceptor.ts     # Mise en cache des réponses (si applicable)
    │
    ├── interfaces/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── paginated-result.interface.ts # Interface de pagination standardisée
    │   ├── api-response.interface.ts # Structure de réponse API commune
    │   └── service-response.interface.ts # Structure pour communication inter-services
    │
    ├── middleware/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── logger.middleware.ts     # Journalisation des requêtes entrantes
    │   └── cors.middleware.ts       # Configuration CORS personnalisée (si nécessaire)
    │
    ├── pipes/
    │   ├── index.ts                 # Agrégation des exports
    │   ├── validation.pipe.ts       # Validation globale basée sur class-validator
    │   ├── parse-object-id.pipe.ts  # Validation des ID MongoDB (si utilisé)
    │   └── sanitize.pipe.ts         # Nettoyage des entrées utilisateur
    │
    └── utils/
        ├── index.ts                 # Agrégation des exports
        ├── config.utils.ts          # Utilitaires de configuration
        ├── date.utils.ts            # Manipulation des dates
        ├── security.utils.ts        # Fonctions liées à la sécurité
        ├── pagination.utils.ts      # Fonctions d'aide à la pagination
        └── testing.utils.ts         # Utilitaires pour les tests
```

## Spécifications détaillées

### Guards

Les guards déterminent si une requête doit être traitée par le handler de route, principalement utilisés pour l'authentification et l'autorisation.

#### JwtAuthGuard (déjà implémenté)

Utilise la stratégie JWT pour vérifier la validité du token d'accès fourni dans les en-têtes de la requête.

#### RolesGuard

**Fonctionnalité**: Vérifie si l'utilisateur authentifié possède les rôles requis pour accéder à une route.

**Spécifications**:
- Doit être utilisé après JwtAuthGuard pour avoir accès à l'utilisateur authentifié
- Utilise le décorateur `@Roles()` pour définir les rôles autorisés
- Vérifie le type d'utilisateur (host ou cleaner) dans l'objet utilisateur JWT
- Implémente l'interface `CanActivate` de NestJS
- Rejette les requêtes non autorisées avec un statut 403 (Forbidden)

**Dépendances**:
- `@nestjs/common`, `@nestjs/core`
- Décorateur `Roles`
- Interfaces de types d'utilisateur

#### PublicEndpointGuard

**Fonctionnalité**: Permet de marquer certains endpoints comme publics, exemptés de l'authentification JWT.

**Spécifications**:
- Travaille de concert avec JwtAuthGuard
- Utilise le décorateur `@Public()` pour marquer les routes publiques
- Utilise le réflecteur (Reflector) de NestJS pour lire les métadonnées
- Bypass l'authentification pour les routes marquées comme publiques

**Dépendances**:
- `@nestjs/common`, `@nestjs/core`
- Décorateur `Public`

#### ApiKeyGuard (optionnel)

**Fonctionnalité**: Vérifie la présence et la validité d'une clé API pour certaines routes publiques ou internes.

**Spécifications**:
- Vérifie la clé API dans l'en-tête HTTP ou les paramètres de requête
- Compare avec les clés API configurées dans l'application
- Peut être configuré pour des modes d'accès différents (lecture seule, administrateur)

**Dépendances**:
- `@nestjs/common`, `@nestjs/config`

### Filters

Les filters gèrent les exceptions lancées pendant le traitement des requêtes et les transforment en réponses HTTP appropriées.

#### HttpExceptionFilter

**Fonctionnalité**: Transforme toutes les exceptions HTTP en réponse au format standardisé.

**Spécifications**:
- Implémente l'interface `ExceptionFilter` de NestJS
- Capture toutes les exceptions dérivant de `HttpException`
- Standardise le format de réponse pour les erreurs avec:
  - `statusCode`: Code HTTP
  - `message`: Message d'erreur
  - `error`: Type d'erreur
  - `timestamp`: Date/heure
  - `path`: Chemin de la requête
- Journalise les erreurs avec niveau approprié
- Intègre les codes d'erreur personnalisés si présents

**Dépendances**:
- `@nestjs/common`
- Service de journalisation

#### ValidationExceptionFilter

**Fonctionnalité**: Gère spécifiquement les erreurs de validation des DTOs.

**Spécifications**:
- Capture les exceptions `BadRequestException` issues de validation
- Formate les erreurs de validation en format convivial:
  - Transforme les erreurs de validation en structure hiérarchique claire
  - Groupe les erreurs par champ
  - Fournit des messages d'erreur spécifiques
- Retourne un statut 400 avec structure détaillée des erreurs

**Dépendances**:
- `@nestjs/common`
- `class-validator`

#### DatabaseExceptionFilter

**Fonctionnalité**: Gère les exceptions spécifiques à la base de données.

**Spécifications**:
- Capture les exceptions TypeORM ou MongoDB (selon la base choisie)
- Masque les détails techniques de l'erreur en production
- Transforme les erreurs de contrainte d'unicité en messages conviviaux
- Journalise les erreurs de base de données en détail pour le debugging

**Dépendances**:
- `@nestjs/common`
- TypeORM ou MongoDB (selon choix)
- Service de journalisation

### Interceptors

Les interceptors permettent d'intercepter et de transformer les requêtes et les réponses avant et après l'exécution des handlers de route.

#### LoggingInterceptor

**Fonctionnalité**: Journalise les informations sur les requêtes et les réponses.

**Spécifications**:
- Implémente l'interface `NestInterceptor` de NestJS
- Enregistre l'horodatage, la méthode, le chemin et l'IP de la requête
- Mesure le temps de réponse
- Journalise le code de statut de la réponse
- Masque les données sensibles dans les journaux (mots de passe, tokens, etc.)
- Niveau de détail configurable selon l'environnement

**Dépendances**:
- `@nestjs/common`, `rxjs`
- Service de journalisation

#### TransformInterceptor

**Fonctionnalité**: Normalise le format des réponses API.

**Spécifications**:
- Standardise toutes les réponses réussies dans un format uniforme:
  ```json
  {
    "success": true,
    "data": { /* données originales */ },
    "timestamp": "2023-06-15T10:20:30Z",
    "path": "/api/resource"
  }
  ```
- Préserve le code de statut HTTP original
- Gère correctement les réponses de pagination
- Supporte les réponses vides et nulles

**Dépendances**:
- `@nestjs/common`, `rxjs`

#### TimeoutInterceptor

**Fonctionnalité**: Applique un délai d'expiration aux requêtes.

**Spécifications**:
- Utilise l'opérateur `timeout` de RxJS
- Délai configurable (par défaut: 30 secondes)
- Lance une exception `RequestTimeoutException` si dépassé
- Peut être désactivé pour certaines routes via des métadonnées

**Dépendances**:
- `@nestjs/common`, `rxjs`

#### CacheInterceptor (optionnel)

**Fonctionnalité**: Met en cache les réponses des endpoints GET pour améliorer les performances.

**Spécifications**:
- Configure le cache en fonction de la route et des paramètres
- Durée de cache configurable par route
- Invalidation du cache à la modification des ressources
- Ne met pas en cache les réponses pour utilisateurs authentifiés (par défaut)

**Dépendances**:
- `@nestjs/common`, `@nestjs/cache-manager`, `cache-manager`

### Decorators

Les décorateurs sont des annotations permettant d'ajouter des métadonnées aux classes ou méthodes.

#### PublicDecorator

**Fonctionnalité**: Marque un endpoint comme étant public (sans authentification).

**Spécifications**:
- Utilisé en conjonction avec le `PublicEndpointGuard`
- Définit une métadonnée 'isPublic' à true
- S'applique aux méthodes de contrôleur

**Dépendances**:
- `@nestjs/common`

#### RolesDecorator

**Fonctionnalité**: Définit les rôles/types d'utilisateurs autorisés pour un endpoint.

**Spécifications**:
- Utilisé en conjonction avec le `RolesGuard`
- Accepte un ou plusieurs types d'utilisateur (`UserType.HOST`, `UserType.CLEANER`)
- Définit une métadonnée 'roles' avec les rôles spécifiés
- S'applique aux méthodes de contrôleur

**Dépendances**:
- `@nestjs/common`
- Constantes de rôles/types d'utilisateur

#### CurrentUserDecorator

**Fonctionnalité**: Injecte l'utilisateur actuel dans les paramètres du handler.

**Spécifications**:
- Extrait l'utilisateur de la requête (placé par JwtAuthGuard)
- Permet d'accéder facilement aux informations de l'utilisateur authentifié
- Options pour exclure certains champs (mot de passe, etc.)
- Peut lever une exception si aucun utilisateur n'est trouvé

**Dépendances**:
- `@nestjs/common`

#### ApiPaginationDecorator

**Fonctionnalité**: Configure Swagger pour documenter correctement les endpoints paginés.

**Spécifications**:
- Ajoute automatiquement les paramètres de pagination à la documentation
- Définit le schéma de réponse paginée
- Prend en charge les options de pagination (taille de page, tri, etc.)

**Dépendances**:
- `@nestjs/swagger`
- Interface de pagination

### Pipes

Les pipes transforment ou valident les données avant qu'elles n'atteignent le handler.

#### ValidationPipe (configuration)

**Fonctionnalité**: Configuration avancée du ValidationPipe global de NestJS.

**Spécifications**:
- Validation automatique des DTOs basée sur class-validator
- Options configurées:
  - `transform: true` - conversion automatique des types
  - `whitelist: true` - suppression des propriétés non décorées
  - `forbidNonWhitelisted: true` - erreur pour les propriétés non autorisées
  - `stopAtFirstError: true` - arrêt à la première erreur par propriété
- Messages d'erreur personnalisés et localisables
- Gestion des groupes de validation (création vs mise à jour)

**Dépendances**:
- `@nestjs/common`
- `class-validator`, `class-transformer`

#### ParseObjectIdPipe (si MongoDB)

**Fonctionnalité**: Valide et convertit les ID en ObjectId MongoDB.

**Spécifications**:
- Vérifie si la chaîne fournie est un ObjectId MongoDB valide
- Convertit en instance ObjectId
- Lance BadRequestException si invalide
- Message d'erreur personnalisable

**Dépendances**:
- `@nestjs/common`
- `mongodb`

#### SanitizePipe

**Fonctionnalité**: Nettoie les entrées utilisateur pour prévenir les attaques.

**Spécifications**:
- Supprime ou échappe les caractères potentiellement dangereux
- Protection contre XSS, injection SQL, etc.
- Configuration différente selon le contexte (HTML, SQL, etc.)
- Peut être appliqué à des paramètres spécifiques ou globalement

**Dépendances**:
- `@nestjs/common`
- `sanitize-html` ou bibliothèque similaire

### Middleware

Les middleware sont des fonctions exécutées avant le traitement des routes par les handlers.

#### LoggerMiddleware

**Fonctionnalité**: Journalise les informations sur chaque requête entrante.

**Spécifications**:
- Implémente l'interface `NestMiddleware`
- Enregistre la méthode, le chemin, l'IP et les en-têtes pertinents
- Niveau de détail configurable
- Masque les données sensibles dans les URLs et en-têtes
- Peut être configuré pour inclure le corps de la requête en développement

**Dépendances**:
- `@nestjs/common`
- Service de journalisation

#### CorsMiddleware (si nécessaire)

**Fonctionnalité**: Configuration personnalisée des règles CORS.

**Spécifications**:
- Configuration avancée de CORS au-delà des options de base
- Gestion des origines autorisées selon l'environnement
- Contrôle précis des méthodes et en-têtes autorisés
- Gestion des cookies et informations d'authentification

**Dépendances**:
- `@nestjs/common`
- Services de configuration

### Constants

Les constantes sont des valeurs partagées entre les différents modules de l'application.

#### AppConstants ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Définit les constantes générales de l'application.

**Spécifications**:
- Préfixes d'API
- Valeurs de pagination par défaut
- Timeouts
- Tailles de buffer et limites
- Noms des fournisseurs injectables

**Implémentation**:
- Constantes de pagination (DEFAULT_PAGE_NUMBER, DEFAULT_PAGE_SIZE, MAX_PAGE_SIZE)
- Constantes de tri (DEFAULT_SORT_ORDER)
- Constantes d'API (API_PREFIX, API_VERSION)
- Constantes d'application (APP_NAME, APP_DESCRIPTION)
- Constantes de timeout (REQUEST_TIMEOUT)

#### RolesConstants ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Définit les types d'utilisateurs et leurs rôles.

**Spécifications**:
- Enum des types d'utilisateur (HOST, CLEANER)
- Constantes pour les rôles spéciaux (ADMIN, SUPER_ADMIN)
- Mappages entre types et permissions

**Implémentation**:
- Enum UserType avec les valeurs HOST et CLEANER
- Constantes pour les rôles administratifs
- Structure ROLE_PERMISSIONS définissant les autorisations par rôle

**Note d'implémentation**:
- Pour éviter les références circulaires dans la définition des permissions administratives, les permissions de base sont définies dans des constantes intermédiaires (`AdminBasePermissions`, `SuperAdminBasePermissions`) avant d'être utilisées dans l'objet `AdminPermissions` exporté.

#### ErrorCodesConstants ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Catalogue des codes d'erreur standardisés.

**Spécifications**:
- Codes d'erreur numériques uniques par catégorie
- Messages d'erreur par défaut associés
- Organisation par module/domaine d'erreur
- Descriptions détaillées pour la documentation

**Implémentation**:
- Codes d'erreur d'authentification (AUTH_INVALID_CREDENTIALS, AUTH_TOKEN_EXPIRED, etc.)
- Codes d'erreur de validation (VALIDATION_FAILED, INVALID_INPUT, etc.)
- Codes d'erreur de ressources (RESOURCE_NOT_FOUND, RESOURCE_ALREADY_EXISTS, etc.)
- Codes d'erreur de permission (PERMISSION_DENIED, INSUFFICIENT_RIGHTS, etc.)
- Codes d'erreur serveur (SERVER_ERROR, DATABASE_ERROR, etc.)

#### ValidationConstants ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Constantes utilisées dans les règles de validation.

**Spécifications**:
- Expressions régulières pour validation (email, téléphone, SIRET, etc.)
- Limites de longueur pour différents champs
- Messages d'erreur par défaut
- Configuration des contraintes de sécurité (complexité de mot de passe)

**Implémentation**:
- Expressions régulières (EMAIL_REGEX, PHONE_REGEX, SIRET_REGEX, etc.)
- Constantes de longueur (MIN_PASSWORD_LENGTH, MAX_NAME_LENGTH, etc.)
- Messages d'erreur standardisés pour la validation
- Contraintes de sécurité pour les mots de passe (PASSWORD_PATTERN)

### Interfaces

Les interfaces définissent les structures de données communes utilisées dans l'application.

#### PaginatedResultInterface ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Structure standard pour les réponses paginées.

**Spécifications**:
```typescript
interface PaginatedResult<T> {
  items: T[];
  meta: {
    totalItems: number;
    itemsPerPage: number;
    currentPage: number;
    totalPages: number;
    sortBy?: string;
    sortOrder?: 'ASC' | 'DESC';
  };
}
```

**Implémentation**:
- Interface PaginatedResult<T> avec le tableau d'items et les métadonnées de pagination
- Interface PaginationMeta pour les métadonnées détaillées de pagination
- Interface PaginationOptions pour configurer les requêtes paginées
- Support pour le tri et la pagination selon les standards de l'API

#### ApiResponseInterface ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Structure standard pour les réponses API.

**Spécifications**:
```typescript
interface ApiResponse<T> {
  success: boolean;
  data: T;
  timestamp: string;
  path: string;
}
```

**Implémentation**:
- Interface ApiResponse<T> pour les réponses réussies
- Interface ApiErrorResponse pour les réponses d'erreur standardisées
- Support pour les métadonnées supplémentaires
- Timestamp et information de chemin inclus automatiquement
- Interface ValidationErrorItem pour structurer les erreurs de validation
- Champ validationErrors dans ApiErrorResponse pour les erreurs de validation structurées

#### ServiceResponseInterface ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Structure pour les réponses des services internes.

**Spécifications**:
```typescript
interface ServiceResponse<T> {
  success: boolean;
  data?: T;
  error?: {
    code: string;
    message: string;
  };
}
```

**Implémentation**:
- Interface ServiceResponse<T> pour la communication interne
- Interface ServiceError pour les détails d'erreur
- Utilitaires ServiceResponseUtils pour créer facilement des réponses
- Support pour la propagation d'erreurs avec codes et détails

### Utils

Les utilitaires sont des fonctions réutilisables pour des tâches communes.

#### ConfigUtils (déjà implémenté)

Utilitaires pour la gestion des configurations.

#### DateUtils ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Fonctions pour la manipulation des dates.

**Spécifications**:
- Formatage de date selon différents standards
- Calcul de différences entre dates
- Validation de dates
- Conversion entre fuseaux horaires
- Gestion de jours ouvrables et fériés (pour la planification des ménages)

**Implémentation**:
- Constantes DATE_FORMATS pour les formats standardisés
- Fonctions d'ajout de périodes (addDays, addMonths, addYears)
- Fonctions de formatage (formatISODate, formatHumanDate)
- Fonctions de validation (isValidDate, isDateBetween)
- Fonctions de périodes (startOfWeek, endOfMonth)
- Utilitaires pour les plages de dates (doDateRangesOverlap, getDatesInRange)
- Fonctions pour les conversions et calculs (toUTC, calculateAge)

#### SecurityUtils ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Fonctions liées à la sécurité.

**Spécifications**:
- Hachage et vérification de mots de passe
- Génération de tokens aléatoires
- Chiffrement et déchiffrement de données sensibles
- Validation de la complexité des mots de passe
- Nettoyage des données utilisateur

**Implémentation**:
- Configuration SECURITY_CONFIG avec paramètres de sécurité
- Fonctions de hachage (hashPassword, verifyPassword) avec bcrypt
- Génération de jetons (generateSecureToken, generateExpiringToken)
- Validation de jetons (verifyExpiringToken)
- Fonctions de chiffrement (encryptData, decryptData) avec AES-GCM
- Protection XSS (sanitizeText)
- Utilitaires crypto (generateRandomString)

#### PaginationUtils ✅ (IMPLÉMENTÉ)

**Fonctionnalité**: Fonctions d'aide à la pagination.

**Spécifications**:
- Conversion des paramètres de pagination en options TypeORM/MongoDB
- Création d'objets de réponse paginée standardisés
- Gestion du tri et des filtres
- Validation des paramètres de pagination

**Implémentation**:
- Normalisation des options (normalizePaginationOptions)
- Calcul d'offset pour les requêtes SQL (calculateOffset)
- Création de résultats paginés (createPaginatedResult)
- Génération de liens HATEOAS (generatePaginationLinks)
- Intégration avec TypeORM (paginateTypeOrmQuery)
- Adaptation des paramètres de requête HTTP (paginationOptionsFromQuery)

**Note d'implémentation**:
- La propriété `sortBy` de `PaginationOptions` est normalisée comme une chaîne vide (`''`) par défaut lorsqu'elle n'est pas définie, au lieu de rester `undefined`. Cela permet de satisfaire le type de retour `Required<PaginationOptions>` tout en préservant le comportement conditionnel dans les fonctions qui en dépendent (les vérifications de type `if (sortBy)` continuent de fonctionner comme prévu puisqu'une chaîne vide est évaluée comme falsy).

#### TestingUtils

**Fonctionnalité**: Utilitaires pour les tests.

**Spécifications**:
- Création d'entités de test avec données aléatoires
- Mocks pour les services et dépendances
- Fonctions d'assertion personnalisées
- Utilitaires pour tester l'authentification
- Création de requêtes HTTP pour les tests d'intégration

**Dépendances**:
- `@nestjs/testing`, `jest`
- `faker` ou bibliothèque similaire

## Plan d'implémentation

L'implémentation des éléments communs se fait selon les priorités et dépendances suivantes:

### Étape 1: Structure de base et utilitaires ✅ (TERMINÉ)
1. ✅ Créer la structure de dossiers pour tous les éléments communs
2. ✅ Implémenter les constantes de base
   - ✅ app.constants.ts - Constantes générales de l'application
   - ✅ roles.constants.ts - Types d'utilisateurs et leurs permissions
   - ✅ error-codes.constants.ts - Catalogue des codes d'erreur standardisés
   - ✅ validation.constants.ts - Règles de validation et expressions régulières
3. ✅ Implémenter les interfaces fondamentales
   - ✅ api-response.interface.ts - Structure standard pour les réponses API
   - ✅ paginated-result.interface.ts - Structure pour les résultats paginés
   - ✅ service-response.interface.ts - Structure pour communication inter-services
4. ✅ Implémenter les utilitaires de base
   - ✅ security.utils.ts - Fonctions liées à la sécurité
   - ✅ date.utils.ts - Manipulation des dates
   - ✅ pagination.utils.ts - Fonctions d'aide à la pagination

### Étape 2: Éléments d'authentification et d'autorisation ✅ (TERMINÉ)
1. ✅ Implémenter les décorateurs
   - ✅ public.decorator.ts - Pour marquer les routes comme publiques
   - ✅ roles.decorator.ts - Pour définir les rôles requis pour une route
   - ✅ current-user.decorator.ts - Pour accéder à l'utilisateur dans les contrôleurs
   - ✅ timeout.decorator.ts - Pour configurer le timeout des requêtes
2. ✅ Implémenter/configurer les guards
   - ✅ roles.guard.ts - Pour vérifier les rôles utilisateur
   - ✅ public-endpoint.guard.ts - Pour gérer les routes publiques
   - ✅ jwt-auth.guard.ts - Pour l'authentification JWT
3. ⏳ Tester les fonctionnalités d'authentification et d'autorisation

### Étape 3: Traitement des requêtes et réponses ⏳ (PARTIELLEMENT TERMINÉ)
1. ⏳ Implémenter les interceptors
   - ⏳ logging.interceptor.ts - Pour journaliser les requêtes/réponses
   - ⏳ transform.interceptor.ts - Pour normaliser les réponses API
   - ✅ timeout.interceptor.ts - Pour gérer les timeouts de requêtes
2. ✅ Implémenter les filters
   - ✅ http-exception.filter.ts - Pour standardiser les réponses d'erreur
   - ✅ validation.filter.ts - Pour formater les erreurs de validation
   - ✅ index.ts - Fichier d'exportation centralisé pour les filtres
3. ⏳ Configurer les pipes
   - ⏳ Configuration du ValidationPipe global avec options avancées

### Étape 4: Éléments avancés et spécifiques ⏳ (À VENIR)
1. ⏳ Implémenter les middleware
   - ⏳ logger.middleware.ts - Pour journaliser les requêtes entrantes
   - ⏳ cors.middleware.ts - Configuration CORS avancée (si nécessaire)
2. ⏳ Implémenter les utilitaires avancés
   - ⏳ testing.utils.ts - Utilitaires pour les tests automatisés
3. ⏳ Implémenter les pipes spécifiques
   - ⏳ parse-object-id.pipe.ts - Pour valider les identifiants MongoDB (si utilisé)
   - ⏳ sanitize.pipe.ts - Pour nettoyer les entrées utilisateur
4. ⏳ Implémenter les filtres spécifiques
   - ⏳ database-exception.filter.ts - Pour gérer les erreurs de base de données

### Étape 5: Tests et documentation ⏳ (À VENIR)
1. ⏳ Finaliser les tests unitaires pour tous les éléments communs
2. ⏳ Documenter l'utilisation de chaque élément commun
3. ⏳ Intégrer les éléments dans les modules existants
4. ⏳ Créer des exemples d'utilisation pour chaque type d'élément

## État d'avancement

### Composants implémentés

#### Constants ✅
- [x] app.constants.ts - Définit les constantes globales de l'application
- [x] roles.constants.ts - Définit les types d'utilisateurs et leurs permissions
- [x] error-codes.constants.ts - Catalogue complet des codes d'erreur standardisés
- [x] validation.constants.ts - Règles de validation et expressions régulières

#### Interfaces ✅
- [x] api-response.interface.ts - Structure standard pour toutes les réponses API
- [x] paginated-result.interface.ts - Structure pour la pagination des résultats
- [x] service-response.interface.ts - Structure pour la communication inter-services

#### Utils ✅
- [x] security.utils.ts - Fonctions complètes de sécurité (hachage, chiffrement, tokens)
- [x] date.utils.ts - Manipulation avancée des dates pour les réservations
- [x] pagination.utils.ts - Utilitaires de pagination intégrables avec TypeORM

#### Decorators ✅
- [x] public.decorator.ts - Pour marquer les routes comme publiques
- [x] roles.decorator.ts - Pour définir les rôles requis pour une route
- [x] current-user.decorator.ts - Pour accéder à l'utilisateur dans les contrôleurs
- [x] timeout.decorator.ts - Pour configurer le timeout des requêtes

#### Guards ✅
- [x] jwt-auth.guard.ts - Vérification des tokens JWT
- [x] roles.guard.ts - Vérification des rôles utilisateur
- [x] public-endpoint.guard.ts - Gestion des routes publiques

#### Interceptors ⏳
- [ ] transform.interceptor.ts - Pour normaliser les réponses API
- [ ] logging.interceptor.ts - Pour journaliser les requêtes/réponses
- [x] timeout.interceptor.ts - Pour gérer les timeouts de requêtes

#### Filters ✅
- [x] http-exception.filter.ts - Pour standardiser les réponses d'erreur
- [x] validation.filter.ts - Pour formater les erreurs de validation
- [x] index.ts - Fichier d'exportation centralisé pour les filtres

### Prochaines priorités

1. **Interceptors restants**
   - Implémentation du TransformInterceptor pour normaliser toutes les réponses API
   - Implémentation du LoggingInterceptor pour journaliser les requêtes/réponses
   
2. **Configuration du ValidationPipe global**
   - Configuration avancée avec options spécifiques au projet
   - Intégration avec les filtres d'exception existants
   
3. **Intégration dans main.ts**
   - Documentation et exemple d'intégration dans le fichier main.ts
   - Ordre correct d'application des filtres, interceptors et pipes

## Relations avec les autres modules

Les éléments communs interagissent avec d'autres parties de l'application:

### Module Auth
- Les `guards` protègent les routes nécessitant une authentification
- Les `decorators` comme `@Roles()` et `@CurrentUser()` simplifient l'implémentation des contrôleurs
- Les `filters` standardisent la gestion des erreurs d'authentification

### Module Users
- Les `pipes` valident les DTO des utilisateurs
- Les `interceptors` normalisent les réponses
- Les `utils` de sécurité gèrent le hachage des mots de passe et le chiffrement des données sensibles

### Autres modules
- Tous les modules bénéficient des `filters` d'exception, des `interceptors` et des `pipes` de validation
- Les `constants` et `interfaces` assurent la cohérence entre les modules
- Les `utils` de pagination simplifient l'implémentation des listes paginées

## Tests

Chaque élément commun doit être testé de manière approfondie:

### Tests unitaires
- Tests pour chaque guard, filter, interceptor, decorator, pipe
- Tests avec différentes entrées et cas limites
- Mocks pour isoler les dépendances

### Tests d'intégration
- Tests combinant plusieurs éléments communs
- Vérification des interactions entre éléments
- Tests des scénarios d'erreur et de succès

## Documentation

La documentation des éléments communs doit inclure:

### Documentation inline
- Commentaires JSDoc pour toutes les classes, méthodes, interfaces et types
- Exemples d'utilisation dans les commentaires

### Documentation utilisateur
- Guide d'utilisation de chaque élément commun
- Exemples concrets d'implémentation
- Bonnes pratiques et pièges à éviter

### Documentation Swagger
- Décorateurs pour documenter les structures de données communes
- Documentation des codes d'erreur standardisés
- Exemples de réponses pour les différents types d'erreurs