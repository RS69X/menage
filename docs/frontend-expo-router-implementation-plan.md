# Plan d'implémentation du frontend avec Expo Router

## Introduction

Ce document présente le plan d'implémentation du frontend de l'application de mise en relation entre professionnels de ménage et hébergeurs, en utilisant **Expo Router** comme système de navigation. Ce choix diffère de la structure initialement décrite dans le README.md (qui était basée sur React Navigation), mais offre des avantages significatifs en termes de simplicité et de maintenabilité.

## Architecture générale

### Expo Router vs. React Navigation

Expo Router est un système de routage basé sur le système de fichiers, similaire à Next.js ou React Router 6.4+. Contrairement à React Navigation qui utilise une configuration déclarative centralisée, Expo Router suit une approche où **la structure de dossiers définit la navigation**.

#### Principaux avantages

- **URLs natives** : URLs partagables sur iOS, Android et Web
- **Simplicité** : La structure des fichiers reflète la navigation
- **Performance** : Chargement efficace des écrans et navigation fluide
- **Navigation imbriquée** : Facilement gérée par la hiérarchie des dossiers
- **Compatibilité TypeScript** : Support complet pour le typage

### Architecture hybride

Notre architecture sera hybride :
- Les **routes/écrans** seront définis dans le dossier `app/` (pattern d'Expo Router)
- Les **composants réutilisables**, **services**, et autres éléments seront dans `src/`

## Structure détaillée des dossiers

### Structure du dossier `app/` (Expo Router)

```
app/
├── (auth)/             # Groupe pour les routes d'authentification
│   ├── login.tsx       # Écran de connexion
│   ├── register/       # Sous-routes pour l'inscription
│   │   ├── host.tsx    # Inscription hébergeur
│   │   ├── cleaner.tsx # Inscription professionnel
│   │   └── _layout.tsx # Layout pour les écrans d'inscription
│   ├── forgot-password.tsx
│   ├── reset-password.tsx
│   ├── verify-email.tsx
│   └── _layout.tsx     # Layout commun pour l'authentification
├── (host)/             # Groupe pour le tableau de bord hébergeur
│   ├── _layout.tsx     # Layout avec TabNavigator
│   ├── index.tsx       # Tab: Accueil/Tableau de bord principal
│   ├── listings/       # Tab: Annonces publiées
│   │   ├── index.tsx   # Liste des annonces
│   │   ├── [id].tsx    # Détail d'une annonce
│   │   ├── create.tsx  # Création d'annonce
│   │   └── _layout.tsx # Layout pour les annonces
│   ├── messages.tsx    # Tab: Messages
│   ├── notifications.tsx # Tab: Notifications
│   └── profile.tsx     # Tab: Profil
├── (cleaner)/          # Groupe pour le tableau de bord professionnel
│   ├── _layout.tsx     # Layout avec TabNavigator
│   ├── index.tsx       # Tab: Recherche d'annonces (swipe)
│   ├── bookings/       # Tab: Réservations/Planning
│   │   ├── index.tsx   # Liste des réservations
│   │   ├── [id].tsx    # Détail d'une réservation
│   │   └── _layout.tsx # Layout pour les réservations
│   ├── messages.tsx    # Tab: Messages
│   ├── notifications.tsx # Tab: Notifications
│   ├── profile.tsx     # Tab: Profil
│   └── invoices.tsx    # Tab: Facturation
├── index.tsx           # Écran de bienvenue/choix du type d'utilisateur
└── _layout.tsx         # Layout racine avec initialisation d'auth
```

#### Notes sur cette structure

- **Groupes de routes** : Les parenthèses `()` dans les noms de dossiers créent des groupes de routes qui ne sont pas inclus dans l'URL, mais servent à organiser la navigation.
- **Fichiers `_layout.tsx`** : Définissent le layout pour tous les écrans de leur dossier/groupe.
- **Paramètres dynamiques** : Les crochets `[]` dans les noms de fichiers définissent des routes dynamiques (par ex. `[id].tsx`).

### Structure du dossier `src/` (Composants et services)

```
src/
├── components/         # Composants réutilisables
│   ├── auth/           # Composants pour l'authentification
│   │   ├── LoginForm.tsx
│   │   ├── RegisterHostForm.tsx
│   │   ├── RegisterCleanerForm.tsx
│   │   └── ...
│   ├── common/         # Composants partagés
│   │   ├── Button.tsx
│   │   ├── Card.tsx
│   │   ├── FormField.tsx
│   │   └── ...
│   ├── host/           # Composants spécifiques aux hébergeurs
│   │   ├── ListingCard.tsx
│   │   ├── ListingForm.tsx
│   │   └── ...
│   └── cleaner/        # Composants spécifiques aux professionnels
│       ├── ProfileCard.tsx
│       ├── BookingItem.tsx
│       └── ...
├── types/              # Types TypeScript
│   ├── models/         # Types des modèles de données
│   │   ├── user.ts
│   │   ├── auth.ts
│   │   └── ...
│   ├── api/            # Types pour l'API
│   │   ├── requests/   # Types des requêtes
│   │   └── responses/  # Types des réponses
│   ├── forms/          # Types pour les formulaires
│   │   └── auth/       # Types pour les formulaires d'auth
│   ├── utils/          # Utilitaires de type
│   └── index.ts        # Exports centralisés
├── services/           # Services (API, stockage, etc.)
│   ├── api/            # Client API et endpoints
│   │   ├── client.ts   # Configuration Axios
│   │   ├── auth.ts     # Endpoints d'authentification
│   │   └── ...
│   └── storage/        # Gestion du stockage local
│       ├── authStorage.ts
│       └── ...
├── store/              # État global avec Zustand
│   ├── auth.ts         # Store d'authentification
│   ├── host.ts         # Store spécifique aux hébergeurs
│   ├── cleaner.ts      # Store spécifique aux professionnels
│   └── ...
├── utils/              # Fonctions utilitaires
│   ├── formatters.ts   # Formatage de dates, montants, etc.
│   ├── validators.ts   # Fonctions de validation
│   └── ...
├── constants/          # Constantes de l'application
│   ├── theme.ts        # Thème (couleurs, typographie)
│   ├── config.ts       # Configuration générale
│   └── ...
├── styles/             # Styles partagés
│   ├── common.ts       # Styles communs
│   ├── typography.ts   # Styles de texte
│   └── ...
└── hooks/              # Hooks personnalisés
    ├── useAuth.ts      # Hook d'authentification
    ├── useForm.ts      # Hook pour les formulaires
    └── ...
```

## Conventions de nommage et bonnes pratiques

### Conventions de nommage

- **Fichiers de composants** : PascalCase, export par défaut, ex: `Button.tsx`
- **Fichiers de hooks** : camelCase préfixé par `use`, ex: `useAuth.ts`
- **Fichiers de services, utils, constants** : camelCase, ex: `authService.ts`
- **Types et interfaces** : PascalCase, ex: `UserType`, `AuthResponse`
- **Fichiers de routes (dans app/)** : kebab-case, ex: `forgot-password.tsx` 

### Organisation des imports

Ordre recommandé pour les imports :
1. Imports de librairies externes
2. Imports de types
3. Imports de composants
4. Imports de hooks, utils, services
5. Imports de styles et constantes

```typescript
// Exemple d'organisation des imports
import React, { useState, useEffect } from 'react';
import { View, Text } from 'react-native';
import { router, useLocalSearchParams } from 'expo-router';

import { User, AuthResponse } from '@/types';
import { LoginRequest } from '@/types/api/requests';

import Button from '@/components/common/Button';
import FormField from '@/components/common/FormField';

import { useAuth } from '@/hooks/useAuth';
import { validateEmail } from '@/utils/validators';
import { authService } from '@/services/api/auth';

import { COLORS } from '@/constants/theme';
import styles from './styles';
```

### Utilisation des layouts avec Expo Router

Les fichiers `_layout.tsx` sont fondamentaux dans l'architecture Expo Router :

```typescript
// Exemple de layout pour le tableau de bord hébergeur (app/(host)/_layout.tsx)
import { Tabs } from 'expo-router';
import { FontAwesome5 } from '@expo/vector-icons';

import { useAuth } from '@/hooks/useAuth';
import { COLORS } from '@/constants/theme';

export default function HostLayout() {
  const { user, isAuthenticated } = useAuth();
  
  // Rediriger vers login si non authentifié
  useEffect(() => {
    if (!isAuthenticated) {
      router.replace('/login');
    }
  }, [isAuthenticated]);

  return (
    <Tabs
      screenOptions={{
        tabBarActiveTintColor: COLORS.primary,
        headerShown: false,
      }}
    >
      <Tabs.Screen
        name="index"
        options={{
          title: "Accueil",
          tabBarIcon: ({ color }) => <FontAwesome5 name="home" size={24} color={color} />,
        }}
      />
      <Tabs.Screen
        name="listings"
        options={{
          title: "Mes annonces",
          tabBarIcon: ({ color }) => <FontAwesome5 name="list" size={24} color={color} />,
        }}
      />
      {/* Autres onglets */}
    </Tabs>
  );
}
```

## Gestion d'état avec Zustand

Pour la gestion d'état global, nous utiliserons Zustand comme spécifié dans le README.md :

```typescript
// src/store/auth.ts
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';
import AsyncStorage from '@react-native-async-storage/async-storage';

import { User, AuthTokens } from '@/types';
import { authService } from '@/services/api/auth';
import { authStorage } from '@/services/storage/authStorage';

interface AuthState {
  user: User | null;
  tokens: AuthTokens | null;
  isAuthenticated: boolean;
  isLoading: boolean;
  error: string | null;
  login: (email: string, password: string) => Promise<void>;
  logout: () => Promise<void>;
  // Autres actions
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set, get) => ({
      user: null,
      tokens: null,
      isAuthenticated: false,
      isLoading: false,
      error: null,
      
      login: async (email, password) => {
        set({ isLoading: true, error: null });
        try {
          const response = await authService.login({ email, password });
          set({
            user: response.user,
            tokens: response.tokens,
            isAuthenticated: true,
            isLoading: false,
          });
          await authStorage.storeTokens(response.tokens);
        } catch (error) {
          set({
            error: error.message || 'Échec de connexion',
            isLoading: false,
          });
        }
      },
      
      logout: async () => {
        set({ isLoading: true });
        try {
          await authService.logout();
          await authStorage.clearTokens();
          set({
            user: null,
            tokens: null,
            isAuthenticated: false,
            isLoading: false,
          });
        } catch (error) {
          set({ isLoading: false });
          // Toujours se déconnecter localement même en cas d'erreur API
        }
      },
      
      // Autres actions d'authentification
    }),
    {
      name: 'auth-storage',
      storage: createJSONStorage(() => AsyncStorage),
      // Ne stockez que les informations essentielles, pas les actions
      partialize: (state) => ({
        user: state.user,
        tokens: state.tokens,
        isAuthenticated: state.isAuthenticated,
      }),
    }
  )
);

// Hook personnalisé pour faciliter l'accès au store
export const useAuth = () => useAuthStore();
```

## Formulaires avec React Hook Form et Yup

Pour les formulaires, nous utiliserons React Hook Form avec Yup pour la validation comme indiqué dans le README.md :

```typescript
// src/components/auth/LoginForm.tsx
import React from 'react';
import { View, Text } from 'react-native';
import { useForm, Controller } from 'react-hook-form';
import { yupResolver } from '@hookform/resolvers/yup';

import { LoginForm as LoginFormType } from '@/types/forms/auth';
import { loginSchema } from '@/types/forms/auth/validation';
import { useAuth } from '@/hooks/useAuth';
import FormField from '@/components/common/FormField';
import Button from '@/components/common/Button';

export default function LoginForm() {
  const { login, isLoading, error } = useAuth();
  
  const { control, handleSubmit, formState: { errors } } = useForm<LoginFormType>({
    resolver: yupResolver(loginSchema),
    defaultValues: {
      email: '',
      password: '',
      rememberMe: false
    }
  });
  
  const onSubmit = async (data: LoginFormType) => {
    await login(data.email, data.password);
  };
  
  return (
    <View>
      <Controller
        control={control}
        name="email"
        render={({ field: { onChange, value } }) => (
          <FormField
            label="Email"
            value={value}
            onChangeText={onChange}
            error={errors.email?.message}
            keyboardType="email-address"
            autoCapitalize="none"
          />
        )}
      />
      
      <Controller
        control={control}
        name="password"
        render={({ field: { onChange, value } }) => (
          <FormField
            label="Mot de passe"
            value={value}
            onChangeText={onChange}
            error={errors.password?.message}
            secureTextEntry
          />
        )}
      />
      
      <Controller
        control={control}
        name="rememberMe"
        render={({ field: { onChange, value } }) => (
          <CheckBox
            label="Se souvenir de moi"
            value={value}
            onValueChange={onChange}
          />
        )}
      />
      
      {error && <Text style={styles.errorText}>{error}</Text>}
      
      <Button
        title="Se connecter"
        onPress={handleSubmit(onSubmit)}
        loading={isLoading}
      />
    </View>
  );
}
```

## Services API

Notre configuration API utilisera Axios avec intercepteurs pour gérer les tokens d'authentification :

```typescript
// src/services/api/client.ts
import axios from 'axios';
import { API_URL } from '@/constants/config';
import { authStorage } from '@/services/storage/authStorage';
import { useAuthStore } from '@/store/auth';

const apiClient = axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Intercepteur pour ajouter le token aux requêtes
apiClient.interceptors.request.use(
  async (config) => {
    const tokens = await authStorage.getTokens();
    if (tokens?.accessToken) {
      config.headers.Authorization = `Bearer ${tokens.accessToken}`;
    }
    return config;
  },
  (error) => {
    return Promise.reject(error);
  }
);

// Intercepteur pour gérer les réponses et les erreurs
apiClient.interceptors.response.use(
  (response) => {
    return response;
  },
  async (error) => {
    const originalRequest = error.config;
    
    // Si erreur 401 (Unauthorized) et pas déjà tenté de rafraîchir
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;
      
      try {
        // Tenter de rafraîchir le token
        const tokens = await authStorage.getTokens();
        if (tokens?.refreshToken) {
          const response = await axios.post(`${API_URL}/auth/refresh-token`, {
            refreshToken: tokens.refreshToken,
          });
          
          const newTokens = response.data.tokens;
          await authStorage.storeTokens(newTokens);
          
          // Réessayer la requête originale avec le nouveau token
          originalRequest.headers.Authorization = `Bearer ${newTokens.accessToken}`;
          return axios(originalRequest);
        }
      } catch (refreshError) {
        // Si échec du rafraîchissement, déconnecter l'utilisateur
        useAuthStore.getState().logout();
        return Promise.reject(refreshError);
      }
    }
    
    return Promise.reject(error);
  }
);

export default apiClient;
```

## Étapes d'implémentation

### Phase 1 : Structure de base et configuration

1. **Mettre en place la structure de dossiers**
   - Créer les dossiers manquants selon le plan ci-dessus
   - Configurer les imports absolus (avec le préfixe `@/`)

2. **Configurer les outils de développement**
   - ESLint et Prettier selon les conventions du README
   - Husky pour les hooks pre-commit

3. **Configurer Expo Router**
   - Créer le fichier `_layout.tsx` racine
   - Mettre en place le système de protection des routes

### Phase 2 : Authentification et profils utilisateurs

1. **Développer les services d'authentification**
   - Implémentation d'authService et authStorage
   - Configuration du store Zustand

2. **Créer les composants de formulaires**
   - Composants communs (FormField, Button, etc.)
   - Formulaires spécifiques (LoginForm, RegisterHostForm, etc.)

3. **Mettre en place les écrans d'authentification**
   - Écran d'accueil avec choix du type d'utilisateur
   - Écrans de connexion et d'inscription
   - Écrans de gestion de mot de passe

### Phase 3 : Tableaux de bord

1. **Développer la structure de navigation des tableaux de bord**
   - Configuration des tabs pour hébergeurs
   - Configuration des tabs pour professionnels

2. **Créer les écrans principaux**
   - Tableaux de bord de base pour chaque type d'utilisateur
   - Profil utilisateur

### Phase 4 : Fonctionnalités métier

1. **Hébergeurs : Gestion des annonces**
   - Formulaire de création d'annonce
   - Liste des annonces publiées

2. **Professionnels : Recherche d'annonces**
   - Interface de swipe
   - Détail des annonces

La suite des phases suivra la roadmap globale du projet.

## Conclusion

Cette architecture basée sur Expo Router offre plusieurs avantages :
- **Simplicité** : La structure des fichiers reflète directement la navigation
- **Maintenabilité** : Séparation claire des responsabilités
- **Scalabilité** : Facilité d'ajout de nouvelles routes et fonctionnalités
- **Performance** : Chargement optimisé des écrans

L'utilisation d'une structure hybride nous permet de tirer parti des avantages d'Expo Router tout en maintenant une organisation claire des composants, services et autres éléments dans le dossier `src/`.