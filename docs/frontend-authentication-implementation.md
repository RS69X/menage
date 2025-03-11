# Plan d'implémentation - Interface d'inscription et de connexion

## 1. Vue d'ensemble

Ce document détaille le plan d'implémentation pour les interfaces d'inscription et de connexion de l'application mobile, avec un focus particulier sur l'écran d'accueil. Ces interfaces constituent la porte d'entrée de l'application et doivent offrir une expérience fluide et intuitive tout en gérant distinctement les deux types d'utilisateurs: hébergeurs et professionnels de ménage.

## 2. Écran d'accueil

### 2.1 Objectif et fonctionnalités

L'écran d'accueil sert d'introduction à l'application et permet à l'utilisateur de:
- Découvrir brièvement le service proposé
- Choisir son type de profil (hébergeur ou professionnel de ménage)
- Accéder aux formulaires d'inscription ou de connexion

### 2.2 Structure et composants

#### Layout général
- **En-tête**: Logo de l'application et nom
- **Section principale**: 
  - Présentation succincte de l'application (2-3 phrases)
  - Illustration représentant le concept de mise en relation
- **Section de choix du profil**: 
  - Deux cartes/boutons distincts pour les deux types d'utilisateurs
  - Chaque carte inclut:
    - Icône représentative
    - Titre ("Je suis hébergeur" / "Je suis professionnel de ménage")
    - Courte description du rôle (1 phrase)
- **Section de connexion**:
  - Lien "Déjà inscrit? Se connecter"
- **Pied de page**:
  - Liens vers les conditions d'utilisation et politique de confidentialité

#### Composants spécifiques
1. **WelcomeHeader**: Logo et titre de l'application
2. **AppDescription**: Texte et illustration de présentation
3. **UserTypeSelectionCard**: Composant réutilisable pour les deux types d'utilisateurs
4. **AuthNavigationLink**: Lien vers la connexion pour les utilisateurs existants
5. **FooterLinks**: Liens légaux requis

### 2.3 Design et expérience utilisateur

- **Palette de couleurs**:
  - Couleur primaire pour les hébergeurs (à définir - suggestion: bleu)
  - Couleur secondaire pour les professionnels (à définir - suggestion: vert)
  - Tons neutres pour le fond et les textes

- **Typographie**:
  - Police principale: Sans-serif moderne (compatible avec iOS et Android)
  - Hiérarchie claire des tailles de texte:
    - Titre principal: 24-28px
    - Sous-titres: 18-20px
    - Corps de texte: 14-16px
    - Texte secondaire: 12-14px

- **Animations et transitions**:
  - Animation subtile lors du chargement initial
  - Effet de pression sur les cartes de sélection
  - Transition fluide vers les écrans d'inscription

- **Accessibilité**:
  - Contraste suffisant entre texte et fond
  - Taille de cible tactile d'au moins 44×44 points pour tous les éléments interactifs
  - Support des fonctionnalités d'accessibilité natives (VoiceOver, TalkBack)

### 2.4 Flux utilisateur

1. L'utilisateur ouvre l'application pour la première fois
2. L'écran d'accueil s'affiche avec l'animation d'introduction
3. L'utilisateur peut:
   - Sélectionner "Je suis hébergeur" → Navigation vers l'écran d'inscription hébergeur
   - Sélectionner "Je suis professionnel de ménage" → Navigation vers l'écran d'inscription professionnel
   - Appuyer sur "Déjà inscrit? Se connecter" → Navigation vers l'écran de connexion

## 3. Écrans d'inscription

### 3.1 Inscription hébergeur

#### Champs du formulaire
- Informations personnelles:
  - Prénom (obligatoire)
  - Nom (obligatoire)
  - Email (obligatoire, validation format)
  - Téléphone (obligatoire, validation format)
  - Mot de passe (obligatoire, min 8 caractères, validation complexité)
  - Confirmation du mot de passe

- Informations professionnelles:
  - Nom de société (optionnel)
  - Numéro SIRET (optionnel, validation format)

- Adresse:
  - Adresse (obligatoire)
  - Complément d'adresse (optionnel)
  - Code postal (obligatoire, validation format)
  - Ville (obligatoire)

- Validation:
  - Case à cocher pour acceptation des conditions d'utilisation (obligatoire)
  - Case à cocher pour acceptation de la politique de confidentialité (obligatoire)

#### Structure et navigation
- Formulaire divisé en étapes (steps) pour améliorer l'expérience:
  1. Informations personnelles
  2. Informations professionnelles
  3. Adresse
  4. Validation et confirmation

- Navigation entre les étapes:
  - Bouton "Suivant" pour avancer
  - Bouton "Précédent" pour revenir
  - Indicateur de progression
  - Validation des champs obligatoires avant de passer à l'étape suivante

### 3.2 Inscription professionnel de ménage

#### Champs du formulaire
- Informations personnelles:
  - Prénom (obligatoire)
  - Nom (obligatoire)
  - Email (obligatoire, validation format)
  - Téléphone (obligatoire, validation format)
  - Mot de passe (obligatoire, min 8 caractères, validation complexité)
  - Confirmation du mot de passe

- Informations professionnelles:
  - Statut (auto-entrepreneur, entreprise, etc.) (obligatoire)
  - Nom de société (obligatoire si entreprise)
  - Numéro SIRET (obligatoire, validation format)

- Adresse:
  - Adresse (obligatoire)
  - Complément d'adresse (optionnel)
  - Code postal (obligatoire, validation format)
  - Ville (obligatoire)

- Préférences de travail:
  - Rayon de déplacement en km (obligatoire, valeur par défaut: 10km)

- Informations bancaires:
  - IBAN (obligatoire, validation format)
  - BIC (obligatoire, validation format)

- Validation:
  - Case à cocher pour acceptation des conditions d'utilisation (obligatoire)
  - Case à cocher pour acceptation de la politique de confidentialité (obligatoire)

#### Structure et navigation
- Formulaire divisé en étapes (steps) pour améliorer l'expérience:
  1. Informations personnelles
  2. Informations professionnelles
  3. Adresse
  4. Préférences et informations bancaires
  5. Validation et confirmation

- Navigation entre les étapes:
  - Bouton "Suivant" pour avancer
  - Bouton "Précédent" pour revenir
  - Indicateur de progression
  - Validation des champs obligatoires avant de passer à l'étape suivante

## 4. Écran de connexion

### 4.1 Structure et composants

- **En-tête**: Logo et titre de l'application
- **Formulaire de connexion**:
  - Champ email (obligatoire, validation format)
  - Champ mot de passe (obligatoire)
  - Option "Se souvenir de moi" (case à cocher)
  - Bouton "Se connecter"
  - Lien "Mot de passe oublié?"
- **Navigation secondaire**:
  - Lien "Pas encore inscrit? Créer un compte"
- **Pied de page**:
  - Liens vers les conditions d'utilisation et politique de confidentialité

### 4.2 Fonctionnalités

- Validation des champs avant soumission
- Gestion des erreurs d'authentification (messages d'erreur explicites)
- Stockage sécurisé des identifiants (si "Se souvenir de moi" activé)
- Redirection vers le tableau de bord approprié selon le type d'utilisateur

## 5. Récupération de mot de passe

### 5.1 Écran de demande de réinitialisation

- Champ email (obligatoire, validation format)
- Bouton "Envoyer le lien de réinitialisation"
- Messages de confirmation et d'erreur explicites

### 5.2 Écran de création de nouveau mot de passe

- Champ nouveau mot de passe (obligatoire, validation complexité)
- Champ confirmation du nouveau mot de passe
- Bouton "Réinitialiser le mot de passe"
- Feedback de succès et redirection vers la connexion

## 6. Vérification d'email

### 6.1 Processus de vérification

- Écran de confirmation post-inscription indiquant qu'un email a été envoyé
- Page de redirection après clic sur le lien dans l'email
- Confirmation visuelle de la vérification réussie

## 7. Intégration avec le backend

### 7.1 Endpoints API requis

- `/auth/register/host` : Inscription hébergeur (POST)
- `/auth/register/cleaner` : Inscription professionnel (POST)
- `/auth/login` : Connexion (POST)
- `/auth/refresh-token` : Rafraîchissement du token (POST)
- `/auth/forgot-password` : Demande de réinitialisation de mot de passe (POST)
- `/auth/reset-password` : Réinitialisation du mot de passe (POST)
- `/auth/verify-email/:token` : Vérification d'email (GET)

### 7.2 Structure des requêtes et réponses

#### Inscription hébergeur
```typescript
// Requête
interface RegisterHostRequest {
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
  password: string;
  companyName?: string;
  siret?: string;
  address: string;
  addressComplement?: string;
  postalCode: string;
  city: string;
  termsAccepted: boolean;
  privacyPolicyAccepted: boolean;
}

// Réponse
interface AuthResponse {
  accessToken: string;
  refreshToken: string;
  user: {
    id: string;
    email: string;
    firstName: string;
    lastName: string;
    userType: 'host';
    emailVerified: boolean;
  };
}
```

#### Inscription professionnel
```typescript
// Requête
interface RegisterCleanerRequest {
  firstName: string;
  lastName: string;
  email: string;
  phoneNumber: string;
  password: string;
  businessType: 'individual' | 'company';
  companyName?: string;
  siret: string;
  address: string;
  addressComplement?: string;
  postalCode: string;
  city: string;
  workRadius: number;
  iban: string;
  bic: string;
  termsAccepted: boolean;
  privacyPolicyAccepted: boolean;
}

// Réponse - même structure que pour l'hébergeur avec userType: 'cleaner'
```

#### Connexion
```typescript
// Requête
interface LoginRequest {
  email: string;
  password: string;
  rememberMe: boolean;
}

// Réponse - même structure que pour l'inscription
```

### 7.3 Gestion des tokens

- Stockage sécurisé des tokens (AsyncStorage avec chiffrement)
- Mise en place d'un intercepteur pour ajouter le token à chaque requête
- Logique de rafraîchissement automatique du token lorsque nécessaire
- Suppression des tokens lors de la déconnexion

## 8. Gestion d'état et stockage local

### 8.1 État d'authentification

- Utilisation de React Context ou Redux pour gérer l'état d'authentification
- Structure de l'état:
  ```typescript
  interface AuthState {
    isAuthenticated: boolean;
    userType: 'host' | 'cleaner' | null;
    user: {
      id: string;
      email: string;
      firstName: string;
      lastName: string;
      emailVerified: boolean;
    } | null;
    loading: boolean;
    error: string | null;
  }
  ```

### 8.2 Stockage local

- AsyncStorage pour les tokens et préférences utilisateur
- Structure des données stockées:
  ```typescript
  {
    'auth_tokens': {
      accessToken: string;
      refreshToken: string;
      expiresAt: number; // timestamp
    },
    'user_preferences': {
      rememberMe: boolean;
      // autres préférences utilisateur
    }
  }
  ```

## 9. Validation et gestion des erreurs

### 9.1 Validation côté client

- Validation synchrone des champs de formulaire (format, longueur, complexité)
- Affichage des erreurs sous les champs correspondants
- Désactivation des boutons de soumission tant que le formulaire n'est pas valide

### 9.2 Gestion des erreurs API

- Gestion des codes d'erreur HTTP (400, 401, 403, 500, etc.)
- Messages d'erreur explicites pour chaque cas d'erreur:
  - Email déjà utilisé
  - Identifiants incorrects
  - Problème de connexion réseau
  - Erreurs serveur

## 10. Tests

### 10.1 Tests unitaires

- Tests des composants individuels
- Tests des fonctions de validation
- Tests des reducers et actions (si Redux)

### 10.2 Tests d'intégration

- Tests des flux complets (inscription, connexion, récupération de mot de passe)
- Tests des interactions avec le backend (avec des mocks)

### 10.3 Tests manuels

- Vérification sur différents appareils (iOS et Android)
- Vérification des scénarios d'erreur
- Vérification de l'accessibilité

## 11. Considérations de sécurité

- Protection contre les attaques XSS
- Validation stricte des entrées utilisateur
- Chiffrement des données sensibles stockées localement
- Limitation des tentatives de connexion infructueuses
- Protection des champs de mot de passe

## 12. Exigences techniques et dépendances

### 12.1 Bibliothèques React Native requises

- **react-navigation**: Pour la navigation entre les écrans
- **formik** ou **react-hook-form**: Pour la gestion des formulaires
- **yup**: Pour la validation des formulaires
- **axios**: Pour les requêtes HTTP
- **react-native-keychain** ou équivalent: Pour le stockage sécurisé
- **expo-secure-store**: Alternative pour le stockage sécurisé avec Expo
- **redux** et **redux-toolkit** (optionnel): Pour la gestion d'état global
- **react-native-elements** ou autre UI kit: Pour les composants d'interface

### 12.2 Structure des dossiers pour cette fonctionnalité

```
src/
├── screens/
│   ├── auth/
│   │   ├── WelcomeScreen.tsx
│   │   ├── LoginScreen.tsx
│   │   ├── RegisterHostScreen.tsx
│   │   ├── RegisterCleanerScreen.tsx
│   │   ├── ForgotPasswordScreen.tsx
│   │   ├── ResetPasswordScreen.tsx
│   │   └── EmailVerificationScreen.tsx
├── components/
│   ├── auth/
│   │   ├── WelcomeHeader.tsx
│   │   ├── UserTypeSelectionCard.tsx
│   │   ├── AuthNavigationLink.tsx
│   │   ├── LoginForm.tsx
│   │   ├── RegisterHostForm.tsx
│   │   ├── RegisterCleanerForm.tsx
│   │   ├── FormStepIndicator.tsx
│   │   └── PasswordStrengthIndicator.tsx
├── navigation/
│   ├── AuthNavigator.tsx
├── services/
│   ├── api/
│   │   ├── auth.ts
│   ├── storage/
│   │   ├── tokenStorage.ts
├── context/ (ou store/ si Redux)
│   ├── auth/
│   │   ├── authContext.tsx (ou authSlice.ts si Redux)
├── utils/
│   ├── validation.ts
│   ├── errorHandling.ts
├── constants/
│   ├── errorMessages.ts
│   ├── validationRules.ts
└── styles/
    ├── auth.ts
```

## 13. Planning d'implémentation

### 13.1 Étapes d'implémentation

1. **Préparation (1-2 jours)**
   - Installation des dépendances nécessaires
   - Configuration de la navigation
   - Mise en place des styles de base et constantes

2. **Écran d'accueil (1-2 jours)**
   - Implémentation des composants de l'écran d'accueil
   - Mise en place de la navigation vers l'inscription et la connexion

3. **Formulaires d'inscription (3-4 jours)**
   - Implémentation des formulaires multi-étapes pour les deux types d'utilisateurs
   - Validation des champs et gestion des erreurs

4. **Écran de connexion (1-2 jours)**
   - Implémentation du formulaire de connexion
   - Gestion des erreurs d'authentification

5. **Récupération de mot de passe (1-2 jours)**
   - Implémentation des écrans de demande et de réinitialisation

6. **Intégration backend (2-3 jours)**
   - Connexion avec les endpoints API
   - Gestion des tokens et des réponses

7. **Tests et débogage (2-3 jours)**
   - Tests des différents flux
   - Correction des bugs identifiés

### 13.2 Timeline estimée

- **Semaine 1**: Préparation, écran d'accueil, début des formulaires d'inscription
- **Semaine 2**: Finalisation des formulaires d'inscription, écran de connexion, récupération de mot de passe
- **Semaine 3**: Intégration backend, tests et débogage

## 14. Conclusion

Ce plan d'implémentation définit la structure et les fonctionnalités des interfaces d'inscription et de connexion pour l'application mobile. Il servira de guide pour le développement de cette partie essentielle de l'application, assurant une expérience utilisateur cohérente et conforme aux spécifications du projet.

En suivant ce plan, nous nous assurerons que l'écran d'accueil et les interfaces d'authentification répondent aux besoins spécifiques des deux types d'utilisateurs tout en maintenant un design moderne et une expérience fluide.