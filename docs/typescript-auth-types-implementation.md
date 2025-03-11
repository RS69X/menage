# Implémentation des Types TypeScript pour l'authentification frontend

## 1. Introduction

Ce document décrit l'implémentation des types TypeScript pour le module d'authentification du frontend. L'objectif principal était de créer une structure de types robuste et cohérente pour:
- Garantir la sécurité de type entre le frontend et le backend
- Faciliter la gestion des différents types d'utilisateurs (hébergeurs et professionnels de ménage)
- Fournir un support de type complet pour les formulaires multi-étapes
- Assurer une validation stricte des données
- Permettre un typage fort des composants UI pour améliorer la maintenance et la réutilisabilité

## 2. Organisation des dossiers

La structure suivante a été implémentée pour organiser les types TypeScript liés à l'authentification:

```
frontend/src/types/
├── index.ts               # Point d'entrée central pour tous les types
│
├── models/                # Types reflétant les modèles de données
│   ├── user.ts            # Types des utilisateurs (User, Host, Cleaner)
│   ├── auth.ts            # Types liés à l'authentification (tokens, état)
│   └── index.ts           # Export centralisé des modèles
│
├── api/                   # Types pour l'API
│   ├── requests/          # Types des requêtes
│   │   ├── auth.ts        # Requêtes d'authentification
│   │   └── index.ts       # Export centralisé des requêtes
│   ├── responses/         # Types des réponses
│   │   ├── auth.ts        # Réponses d'authentification
│   │   └── index.ts       # Export centralisé des réponses
│   └── index.ts           # Export centralisé de l'API
│
├── components/            # Types pour les props des composants UI
│   ├── auth/              # Types des composants d'authentification
│   │   ├── welcome-screen.types.ts # Types pour l'écran d'accueil
│   │   └── index.ts       # Export centralisé des types de composants auth
│   ├── common/            # Types des composants communs
│   └── index.ts           # Export centralisé des types de composants
│
├── forms/                 # Types pour les formulaires frontend
│   ├── auth/              # Formulaires d'authentification
│   │   ├── login.ts       # Formulaire de connexion
│   │   ├── host.ts        # Formulaire d'inscription hébergeur
│   │   ├── cleaner.ts     # Formulaire d'inscription professionnel
│   │   ├── validation.ts  # Schémas de validation Yup
│   │   └── index.ts       # Export centralisé des formulaires d'auth
│   └── index.ts           # Export centralisé des formulaires
│
└── utils/                 # Utilitaires liés aux types
    ├── typeGuards.ts      # Fonctions de type guards (isHost, isCleaner, etc.)
    └── index.ts           # Export centralisé des utilitaires
```

Cette structure présente plusieurs avantages:
- Séparation claire des responsabilités
- Organisation modulaire facilitant l'extension future
- Système d'imports simplifié grâce aux fichiers d'index
- Cohérence avec l'architecture du backend
- Intégration des types de composants UI pour une meilleure expérience de développement

## 3. Types implémentés

### 3.1 Types de modèles de base

#### `models/user.ts`
```typescript
/**
 * Types représentant les utilisateurs de l'application
 */

/**
 * Énumération des types d'utilisateurs
 */
export enum UserType {
  HOST = 'host',
  CLEANER = 'cleaner',
}

/**
 * Interface utilisateur de base contenant les propriétés communes
 */
export interface BaseUser {
  id: string;
  email: string;
  firstName: string;
  lastName: string;
  phoneNumber: string;
  userType: UserType;
  emailVerified: boolean;
  address: string;
  addressComplement?: string;
  city: string;
  postalCode: string;
  createdAt: string;
  updatedAt: string;
}

/**
 * Interface spécifique aux hébergeurs
 * Étend BaseUser avec des propriétés spécifiques aux hébergeurs
 */
export interface Host extends BaseUser {
  userType: UserType.HOST;
  companyName?: string;
  siret?: string;
}

/**
 * Interface spécifique aux professionnels de ménage
 * Étend BaseUser avec des propriétés spécifiques aux professionnels
 */
export interface Cleaner extends BaseUser {
  userType: UserType.CLEANER;
  siret: string;
  isCompany: boolean;
  companyName?: string;
  workRadius: number;
  // Les coordonnées bancaires ne sont pas exposées au frontend
}

/**
 * Type d'union représentant n'importe quel type d'utilisateur
 */
export type User = Host | Cleaner;
```

#### `models/auth.ts`
```typescript
/**
 * Types relatifs à l'authentification
 */

import { User } from './user';

/**
 * Structure des tokens d'authentification
 */
export interface AuthTokens {
  accessToken: string;
  refreshToken: string;
  expiresIn: number;
}

/**
 * Types d'erreurs d'authentification
 */
export enum AuthErrorType {
  INVALID_CREDENTIALS = 'INVALID_CREDENTIALS',
  ACCOUNT_DISABLED = 'ACCOUNT_DISABLED',
  EMAIL_NOT_VERIFIED = 'EMAIL_NOT_VERIFIED',
  TOKEN_EXPIRED = 'TOKEN_EXPIRED',
  UNAUTHORIZED = 'UNAUTHORIZED',
  UNKNOWN = 'UNKNOWN',
}

/**
 * Structure des erreurs d'authentification
 */
export interface AuthError {
  type: AuthErrorType;
  message: string;
  details?: string;
}

/**
 * État d'authentification pour le store
 */
export interface AuthState {
  isAuthenticated: boolean;
  user: User | null;
  tokens: AuthTokens | null;
  loading: boolean;
  error: AuthError | null;
}
```

### 3.2 Types d'API

#### `api/requests/auth.ts`
```typescript
/**
 * Types de requêtes pour l'API d'authentification
 */

/**
 * Requête de connexion
 */
export interface LoginRequest {
  email: string;
  password: string;
  rememberMe?: boolean;
}

/**
 * Requête d'inscription pour un hébergeur
 */
export interface RegisterHostRequest {
  // Informations personnelles
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
  
  // Informations professionnelles (optionnelles pour les hébergeurs)
  companyName?: string;
  siret?: string;
  
  // Adresse
  address: string;
  addressComplement?: string;
  city: string;
  postalCode: string;
  
  // Sécurité
  password: string;
  
  // Accord avec les conditions
  acceptTerms: boolean;
}

/**
 * Requête d'inscription pour un professionnel de ménage
 */
export interface RegisterCleanerRequest {
  // Informations personnelles
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
  
  // Informations professionnelles (obligatoires pour les professionnels)
  siret: string;
  isCompany: boolean;
  companyName?: string; // Obligatoire si isCompany est true
  
  // Adresse
  address: string;
  addressComplement?: string;
  city: string;
  postalCode: string;
  
  // Préférences de travail
  workRadius: number;
  
  // Informations bancaires
  iban: string;
  bic: string;
  
  // Sécurité
  password: string;
  
  // Accord avec les conditions
  acceptTerms: boolean;
}

/**
 * Autres requêtes d'authentification
 */
export interface ForgotPasswordRequest {
  email: string;
}

export interface ResetPasswordRequest {
  token: string;
  password: string;
  confirmPassword: string;
}

export interface RefreshTokenRequest {
  refreshToken: string;
}

export interface VerifyEmailRequest {
  token: string;
}

export interface LogoutRequest {
  refreshToken: string;
}
```

#### `api/responses/auth.ts`
```typescript
/**
 * Types de réponses pour l'API d'authentification
 */

import { User } from '../../models/user';
import { AuthTokens } from '../../models/auth';

/**
 * Réponse standard d'authentification
 * Retournée lors de la connexion et de l'inscription réussies
 */
export interface AuthResponse {
  user: User;
  tokens: AuthTokens;
}

/**
 * Réponse simple avec un message
 */
export interface MessageResponse {
  message: string;
  success: boolean;
}

/**
 * Réponse de vérification d'email
 */
export interface EmailVerificationResponse {
  verified: boolean;
  message: string;
}

/**
 * Réponse de rafraîchissement de token
 */
export interface RefreshTokenResponse {
  tokens: AuthTokens;
}

/**
 * Structure standardisée des erreurs API
 */
export interface ApiErrorResponse {
  statusCode: number;
  message: string | string[];
  error?: string;
  timestamp?: string;
  path?: string;
}

/**
 * Réponse de validation de formulaire
 */
export interface ValidationErrorResponse extends ApiErrorResponse {
  validationErrors?: Record<string, string[]>;
}
```

### 3.3 Types de formulaires multi-étapes

#### `forms/auth/login.ts`
```typescript
/**
 * Types pour le formulaire de connexion
 */

/**
 * Interface pour le formulaire de connexion
 */
export interface LoginForm {
  email: string;
  password: string;
  rememberMe: boolean; // On définit avec une valeur par défaut (false) dans le formulaire
}

/**
 * État du formulaire de connexion
 */
export interface LoginFormState {
  isSubmitting: boolean;
  isSuccess: boolean;
  error: string | null;
}
```

#### `forms/auth/host.ts`
```typescript
/**
 * Types pour le formulaire d'inscription hébergeur
 */

/**
 * Étape 1: Informations personnelles
 */
export interface HostPersonalInfoForm {
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
}

/**
 * Étape 2: Informations professionnelles
 */
export interface HostProfessionalInfoForm {
  companyName?: string;
  siret?: string;
}

/**
 * Étape 3: Adresse complète
 */
export interface HostAddressForm {
  address: string;
  addressComplement?: string;
  city: string;
  postalCode: string;
}

/**
 * Étape 4: Création de mot de passe
 */
export interface HostPasswordForm {
  password: string;
  confirmPassword: string;
}

/**
 * Étape 5: Acceptation des conditions
 */
export interface HostTermsForm {
  acceptTerms: boolean;
  privacyPolicyAccepted: boolean;
}

/**
 * Formulaire complet d'inscription hébergeur
 */
export interface HostRegistrationForm extends 
  HostPersonalInfoForm,
  HostProfessionalInfoForm,
  HostAddressForm,
  Omit<HostPasswordForm, 'confirmPassword'>,
  HostTermsForm {}

/**
 * Énumération des étapes du formulaire d'inscription hébergeur
 */
export enum HostRegistrationStep {
  PERSONAL_INFO = 0,
  PROFESSIONAL_INFO = 1,
  ADDRESS = 2,
  PASSWORD = 3,
  TERMS = 4
}

/**
 * État du formulaire d'inscription hébergeur
 */
export interface HostRegistrationState {
  currentStep: HostRegistrationStep;
  formData: Partial<HostRegistrationForm>;
  isSubmitting: boolean;
  isSuccess: boolean;
  error: string | null;
}
```

#### `forms/auth/cleaner.ts`
```typescript
/**
 * Types pour le formulaire d'inscription professionnel de ménage
 */

/**
 * Étape 1: Informations personnelles
 */
export interface CleanerPersonalInfoForm {
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
}

/**
 * Étape 2: Informations professionnelles
 */
export interface CleanerProfessionalInfoForm {
  siret: string;
  isCompany: boolean;
  companyName?: string; // Obligatoire si isCompany est true
}

/**
 * Étape 3: Adresse complète
 */
export interface CleanerAddressForm {
  address: string;
  addressComplement?: string;
  city: string;
  postalCode: string;
}

/**
 * Étape 4: Préférences de travail
 */
export interface CleanerWorkPreferencesForm {
  workRadius: number; // Rayon de déplacement en km
}

/**
 * Étape 5: Informations bancaires
 */
export interface CleanerBankInfoForm {
  iban: string;
  bic: string;
}

/**
 * Étape 6: Création de mot de passe
 */
export interface CleanerPasswordForm {
  password: string;
  confirmPassword: string;
}

/**
 * Étape 7: Acceptation des conditions
 */
export interface CleanerTermsForm {
  acceptTerms: boolean;
  privacyPolicyAccepted: boolean;
  dataProcessingAccepted: boolean; // Consentement spécifique pour les données bancaires
}

/**
 * Formulaire complet d'inscription professionnel de ménage
 */
export interface CleanerRegistrationForm extends 
  CleanerPersonalInfoForm,
  CleanerProfessionalInfoForm,
  CleanerAddressForm,
  CleanerWorkPreferencesForm,
  CleanerBankInfoForm,
  Omit<CleanerPasswordForm, 'confirmPassword'>,
  CleanerTermsForm {}

/**
 * Énumération des étapes du formulaire d'inscription professionnel
 */
export enum CleanerRegistrationStep {
  PERSONAL_INFO = 0,
  PROFESSIONAL_INFO = 1,
  ADDRESS = 2,
  WORK_PREFERENCES = 3,
  BANK_INFO = 4,
  PASSWORD = 5,
  TERMS = 6
}

/**
 * État du formulaire d'inscription professionnel
 */
export interface CleanerRegistrationState {
  currentStep: CleanerRegistrationStep;
  formData: Partial<CleanerRegistrationForm>;
  isSubmitting: boolean;
  isSuccess: boolean;
  error: string | null;
}
```

### 3.4 Schémas de validation Yup

Les schémas de validation sont définis dans `forms/auth/validation.ts`. Voici un extrait des validations principales:

```typescript
// Validation de l'email
export const emailSchema = Yup.string()
  .required('L\'email est requis')
  .email('Format d\'email invalide')
  .lowercase()
  .trim();

// Validation du mot de passe
export const passwordSchema = Yup.string()
  .required('Le mot de passe est requis')
  .min(8, 'Le mot de passe doit contenir au moins 8 caractères')
  .matches(/[A-Z]/, 'Le mot de passe doit contenir au moins une majuscule')
  .matches(/[a-z]/, 'Le mot de passe doit contenir au moins une minuscule')
  .matches(/[0-9]/, 'Le mot de passe doit contenir au moins un chiffre')
  .matches(/[^A-Za-z0-9]/, 'Le mot de passe doit contenir au moins un caractère spécial');

// Schéma pour le formulaire de connexion
export const loginSchema = Yup.object().shape({
  email: emailSchema,
  password: Yup.string().required('Le mot de passe est requis'),
  rememberMe: Yup.boolean(),
});

// Schémas pour les étapes des formulaires d'inscription
export const hostPersonalInfoSchema = Yup.object().shape({
  firstName: nameSchema,
  lastName: nameSchema,
  email: emailSchema,
  phoneNumber: phoneSchema,
});

// ... autres schémas pour chaque étape
```

### 3.5 Type Guards et utilitaires

#### `utils/typeGuards.ts`
```typescript
/**
 * Utilitaires de type guards pour l'authentification
 */

import { User, UserType, Host, Cleaner } from '../models/user';
import { AuthError, AuthErrorType } from '../models/auth';

/**
 * Vérifie si un utilisateur est un hébergeur
 */
export function isHost(user: User): user is Host {
  return user.userType === UserType.HOST;
}

/**
 * Vérifie si un utilisateur est un professionnel de ménage
 */
export function isCleaner(user: User): user is Cleaner {
  return user.userType === UserType.CLEANER;
}

/**
 * Vérifie si une erreur est de type AuthError
 */
export function isAuthError(error: any): error is AuthError {
  return (
    error &&
    typeof error === 'object' &&
    'type' in error &&
    'message' in error &&
    Object.values(AuthErrorType).includes(error.type as AuthErrorType)
  );
}

/**
 * Vérifie si un objet possède toutes les propriétés requises d'un utilisateur
 */
export function isValidUser(obj: any): obj is User {
  return (
    obj &&
    typeof obj === 'object' &&
    'id' in obj &&
    'email' in obj &&
    'firstName' in obj &&
    'lastName' in obj &&
    'userType' in obj &&
    (obj.userType === UserType.HOST || obj.userType === UserType.CLEANER)
  );
}

/**
 * Vérifie si l'objet est un utilisateur avec des informations complètes
 */
export function isCompleteUserProfile(user: User): boolean {
  // Vérification des champs communs requis
  const hasCommonRequiredFields =
    !!user.firstName &&
    !!user.lastName &&
    !!user.email &&
    !!user.phoneNumber &&
    !!user.address &&
    !!user.city &&
    !!user.postalCode;

  if (!hasCommonRequiredFields) return false;

  // Vérification spécifique au type d'utilisateur
  if (isHost(user)) {
    // Pour les hébergeurs, aucun champ supplémentaire obligatoire
    return true;
  } else if (isCleaner(user)) {
    // Pour les professionnels, vérifier les champs spécifiques obligatoires
    return (
      !!user.siret &&
      typeof user.workRadius === 'number' &&
      // Si c'est une entreprise, le nom de l'entreprise doit être défini
      (!user.isCompany || !!user.companyName)
    );
  }

  return false;
}
```

### 3.6 Types de composants UI

Les types pour les composants UI sont définis dans le dossier `components/`. Ces types permettent de créer des composants React fortement typés, ce qui améliore la maintenabilité et permet de documenter clairement les props acceptées.

#### `components/auth/welcome-screen.types.ts`

Ce fichier contient les interfaces pour les props des composants de l'écran d'accueil, qui est la première interface que voit l'utilisateur:

```typescript
/**
 * Types pour les composants de l'écran d'accueil
 */
import { UserType } from '../../models/user';

/**
 * Props pour le composant WelcomeHeader
 */
export interface WelcomeHeaderProps {
  /**
   * Taille du logo en pixels
   * @default 80
   */
  logoSize?: number;
}

/**
 * Props pour le composant AppDescription
 */
export interface AppDescriptionProps {
  /**
   * Titre principal affiché au-dessus de la description
   */
  title: string;
  
  /**
   * Texte de description expliquant le concept de l'application
   */
  description: string;
}

/**
 * Props pour le composant UserTypeCard
 */
export interface UserTypeCardProps {
  /**
   * Type d'utilisateur (hébergeur ou professionnel)
   */
  type: UserType;
  
  /**
   * Titre affiché sur la carte
   */
  title: string;
  
  /**
   * Description du type d'utilisateur
   */
  description: string;
  
  /**
   * Nom de l'icône à afficher (utilise @expo/vector-icons/Feather)
   */
  icon: string;
  
  /**
   * Fonction appelée lorsque l'utilisateur appuie sur la carte
   */
  onPress: () => void;
}

/**
 * Props pour le composant UserTypeSelection
 */
export interface UserTypeSelectionProps {
  /**
   * Fonction appelée lorsque l'utilisateur sélectionne un type d'utilisateur
   * @param type Type d'utilisateur sélectionné
   */
  onSelectUserType: (type: UserType) => void;
}

/**
 * Props pour le composant AuthNavigationLink
 */
export interface AuthNavigationLinkProps {
  /**
   * Fonction appelée lorsque l'utilisateur appuie sur le lien de connexion
   */
  onPress: () => void;
}

/**
 * Props pour le composant FooterLinks
 */
export interface FooterLinksProps {
  /**
   * Fonction appelée lorsque l'utilisateur appuie sur le lien des conditions d'utilisation
   */
  onPressTerms: () => void;
  
  /**
   * Fonction appelée lorsque l'utilisateur appuie sur le lien de politique de confidentialité
   */
  onPressPrivacy: () => void;
}
```

Cette approche de typage pour les composants UI présente plusieurs avantages:
- Documentation claire des props acceptées par chaque composant
- Autocomplétion et validation de type dans les éditeurs de code
- Meilleure maintenabilité grâce à des interfaces explicites
- Réutilisabilité simplifiée des composants
- Intégration avec les types de modèles (comme UserType) pour une cohérence globale

## 4. Système d'exportation avec fichiers d'index

Pour faciliter l'utilisation des types, une hiérarchie d'exports a été mise en place avec des fichiers d'index à chaque niveau:

```typescript
// models/index.ts
export * from './user';
export * from './auth';

// api/requests/index.ts
export * from './auth';

// api/responses/index.ts
export * from './auth';

// api/index.ts
export * from './requests';
export * from './responses';

// components/auth/index.ts
export * from './welcome-screen.types';

// components/index.ts
export * from './auth';

// forms/auth/index.ts
export * from './login';
export * from './host';
export * from './cleaner';
export * from './validation';

// forms/index.ts
export * from './auth';

// utils/index.ts
export * from './typeGuards';

// types/index.ts (fichier racine)
export * from './models';
export * from './api';
export * from './components';
export * from './forms';
export * from './utils';
```

Cette structure permet d'utiliser des imports concis dans tout le projet:

```typescript
// Au lieu de:
import { User } from '@/types/models/user';
import { LoginForm } from '@/types/forms/auth/login';
import { isHost } from '@/types/utils/typeGuards';
import { WelcomeHeaderProps } from '@/types/components/auth/welcome-screen.types';

// On peut simplement faire:
import { User, LoginForm, isHost, WelcomeHeaderProps } from '@/types';
```

## 5. Utilisation des types dans l'application

### 5.1 Utilisation avec React Hook Form

```typescript
import { useForm } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';
import { LoginForm, loginSchema } from '@/types';

const LoginScreen = () => {
  const { control, handleSubmit, errors } = useForm<LoginForm>({
    resolver: yupResolver(loginSchema),
    defaultValues: {
      email: '',
      password: '',
      rememberMe: false
    }
  });

  // ...
};
```

### 5.2 Utilisation des type guards

```typescript
import { User, isHost, isCleaner } from '@/types';

const UserDashboard = ({ user }: { user: User }) => {
  if (isHost(user)) {
    return <HostDashboard host={user} />; // TypeScript sait que user est de type Host
  } else if (isCleaner(user)) {
    return <CleanerDashboard cleaner={user} />; // TypeScript sait que user est de type Cleaner
  }
  
  return <div>Type d'utilisateur non reconnu</div>;
};
```

### 5.3 Utilisation avec les services API

```typescript
import { LoginRequest, AuthResponse } from '@/types';
import axios from 'axios';

const login = async (data: LoginRequest): Promise<AuthResponse> => {
  const response = await axios.post('/api/auth/login', data);
  return response.data;
};
```

### 5.4 Gestion des formulaires multi-étapes

```typescript
import { useState } from 'react';
import { 
  HostRegistrationForm, 
  HostRegistrationStep, 
  HostPersonalInfoForm,
  hostPersonalInfoSchema
} from '@/types';

const RegisterHostScreen = () => {
  const [currentStep, setCurrentStep] = useState<HostRegistrationStep>(HostRegistrationStep.PERSONAL_INFO);
  const [formData, setFormData] = useState<Partial<HostRegistrationForm>>({});
  
  // Gestionnaire pour l'étape 1
  const handlePersonalInfoSubmit = (data: HostPersonalInfoForm) => {
    setFormData(prev => ({ ...prev, ...data }));
    setCurrentStep(HostRegistrationStep.PROFESSIONAL_INFO);
  };
  
  // Rendu conditionnel selon l'étape
  switch (currentStep) {
    case HostRegistrationStep.PERSONAL_INFO:
      return <PersonalInfoStep onSubmit={handlePersonalInfoSubmit} initialData={formData} />;
    case HostRegistrationStep.PROFESSIONAL_INFO:
      return <ProfessionalInfoStep onSubmit={...} initialData={formData} />;
    // ...autres étapes
  }
};
```

### 5.5 Utilisation des types de composants UI

Les interfaces de composants sont utilisées dans les définitions de composants React pour assurer le typage des props:

```typescript
import React from 'react';
import { View, Image, Text, StyleSheet } from 'react-native';
import { COLORS, TYPOGRAPHY, SPACING } from '@/constants/theme';
import { WelcomeHeaderProps } from '@/types';

/**
 * Composant d'en-tête pour l'écran d'accueil
 */
const WelcomeHeader: React.FC<WelcomeHeaderProps> = ({ 
  logoSize = 80 // Valeur par défaut selon la documentation
}) => {
  return (
    <View style={styles.container}>
      <Image
        source={require('@/assets/images/logo.png')}
        style={[styles.logo, { width: logoSize, height: logoSize }]}
        resizeMode="contain"
      />
      <Text style={styles.appName}>Ménage Connect</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    alignItems: 'center',
    paddingVertical: SPACING.lg,
  },
  logo: {
    marginBottom: SPACING.md,
  },
  appName: {
    fontSize: TYPOGRAPHY.size.xxxl,
    fontWeight: TYPOGRAPHY.weight.bold,
    color: COLORS.primary,
  },
});

export default WelcomeHeader;
```

Pour les composants qui utilisent le type d'utilisateur pour une différenciation visuelle:

```typescript
import React from 'react';
import { View, Text, StyleSheet, Pressable } from 'react-native';
import { Feather } from '@expo/vector-icons';
import Animated, { 
  useSharedValue, 
  useAnimatedStyle,
  withTiming,
  Easing
} from 'react-native-reanimated';
import { COLORS, TYPOGRAPHY, SPACING, BORDER_RADIUS } from '@/constants/theme';
import { UserTypeCardProps } from '@/types';
import { UserType } from '@/types/models/user';

const UserTypeCard: React.FC<UserTypeCardProps> = ({
  type,
  title,
  description,
  icon,
  onPress,
}) => {
  // Animation values
  const scale = useSharedValue(1);
  
  // Card color based on user type - différenciation visuelle selon le type
  const cardColor = type === UserType.HOST ? COLORS.primary : COLORS.secondary;
  
  // ... reste du composant
};
```

## 6. Bonnes pratiques et recommandations

### 6.1 Utilisation cohérente des types

- Privilégier les imports depuis le point d'entrée central (`@/types`) pour la lisibilité
- Toujours utiliser les schémas de validation Yup avec React Hook Form
- Utiliser les type guards pour manipuler de manière sécurisée les différents types d'utilisateurs
- Documenter les interfaces avec des commentaires JSDoc, en particulier pour les props optionnelles et leurs valeurs par défaut

### 6.2 Conventions pour les types de composants UI

- Nommer les interfaces de props avec le suffixe `Props` (ex: `WelcomeHeaderProps`)
- Documenter chaque prop avec des commentaires JSDoc
- Indiquer explicitement les valeurs par défaut dans la documentation (@default)
- Utiliser des types optionnels (?) pour les props non obligatoires
- Intégrer les types de modèles (comme UserType) quand un composant doit s'adapter à différents types d'utilisateurs

### 6.3 Extensions futures

Le système est conçu pour être facilement extensible:
- Ajout de nouveaux types de requêtes/réponses dans `api/requests/` et `api/responses/`
- Ajout de nouveaux formulaires dans `forms/`
- Extension des type guards dans `utils/typeGuards.ts` selon les besoins
- Ajout de nouveaux types de composants pour d'autres parties de l'interface utilisateur

### 6.4 Vigilance sur les modifications de type

- Toute modification des types `User`, `Host` ou `Cleaner` doit être cohérente avec le backend
- Les modifications des types de formulaires doivent s'accompagner de mises à jour des schémas de validation
- Les ajouts de champs obligatoires doivent être propagés dans tous les types concernés
- Les modifications des types de composants UI doivent être reflétées dans l'implémentation des composants

## 7. Conclusion

Cette implémentation fournit une base solide pour le développement du module d'authentification du frontend, en garantissant:
- Une correspondance stricte avec le backend
- Une validation cohérente des données
- Une gestion type-safe des différents profils d'utilisateurs
- Un support robuste pour les formulaires multi-étapes
- Un typage fort des composants UI pour une meilleure maintenabilité et expérience de développement

L'organisation modulaire facilite également la maintenance et l'extension futures du système de types, tout en permettant une utilisation simple et intuitive par les développeurs.

## Références

- [SPECS.md](../SPECS.md) - Spécifications détaillées du projet
- [docs/welcome-screen-types.md](welcome-screen-types.md) - Documentation spécifique des interfaces TypeScript pour l'écran d'accueil
- [docs/welcome-screen-implementation-plan.md](welcome-screen-implementation-plan.md) - Plan d'implémentation détaillé pour l'écran d'accueil