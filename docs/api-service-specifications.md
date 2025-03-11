# Spécifications des Services API et Storage

## Introduction

Ce document détaille l'implémentation des services API et Storage du frontend de l'application Menage Connect, qui permettent la communication avec le backend et la gestion des données locales sur l'appareil de l'utilisateur.

## 1. Service API

Le service API est responsable de toutes les communications avec le backend. Il utilise Axios pour effectuer des requêtes HTTP et gère les tokens d'authentification, les erreurs, et la transformation des données.

### 1.1 Structure des fichiers

```
src/services/api/
├── index.ts                 # Export central de tous les services API
├── client.ts                # Configuration du client Axios
├── interceptors.ts          # Intercepteurs pour la gestion des tokens et erreurs
├── auth.ts                  # Service d'authentification (login, register, etc.)
├── users.ts                 # Service de gestion des utilisateurs
├── listings.ts              # Service de gestion des annonces (à implémenter ultérieurement)
└── bookings.ts              # Service de gestion des réservations (à implémenter ultérieurement)
```

### 1.2 Client API (`client.ts`)

**Objectif** : Configurer et exposer une instance Axios partagée dans toute l'application.

**Fonctionnalités** :
- Configuration de base (URL, timeout, headers par défaut)
- Application des intercepteurs
- Utilitaire de normalisation des erreurs

**Interface** :
```typescript
export const apiClient: AxiosInstance;
export const normalizeApiError: (error: any) => {
  message: string;
  status?: number;
  errors?: Record<string, string[]>;
};
```

### 1.3 Intercepteurs (`interceptors.ts`)

**Objectif** : Gérer automatiquement les tokens d'authentification et le traitement des erreurs.

**Fonctionnalités** :
- Ajout automatique du token d'authentification aux requêtes
- Gestion du rafraîchissement des tokens expirés
- Interception et traitement cohérent des erreurs
- Redirection vers la page de login en cas d'erreur d'authentification

**Interface** :
```typescript
export const setupInterceptors: (client: AxiosInstance) => void;
```

### 1.4 Service d'authentification (`auth.ts`)

**Objectif** : Gérer toutes les interactions liées à l'authentification.

**Endpoints** :
- **registerHost**: Inscription d'un hébergeur (`POST /auth/register/host`)
- **registerCleaner**: Inscription d'un professionnel de ménage (`POST /auth/register/cleaner`)
- **login**: Connexion d'un utilisateur (`POST /auth/login`)
- **refreshToken**: Rafraîchissement du token d'accès (`POST /auth/refresh-token`)
- **verifyEmail**: Vérification de l'email (`GET /auth/verify-email/:token`)
- **requestPasswordReset**: Demande de réinitialisation du mot de passe (`POST /auth/reset-password-request`)
- **resetPassword**: Réinitialisation du mot de passe (`POST /auth/reset-password`)
- **getProfile**: Récupération du profil de l'utilisateur connecté (`GET /auth/profile`)
- **logout**: Déconnexion (local + invalidation token côté serveur si implémenté)

**Interface** :
```typescript
interface AuthService {
  registerHost: (data: RegisterHostForm) => Promise<AuthResponse>;
  registerCleaner: (data: RegisterCleanerForm) => Promise<AuthResponse>;
  login: (data: LoginForm) => Promise<AuthResponse>;
  refreshToken: (refreshToken: string) => Promise<AuthResponse>;
  verifyEmail: (token: string) => Promise<{ message: string }>;
  requestPasswordReset: (email: string) => Promise<{ message: string }>;
  resetPassword: (data: ResetPasswordForm) => Promise<{ message: string }>;
  getProfile: () => Promise<User>;
  logout: () => Promise<void>;
}

export const authService: AuthService;
```

### 1.5 Service utilisateurs (`users.ts`)

**Objectif** : Gérer les interactions liées aux profils utilisateurs.

**Endpoints** :
- **updateHostProfile**: Mise à jour du profil hébergeur (`PATCH /users/host/:id`)
- **updateCleanerProfile**: Mise à jour du profil professionnel (`PATCH /users/cleaner/:id`)
- **getHostById**: Récupération d'un profil hébergeur (`GET /users/host/:id`)
- **getCleanerById**: Récupération d'un profil professionnel (`GET /users/cleaner/:id`)

**Interface** :
```typescript
interface UsersService {
  updateHostProfile: (id: string, data: UpdateHostProfileForm) => Promise<Host>;
  updateCleanerProfile: (id: string, data: UpdateCleanerProfileForm) => Promise<Cleaner>;
  getHostById: (id: string) => Promise<Host>;
  getCleanerById: (id: string) => Promise<Cleaner>;
}

export const usersService: UsersService;
```

### 1.6 Service d'export central (`index.ts`)

**Objectif** : Centraliser l'export de tous les services API pour simplifier les imports.

**Contenu** :
```typescript
export * from './client';
export * from './auth';
export * from './users';
// Autres services à ajouter au fur et à mesure
```

## 2. Service Storage

Le service Storage est responsable de la persistance locale des données sur l'appareil de l'utilisateur, notamment les tokens d'authentification, les informations utilisateur, et les préférences.

### 2.1 Structure des fichiers

```
src/services/storage/
├── index.ts                 # Export central de tous les services de stockage
├── authStorage.ts           # Stockage sécurisé des tokens d'authentification
├── userStorage.ts           # Persistance des informations utilisateur
└── preferencesStorage.ts    # Gestion des préférences utilisateur
```

### 2.2 Stockage d'authentification (`authStorage.ts`)

**Objectif** : Gérer de manière sécurisée les tokens d'authentification.

**Fonctionnalités** :
- Stockage sécurisé des tokens (access et refresh) avec Expo SecureStore
- Récupération des tokens
- Suppression des tokens lors de la déconnexion

**Interface** :
```typescript
interface AuthTokens {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
}

interface AuthStorage {
  storeTokens: (tokens: AuthTokens) => Promise<void>;
  getTokens: () => Promise<AuthTokens | null>;
  getAccessToken: () => Promise<string | null>;
  getRefreshToken: () => Promise<string | null>;
  clearTokens: () => Promise<void>;
}

export const authStorage: AuthStorage;
```

### 2.3 Stockage utilisateur (`userStorage.ts`)

**Objectif** : Gérer la persistance locale des informations utilisateur.

**Fonctionnalités** :
- Stockage des informations de base de l'utilisateur pour un accès rapide
- Mise à jour des données utilisateur
- Suppression des données utilisateur lors de la déconnexion

**Interface** :
```typescript
interface UserStorage {
  storeUser: (user: User) => Promise<void>;
  getUser: () => Promise<User | null>;
  updateUser: (userData: Partial<User>) => Promise<User | null>;
  clearUser: () => Promise<void>;
}

export const userStorage: UserStorage;
```

### 2.4 Stockage des préférences (`preferencesStorage.ts`)

**Objectif** : Gérer les préférences et paramètres de l'application pour l'utilisateur.

**Fonctionnalités** :
- Stockage et récupération des préférences (thème, notifications, etc.)
- Paramètres spécifiques à l'utilisateur (rayon de recherche, filtres, etc.)

**Interface** :
```typescript
export enum StorageKeys {
  THEME = 'theme',
  NOTIFICATION_SETTINGS = 'notification_settings',
  SEARCH_RADIUS = 'search_radius',
  LAST_VIEWED_LISTINGS = 'last_viewed_listings',
  // Autres clés à ajouter selon les besoins
}

interface PreferencesStorage {
  setItem: <T>(key: StorageKeys, value: T) => Promise<void>;
  getItem: <T>(key: StorageKeys, defaultValue?: T) => Promise<T | null>;
  removeItem: (key: StorageKeys) => Promise<void>;
  clear: () => Promise<void>;
}

export const preferencesStorage: PreferencesStorage;
```

### 2.5 Service d'export central (`index.ts`)

**Objectif** : Centraliser l'export de tous les services de stockage.

**Contenu** :
```typescript
export * from './authStorage';
export * from './userStorage';
export * from './preferencesStorage';
```

## 3. Intégration entre API et Storage

Les services API et Storage sont conçus pour travailler ensemble :

1. **Authentification** :
   - Après login/register réussi, `authService` utilise `authStorage` pour stocker les tokens
   - Les intercepteurs utilisent `authStorage` pour récupérer les tokens pour les requêtes

2. **Gestion des sessions** :
   - Les intercepteurs détectent les tokens expirés et utilisent `authService.refreshToken`
   - En cas d'échec de rafraîchissement, l'utilisateur est redirigé vers la page de login

3. **Mise en cache** :
   - Certaines données (profil utilisateur) peuvent être mises en cache dans `userStorage` pour un accès rapide
   - Ces données sont synchronisées avec le backend lors des opérations CRUD

## 4. Gestion des erreurs

La gestion des erreurs est uniforme dans tous les services :

1. **Normalisation** : Toutes les erreurs API sont normalisées via `normalizeApiError`
2. **Remontée d'erreur** : Les erreurs sont propagées aux composants pour affichage
3. **Réessai automatique** : Certaines opérations (refresh token) peuvent être réessayées automatiquement
4. **Fallback** : En cas d'indisponibilité du serveur, certaines opérations peuvent utiliser les données locales

## 5. Sécurité

Les services implémentent plusieurs mesures de sécurité :

1. **Stockage sécurisé** : Tokens stockés avec Expo SecureStore (chiffré)
2. **Données sensibles** : Aucune donnée sensible (mot de passe, etc.) n'est stockée localement
3. **Expiration des tokens** : Respect des délais d'expiration définis par le backend
4. **Nettoyage** : Suppression complète des données sensibles lors de la déconnexion

## 6. Implémentation

L'implémentation des services sera réalisée en suivant ces étapes :

1. **Constants** : Définir les constantes API (URL, timeout, etc.)
2. **Client** : Configurer le client Axios de base
3. **Storage** : Implémenter les services de stockage (tokens, utilisateur, préférences)
4. **Interceptors** : Mettre en place les intercepteurs pour la gestion des tokens
5. **Auth Service** : Implémenter le service d'authentification
6. **User Service** : Implémenter le service utilisateur
7. **Intégration** : Connecter tous les services ensemble et tester
8. **Services additionnels** : Ajouter progressivement les autres services API au fur et à mesure du développement

## 7. Considérations futures

Plusieurs améliorations pourront être envisagées à l'avenir :

1. **Mise en cache avancée** : Implémentation d'un système de cache plus sophistiqué pour les requêtes fréquentes
2. **Mode hors-ligne** : Support limité des fonctionnalités en mode hors-ligne
3. **Synchronisation en arrière-plan** : Synchronisation des données lorsque l'application est en arrière-plan
4. **Compression** : Compression des données pour optimiser la bande passante
5. **Telemetry** : Mesures de performance des API pour diagnostiquer les problèmes