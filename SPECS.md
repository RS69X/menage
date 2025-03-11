# Spécifications - Application de mise en relation entre professionnels de ménage et hébergeurs

## 1. Vision globale du projet

Cette application mobile vise à connecter efficacement les professionnels du ménage avec les professionnels de l'hébergement (hôtels, locations saisonnières, Airbnb, etc.) pour faciliter la réservation de services de nettoyage. La plateforme permet aux hébergeurs de publier des offres de mission de ménage et aux professionnels de ménage de postuler à ces missions selon leurs disponibilités et préférences.

## 2. Utilisateurs cibles

### 2.1 Professionnels de l'hébergement
- Hôteliers
- Propriétaires de locations saisonnières
- Gestionnaires de résidences
- Propriétaires d'Airbnb et autres plateformes similaires

### 2.2 Professionnels du ménage
- Auto-entrepreneurs
- Entreprises de nettoyage
- Indépendants du secteur

## 3. Fonctionnalités clés

### 3.1 Système d'inscription et authentification

#### 3.1.1 Écran d'accueil et orientation utilisateur

L'écran d'accueil constitue le point d'entrée critique de l'application, servant à la fois d'introduction au service et de carrefour décisionnel pour l'utilisateur. Cet écran remplit plusieurs fonctions essentielles :

- **Présentation du concept** : Explication concise de la proposition de valeur de l'application
- **Différenciation des parcours utilisateurs** : Séparation claire entre les deux types d'utilisateurs (hébergeurs et professionnels de ménage)
- **Orientation vers le processus d'inscription adapté** : Redirection vers le formulaire spécifique selon le profil choisi

L'interface comprend :
- **En-tête (WelcomeHeader)** : Logo et nom de l'application
- **Section de présentation (AppDescription)** : Titre accrocheur et brève description du service avec illustration
- **Section de choix du profil (UserTypeSelection)** : Deux cartes distinctes représentant chaque type d'utilisateur
  - **Carte Hébergeur** : Icône, titre "Je suis hébergeur", description courte
  - **Carte Professionnel** : Icône, titre "Je suis professionnel de ménage", description courte
- **Lien d'authentification** : "Déjà inscrit ? Se connecter" pour les utilisateurs existants
- **Pied de page (FooterLinks)** : Liens vers les mentions légales (conditions d'utilisation, politique de confidentialité)

##### Différenciation visuelle des types d'utilisateurs

La distinction entre les deux parcours utilisateurs est renforcée par une différenciation visuelle claire :
- **Hébergeurs** : Utilisation de la couleur primaire (#4361EE) pour tous les éléments visuels associés
- **Professionnels de ménage** : Utilisation de la couleur secondaire (#3F37C9) pour créer une distinction visuelle immédiate

Cette cohérence chromatique est maintenue tout au long du parcours utilisateur, depuis l'écran d'accueil jusqu'aux tableaux de bord spécifiques, renforçant ainsi l'identité distincte de chaque type d'utilisateur.

##### Architecture technique et composants

L'écran d'accueil suit une architecture modulaire avec des composants réutilisables et fortement typés :
- Chaque élément d'interface est implémenté comme un composant React indépendant
- Les interfaces TypeScript complètes pour ces composants sont documentées dans `docs/welcome-screen-types.md`
- Les composants utilisent le système de thème global défini dans `src/constants/theme.ts`
- Les animations d'entrée et effets d'interaction sont implémentés avec React Native Reanimated

Des animations subtiles d'entrée et des effets d'interaction sur les éléments cliquables enrichissent l'expérience utilisateur sans entraver l'accessibilité.

Cette segmentation dès l'entrée dans l'application permet d'optimiser l'expérience utilisateur en présentant uniquement les fonctionnalités et formulaires pertinents pour chaque type d'utilisateur, réduisant ainsi la complexité perçue et augmentant les taux de conversion.

#### 3.1.2 Formulaire d'inscription pour hébergeurs

Le formulaire d'inscription des hébergeurs est une composante essentielle permettant aux professionnels de l'hébergement de s'inscrire pour accéder aux services de mise en relation avec des professionnels du ménage. Ce formulaire représente la seconde étape du parcours utilisateur après l'écran d'accueil, lorsqu'un utilisateur choisit le profil "hébergeur".

##### Structure multi-étapes du formulaire

Le formulaire est divisé en 5 étapes distinctes pour simplifier l'expérience utilisateur en segmentant la saisie des informations en sections logiques et digestibles:

###### Étape 1: Informations personnelles
- Prénom (obligatoire) - Minimum 2 caractères, caractères alphabétiques uniquement
- Nom (obligatoire) - Minimum 2 caractères, caractères alphabétiques uniquement
- Email (obligatoire) - Validation de format par regex
- Téléphone (obligatoire) - Validation de format spécifique

###### Étape 2: Informations professionnelles
- Nom de société (optionnel)
- Numéro SIRET (optionnel) - Validation de format (14 chiffres) et algorithme de contrôle si fourni

###### Étape 3: Adresse complète
- Adresse (obligatoire)
- Complément d'adresse (optionnel)
- Code postal (obligatoire) - Validation de format (5 chiffres pour la France)
- Ville (obligatoire) - Caractères alphabétiques uniquement

###### Étape 4: Création de mot de passe
- Mot de passe (obligatoire) - Minimum 8 caractères, doit contenir au moins une majuscule, une minuscule et un chiffre
- Confirmation du mot de passe (obligatoire) - Doit correspondre au mot de passe
- Indicateur visuel de force du mot de passe

###### Étape 5: Acceptation des conditions et récapitulatif
- Acceptation des conditions d'utilisation (obligatoire)
- Acceptation de la politique de confidentialité (obligatoire)
- Récapitulatif des informations saisies
- Bouton de soumission finale

##### Navigation et progression

- Un indicateur de progression est affiché en haut du formulaire pour indiquer à l'utilisateur sa position dans le processus
- Boutons "Suivant" et "Précédent" pour naviguer entre les étapes
- Le bouton "Suivant" n'est activé que si tous les champs obligatoires de l'étape actuelle sont correctement remplis
- Les données saisies sont conservées lors de la navigation entre les étapes
- L'étape finale contient un bouton "S'inscrire" pour finaliser l'inscription

##### Validation des données

- Affichage des erreurs de validation en temps réel sous chaque champ concerné
- Messages d'erreur clairs et explicites pour guider l'utilisateur
- Validation à la soumission de l'étape et du formulaire complet
- Gestion des erreurs API (email déjà utilisé, erreur de connexion, etc.)

##### Soumission du formulaire

- Indicateur de chargement pendant la soumission pour informer l'utilisateur
- Gestion des erreurs API avec messages adaptés
- Redirection vers une page de confirmation en cas de succès
- Email de confirmation envoyé pour valider l'adresse email

##### Architecture technique

Le formulaire d'inscription des hébergeurs suit une architecture modulaire avec une organisation claire:

- Point d'entrée: `app/(auth)/register/host.tsx`
- Composants réutilisables dans `src/components/auth/register/host/`:
  - `RegisterHostForm.tsx` - Composant principal conteneur
  - `FormStepIndicator.tsx` - Indicateur de progression
  - `PersonalInfoStep.tsx` - Composant pour l'étape 1
  - `ProfessionalInfoStep.tsx` - Composant pour l'étape 2
  - `AddressStep.tsx` - Composant pour l'étape 3
  - `PasswordStep.tsx` - Composant pour l'étape 4
  - `SummaryStep.tsx` - Composant pour l'étape 5
  - `PasswordStrengthIndicator.tsx` - Indicateur de force du mot de passe

La gestion d'état utilise React Hook Form avec des schémas de validation Yup. L'intégration avec le backend se fait via le service d'authentification qui communique avec l'API.

#### Pour les professionnels de ménage:
- **Formulaire d'inscription spécifique** comprenant:
  - Informations personnelles (nom, prénom, email, téléphone)
  - Informations professionnelles obligatoires (SIRET, statut d'entreprise)
  - Nom de société optionnel
  - Adresse complète (adresse, ville, code postal)
  - Préférences de travail (rayon de déplacement)
  - Création de mot de passe sécurisé (minimum 8 caractères)
  - Acceptation des conditions d'utilisation
- **Enregistrement des coordonnées bancaires** (IBAN/BIC) pour les paiements
- **Vérification du SIRET**
- **Connexion sécurisée** via email et mot de passe

#### Architecture d'authentification:

##### Système de tokens JWT
- **Access Token**: Token de courte durée (1 jour) utilisé pour accéder aux ressources protégées
- **Refresh Token**: Token de longue durée (7 jours) stocké en base de données, permettant de générer un nouveau token d'accès sans reconnecter l'utilisateur

##### Stratégies d'authentification
- **Local Strategy**: Pour l'authentification par identifiants (email/mot de passe)
- **JWT Strategy**: Pour la vérification des tokens d'accès
- **Refresh Token Strategy**: Pour le rafraîchissement des tokens expirés

##### Protection des routes
- **Guards d'authentification**: Protection des routes nécessitant une authentification
- **Guards de rôles**: Restriction d'accès basée sur le type d'utilisateur (hébergeur/professionnel)

##### Flux complet d'authentification
1. **Inscription**: Validation des données, hachage du mot de passe, génération de tokens
2. **Connexion**: Vérification des identifiants, génération de tokens
3. **Accès aux ressources**: Validation du token d'accès via le JWT Guard
4. **Rafraîchissement**: Utilisation du refresh token pour obtenir de nouveaux tokens
5. **Vérification d'email**: Confirmation de l'adresse email via un lien sécurisé
6. **Réinitialisation de mot de passe**: Processus sécurisé de récupération de compte

##### Sécurité
- Hachage des mots de passe avec bcrypt (10 rounds de salage)
- Validation stricte des entrées utilisateur
- Protection contre les attaques par force brute (rate limiting)
- Chiffrement des données sensibles (coordonnées bancaires)
- Invalidation des tokens lors de la déconnexion ou du changement de mot de passe

### 3.1.2 Gestion des utilisateurs (Module Users)

#### Modèle de données utilisateurs

##### Architecture des entités
- **Modèle à deux niveaux**: 
  - `User`: Entité de base contenant les informations communes
  - `Host` et `Cleaner`: Entités spécifiques avec relations one-to-one vers User

##### Entité de base User
- Identifiant unique (UUID)
- Informations personnelles (email, nom, prénom, téléphone)
- Mot de passe (stocké sous forme hachée)
- Type d'utilisateur (hébergeur ou professionnel)
- Adresse complète (adresse, ville, code postal)
- Statuts de vérification (email vérifié, identité vérifiée)
- Statut du compte (actif/inactif)
- Refresh token (pour l'authentification)
- Timestamps (création, modification)

##### Entité Host (Hébergeur)
- Relation one-to-one avec User
- Nom de société (optionnel)
- Numéro SIRET (optionnel)
- Évaluation moyenne et nombre d'avis
- Relations avec les annonces publiées

##### Entité Cleaner (Professionnel de ménage)
- Relation one-to-one avec User
- Numéro SIRET (obligatoire)
- Statut d'entreprise (individuel ou société)
- Nom de société (obligatoire si entreprise)
- Informations bancaires chiffrées (IBAN/BIC)
- Rayon de travail (en km)
- Évaluation moyenne et nombre d'avis
- Relations avec les réservations

#### Fonctionnalités du module utilisateurs
- **Gestion de profil**:
  - Création de comptes spécifiques par type d'utilisateur
  - Mise à jour des informations personnelles et professionnelles
  - Modification des préférences (rayon de travail, disponibilités)
  - Gestion des coordonnées bancaires sécurisées

- **Validation et vérification**:
  - Vérification d'email par lien de confirmation
  - Validation de SIRET via API externe
  - Validation des coordonnées bancaires (format IBAN/BIC)
  - Vérification d'identité (état géré par l'admin)

- **Sécurité et conformité RGPD**:
  - Chiffrement des données bancaires sensibles
  - Droit à l'oubli (suppression complète des données)
  - Droit d'accès aux données personnelles
  - Journalisation des accès aux informations sensibles
  - Masquage des données personnelles dans les logs

- **Intégration avec autres modules**:
  - Lien avec le module Auth pour l'authentification
  - Relation avec les annonces (hébergeurs)
  - Relation avec les réservations (professionnels)
  - Support pour le système d'évaluation et d'avis

#### API du module utilisateurs
Le module expose des endpoints REST sécurisés pour:
- Récupération d'un profil utilisateur
- Mise à jour de profil hébergeur
- Mise à jour de profil professionnel
- Listing des utilisateurs (accès admin)
- Suppression de compte (conformité RGPD)

### 3.2 Tableau de bord des hébergeurs

- **Nouvelle annonce**: Formulaire de création d'offre de mission
  - Type d'hébergement
  - Adresse
  - Nombre de personnes recherchées
  - Dates et horaires
  - Superficie
  - Services à réaliser
  - Équipements
  - Détails supplémentaires
  - Calcul automatique du prix (base de 15,50€/heure + 15% de commission)

- **Messages**: Système de messagerie interne
- **Annonces publiées**: Historique et gestion des annonces
- **Notifications**: Alertes pour les candidatures et autres événements
- **Profil**: Gestion des informations personnelles et confidentielles
  - Identité
  - Coordonnées
  - Historique des factures

### 3.3 Tableau de bord des professionnels de ménage

- **Recherches**: Interface de swipe pour découvrir les offres correspondant aux préférences
  - Informations sur l'annonce (type d'hébergement, dates, services, tarif)
  - Localisation approximative
  - Note de l'hébergeur

- **Messages**: Système de messagerie interne
- **Notifications**: Alertes pour les missions acceptées et autres événements
- **Profil**: Gestion des informations personnelles et confidentielles
  - Identité/entreprise
  - Coordonnées bancaires (chiffrées)
  - Préférences de travail (périmètre, disponibilités, types d'hébergement préférés)

- **Facturation**: Suivi des factures par mois
- **Planning**: Calendrier des missions programmées

### 3.4 Processus de réservation et réalisation

1. L'hébergeur publie une annonce
2. Le professionnel de ménage candidate à une annonce
3. L'hébergeur reçoit une notification et consulte le profil du candidat (notations, expérience)
4. L'hébergeur valide une candidature et procède au paiement sécurisé
5. Le professionnel de ménage reçoit la confirmation dans son planning
6. La veille de la mission, le professionnel reçoit les informations précises et contacts
7. Le jour de la mission:
   - Notification avec formulaire des tâches à accomplir
   - Vérification de présence (géolocalisation)
   - Option de rétractation avec preuve (photos) si mission non conforme
   - Validation des tâches réalisées

8. L'hébergeur reçoit une notification de fin de mission
9. Période de réclamation d'une semaine (avec preuves photos si besoin)
10. Paiement libéré au professionnel si aucune réclamation
11. Édition des factures pour les deux parties

### 3.5 Système de notation et d'avis

- Notation réciproque sur 5 étoiles
- Commentaires et avis après mission
- Historique des évaluations visible sur les profils

### 3.6 Gestion des paiements

- Système de paiement sécurisé
- Encaissement initial par la plateforme
- Commission de 15% prélevée par le site
- Paiement au prestataire 7 jours après la mission (en l'absence de réclamation)
- Possibilité de remboursement en cas de litige justifié

## 4. Architecture technique

### 4.1 Architecture générale

- Application mobile React Native avec Expo
- API RESTful pour la communication client-serveur
- Base de données pour le stockage des données utilisateurs et des missions
- Système de notification push pour les alertes en temps réel
- Intégration de services tiers pour les paiements et vérifications
- **Organisation des repositories**:
  - Repository frontend: `menage-connect-mobile` (React Native/Expo)
  - Repository backend: `menage-connect-api` (Node.js/NestJS)
- **Structure modulaire** permettant le développement parallèle des composants

### 4.2 Front-end (Mobile)

- React Native avec Expo
- Navigation: Expo Router (système de routage basé sur les fichiers)
- Gestion d'état: Zustand
- UI/UX: Design system cohérent pour l'expérience utilisateur
- Formulaires et validation: React Hook Form avec Yup
- **Structure des dossiers**:
  - `/assets` : Ressources statiques
  - `/app` : Écrans et navigation de l'application (basé sur Expo Router)
    - `/(auth)` : Routes pour l'authentification
    - `/(host)` : Routes pour les hébergeurs
    - `/(cleaner)` : Routes pour les professionnels
    - `_layout.tsx` : Layouts pour configurer la navigation
  - `/src` : Code source de l'application
    - `/components` : Composants réutilisables
    - `/services` : Services d'API et d'authentification
    - `/store` : État global avec Zustand
    - `/types` : Types TypeScript
    - `/utils` : Fonctions utilitaires
    - `/constants` : Constantes et configuration
    - `/hooks` : Hooks personnalisés
    - `/styles` : Styles partagés

#### 4.2.1 Architecture des composants de formulaire réutilisables

L'application implémente une bibliothèque de composants de formulaire hautement réutilisables pour garantir la cohérence visuelle, simplifier le développement et améliorer la maintenabilité. Cette architecture modulaire constitue la fondation des interfaces de saisie de données dans toute l'application, particulièrement dans les formulaires multi-étapes complexes comme le processus d'inscription.

##### Principes architecturaux

L'architecture des composants de formulaire repose sur plusieurs principes clés:

1. **Séparation des responsabilités**: Chaque composant a un rôle unique et bien défini
2. **Composition plutôt qu'héritage**: Les composants complexes sont construits en composant des éléments plus simples
3. **Intégration native avec React Hook Form**: Tous les composants sont conçus pour fonctionner de manière optimale avec React Hook Form
4. **Centralisation de la validation**: La validation est gérée via des schémas Yup réutilisables
5. **Accessibilité par défaut**: Tous les composants respectent les standards d'accessibilité WCAG
6. **Réactivité et retour visuel**: Les composants fournissent un feedback visuel immédiat (états de focus, erreur, désactivé)
7. **Typages stricts**: Utilisation intensive de TypeScript pour garantir la sécurité de type

##### Structure et organisation

Les composants de formulaire sont organisés dans le dossier `src/components/common/form/` avec un fichier index.ts qui centralise les exports:

```
src/components/common/form/
├── FormInput.tsx           # Composant de base pour les champs texte
├── FormPasswordInput.tsx   # Extension de FormInput pour les mots de passe
├── FormCheckbox.tsx        # Composant pour cases à cocher
├── FormButton.tsx          # Bouton standardisé avec variantes
├── FormError.tsx           # Affichage des erreurs générales
└── index.ts                # Export centralisé de tous les composants
```

Cette organisation permet d'importer tous les composants depuis un point d'entrée unique:

```typescript
import { FormInput, FormPasswordInput, FormCheckbox, FormButton, FormError } from '@/components/common/form';
```

##### Composants fondamentaux

###### FormInput
Composant de base pour tous les champs de saisie textuels, avec support complet de React Hook Form:

**Caractéristiques principales**:
- Intégration native avec React Hook Form via le composant Controller
- Support pour tous les états (normal, focus, erreur, désactivé)
- Gestion des labels et messages d'erreur
- Support des icônes de début et de fin
- Fonction de nettoyage rapide du champ
- Compatibilité complète avec les technologies d'assistance

**Interface**:
```typescript
interface FormInputProps<T extends FieldValues> {
  // Props pour React Hook Form
  control: UseControllerProps<T>['control'];
  name: UseControllerProps<T>['name'];
  rules?: UseControllerProps<T>['rules'];
  
  // Props d'affichage et comportement
  label: string;
  error?: string;
  showOptionalLabel?: boolean;
  hideErrorMessage?: boolean;
  helperText?: string;
  startIcon?: React.ReactNode;
  endIcon?: React.ReactNode;
  onClear?: () => void;
  
  // Plus les props standards de TextInput
  // (placeholder, autoCapitalize, etc.)
}
```

###### FormPasswordInput
Extension spécialisée de FormInput pour la saisie de mots de passe, avec fonctionnalité de masquage/affichage:

**Caractéristiques spécifiques**:
- Toggle de visibilité du mot de passe
- Configuration sécurisée par défaut (autoComplete="off", etc.)
- Support optionnel pour indicateur de force du mot de passe

**Exemple d'utilisation**:
```typescript
<FormPasswordInput
  control={control}
  name="password"
  label="Mot de passe"
  placeholder="Créez un mot de passe sécurisé"
  error={errors.password?.message}
  showStrengthIndicator={true}
/>
```

###### FormCheckbox
Composant pour les cases à cocher avec support pour contenu riche dans les labels:

**Caractéristiques principales**:
- Animations de transition entre états (checked/unchecked)
- Support pour contenus riches dans les labels (liens, formatage)
- Styles d'erreur et messages personnalisables
- Support complet pour l'accessibilité

**Exemple d'utilisation**:
```typescript
<FormCheckbox
  control={control}
  name="termsAccepted"
  label={
    <Text>
      J'accepte les <Text style={styles.link} onPress={openTerms}>conditions d'utilisation</Text>
    </Text>
  }
  error={errors.termsAccepted?.message}
/>
```

###### FormButton
Bouton standardisé avec de multiples variantes et états:

**Caractéristiques principales**:
- Variantes: primaire, secondaire, outline, text
- Tailles: small, medium, large
- États: normal, désactivé, chargement
- Support d'icônes de début et de fin
- Largeur adaptative ou complète

**Variantes visuelles**:
- **Primary**: Style en évidence avec couleur de fond primaire
- **Secondary**: Style alternatif avec couleur de fond secondaire
- **Outline**: Contour coloré sans fond
- **Text**: Style texte uniquement sans fond ni bordure

**Exemple d'utilisation**:
```typescript
<FormButton
  title="Continuer"
  onPress={handleNext}
  variant="primary"
  size="large"
  fullWidth={true}
  loading={isSubmitting}
  rightIcon={<ArrowRightIcon />}
/>
```

###### FormError
Composant pour l'affichage standardisé des messages d'erreur:

**Caractéristiques principales**:
- Support pour différents niveaux de gravité (error, warning, info)
- Affichage d'un ou plusieurs messages
- Style visuel distinctif avec icône facultative
- Intégration avec les rôles d'accessibilité (`alert`)

**Exemple d'utilisation**:
```typescript
<FormError
  message="L'adresse email est déjà utilisée. Veuillez en choisir une autre ou vous connecter."
  severity="error"
/>
```

##### Intégration avec React Hook Form

Tous les composants sont conçus pour s'intégrer parfaitement avec React Hook Form, ce qui permet:

1. **Validation en temps réel**: Feedback immédiat à l'utilisateur
2. **Gestion des erreurs cohérente**: Format standardisé des messages
3. **Performance optimisée**: Évite les re-rendus inutiles
4. **Cohérence du formulaire**: Un seul point de vérité pour l'état du formulaire

**Exemple d'intégration complète**:

```typescript
// Dans un composant d'étape de formulaire
import { useFormContext } from 'react-hook-form';
import { FormInput, FormButton } from '@/components/common/form';
import { RegisterHostFormType } from '@/types';

const PersonalInfoStep: React.FC = () => {
  const { control, formState: { errors, isValid } } = useFormContext<RegisterHostFormType>();
  
  return (
    <View style={styles.container}>
      <FormInput
        control={control}
        name="firstName"
        label="Prénom"
        placeholder="Votre prénom"
        error={errors.firstName?.message}
      />
      
      <FormInput
        control={control}
        name="lastName"
        label="Nom"
        placeholder="Votre nom"
        error={errors.lastName?.message}
      />
      
      <FormInput
        control={control}
        name="email"
        label="Email"
        placeholder="votre@email.com"
        keyboardType="email-address"
        autoCapitalize="none"
        error={errors.email?.message}
      />
      
      <FormInput
        control={control}
        name="phoneNumber"
        label="Téléphone"
        placeholder="Votre numéro de téléphone"
        keyboardType="phone-pad"
        error={errors.phoneNumber?.message}
      />
    </View>
  );
};
```

##### Validation avec Yup

La validation des formulaires est gérée grâce à des schémas Yup qui sont organisés par domaine et réutilisables:

```typescript
// Schéma de validation pour l'étape des informations personnelles
export const personalInfoSchema = yup.object({
  firstName: yup
    .string()
    .required('Le prénom est requis')
    .min(2, 'Le prénom doit contenir au moins 2 caractères')
    .matches(/^[a-zA-ZÀ-ÿ\s'-]+$/, 'Le prénom ne doit contenir que des lettres'),
  lastName: yup
    .string()
    .required('Le nom est requis')
    .min(2, 'Le nom doit contenir au moins 2 caractères')
    .matches(/^[a-zA-ZÀ-ÿ\s'-]+$/, 'Le nom ne doit contenir que des lettres'),
  email: yup
    .string()
    .required('L\'email est requis')
    .email('Format d\'email invalide'),
  phoneNumber: yup
    .string()
    .required('Le numéro de téléphone est requis')
    .matches(PHONE_REGEX, 'Format de téléphone invalide')
});
```

Ces schémas sont ensuite intégrés à React Hook Form via le resolver Yup:

```typescript
const methods = useForm<RegisterHostFormType>({
  resolver: yupResolver(registerHostSchema),
  mode: 'onChange',
  defaultValues: {
    firstName: '',
    lastName: '',
    email: '',
    phoneNumber: '',
    // ... autres champs
  }
});
```

##### Thème et styles cohérents

Tous les composants suivent un système de design cohérent défini dans les constantes de thème (`src/constants/theme.ts`):

```typescript
// Exemple de constantes de thème utilisées par les composants de formulaire
export const COLORS = {
  primary: '#4361EE',
  text: '#212121',
  error: '#F44336',
  errorLight: '#FFEBEE',
  border: '#E0E0E0',
  // ... autres couleurs
};

export const SPACING = {
  small: 8,
  medium: 16,
  large: 24,
  // ... autres espaces
};

export const FONT_SIZES = {
  tiny: 12,
  small: 14,
  medium: 16,
  large: 18,
  // ... autres tailles
};

export const BORDER_RADIUS = {
  small: 4,
  medium: 8,
  large: 12,
  // ... autres rayons
};
```

Cette approche garantit une expérience utilisateur visuellement cohérente à travers toute l'application, tout en facilitant les évolutions du design.

##### Avantages de l'architecture

Cette architecture modulaire apporte de nombreux avantages:

1. **Développement accéléré**: Les composants prêts à l'emploi réduisent le temps de développement
2. **Cohérence visuelle**: Tous les formulaires partagent le même langage visuel
3. **Maintenabilité améliorée**: Les modifications de style ou de comportement peuvent être centralisées
4. **Réutilisabilité maximale**: Les composants sont utilisables dans tous les contextes de formulaire
5. **Expérience utilisateur optimisée**: Retours visuels immédiats et cohérents
6. **Accessibilité garantie**: Les bonnes pratiques sont intégrées aux composants de base
7. **Évolutivité simplifiée**: L'ajout de nouvelles fonctionnalités n'impacte pas les composants existants

Ces avantages sont particulièrement importants dans le contexte des formulaires multi-étapes complexes comme le processus d'inscription, où la cohérence et la fiabilité sont essentielles pour garantir une expérience utilisateur fluide et réduire les abandons.

### 4.3 Back-end

- Node.js (version 18.x LTS ou supérieure)
- **Framework**: NestJS
  - Support natif de TypeScript
  - Architecture modulaire
  - Injection de dépendances intégrée
  - Structure opinionnée facilitant la maintenance
  - Intégration simplifiée avec ORM
- **Base de données**: PostgreSQL (via TypeORM) ou MongoDB (à finaliser)
- **Authentification**: JWT (JSON Web Tokens)
- **Validation**: class-validator et class-transformer
- **Stockage des fichiers**: AWS S3 ou équivalent
- **Documentation API**: Swagger (via @nestjs/swagger)
- **Tests**: Jest intégré à NestJS

#### Structure des dossiers du backend

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
│   │   ├── constants/             # Constantes partagées dans l'application
│   │   ├── decorators/            # Décorateurs personnalisés
│   │   ├── filters/               # Filtres d'exception
│   │   ├── guards/                # Guards d'authentification
│   │   ├── interceptors/          # Intercepteurs pour requêtes/réponses
│   │   ├── interfaces/            # Interfaces et types communs
│   │   ├── middleware/            # Middleware partagés
│   │   ├── pipes/                 # Validation des données
│   │   └── utils/                 # Fonctions utilitaires
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
│   │   │   │   ├── user.entity.ts  # Entité de base utilisateur
│   │   │   │   ├── host.entity.ts  # Entité spécifique hébergeur
│   │   │   │   └── cleaner.entity.ts # Entité spécifique professionnel
│   │   │   ├── repositories/      # Repositories personnalisés
│   │   │   │   ├── host.repository.ts
│   │   │   │   └── cleaner.repository.ts
│   │   │   ├── dto/               # Objets de transfert de données
│   │   │   │   ├── create-user.dto.ts  # DTO base pour la création d'utilisateur
│   │   │   │   ├── create-host.dto.ts  # DTO pour la création d'hébergeur
│   │   │   │   ├── create-cleaner.dto.ts # DTO pour la création de professionnel
│   │   │   │   ├── update-user.dto.ts  # DTO base pour la mise à jour d'utilisateur
│   │   │   │   └── user-response.dto.ts # DTO pour les réponses utilisateur
│   │   │   ├── interfaces/        # Interfaces et types
│   │   │   │   ├── user-type.enum.ts # Enum des types d'utilisateur
│   │   │   │   └── user.interface.ts # Interface utilisateur
│   │   │   └── tests/             # Tests du module
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

#### Organisation des modules
Chaque module suit une structure cohérente:
- **Module** (`*.module.ts`): Définit les imports, providers et exports
- **Controller** (`*.controller.ts`): Gère les routes HTTP
- **Service** (`*.service.ts`): Contient la logique métier
- **Entities** (`entities/*.entity.ts`): Définit les modèles de données
- **DTOs** (`dto/*.dto.ts`): Définit les objets de transfert de données
- **Tests** (`tests/*.spec.ts`): Tests unitaires et d'intégration

#### Architecture des éléments communs (Common Elements)

Le dossier `common/` contient l'infrastructure partagée de l'application, constituée de composants réutilisables qui normalisent et sécurisent les opérations à travers tous les modules. Cette couche transversale est essentielle pour assurer la cohérence, la sécurité et la maintenabilité du système. Un plan d'implémentation détaillé est disponible dans `docs/common-elements-implementation-plan.md`.

##### Types d'éléments communs et leurs fonctions

1. **Guards**: Contrôlent l'accès aux routes API
   - Authentification JWT pour vérifier les tokens d'accès
   - Vérification des rôles (HOST/CLEANER) pour limiter l'accès selon le type d'utilisateur
   - Protection des ressources sensibles (données personnelles, informations bancaires)
   - Exemption d'authentification pour les routes publiques

2. **Filters**: Standardisent la gestion des erreurs et exceptions
   - Format uniforme des réponses d'erreur (status, message, timestamp, path)
   - Gestion spécifique des erreurs de validation avec détails structurés
   - Transformation des erreurs de base de données en messages conviviaux
   - Journalisation centralisée des exceptions pour monitoring

3. **Interceptors**: Transforment les requêtes et réponses
   - Normalisation du format des réponses API (`success`, `data`, `timestamp`)
   - Journalisation des requêtes et temps de réponse pour analyse de performance
   - Application de timeouts pour éviter les requêtes bloquantes
   - Mise en cache conditionnelle pour optimiser les performances

4. **Decorators**: Simplifient l'utilisation des fonctionnalités transversales
   - Annotation `@Public()` pour marquer les routes accessibles sans authentification
   - Annotation `@Roles()` pour définir les types d'utilisateurs autorisés
   - Annotation `@CurrentUser()` pour injecter l'utilisateur authentifié dans les méthodes
   - Décorateurs pour la documentation API et la pagination

5. **Pipes**: Valident et transforment les données entrantes
   - Validation automatique des DTOs basée sur les annotations class-validator
   - Transformation des paramètres de requête (conversion de type, parsing)
   - Assainissement des entrées utilisateur pour prévenir les injections
   - Validation d'IDs et formats spécifiques (SIRET, IBAN)

6. **Middleware**: Traitent les requêtes avant qu'elles n'atteignent les routes
   - Journalisation des requêtes entrantes pour audit
   - Configuration CORS pour la sécurité cross-origin
   - Rate-limiting pour prévenir les attaques par force brute
   - Compression des réponses pour optimiser les performances

7. **Constants & Interfaces**: Garantissent la cohérence du code
   - Types d'utilisateurs et rôles standardisés
   - Formats de réponse API normalisés
   - Codes d'erreur catalogués et documentés
   - Structures de pagination uniformes

8. **Utils**: Fonctions utilitaires partagées
   - Gestion sécurisée des configurations
   - Fonctions de hachage et cryptographie
   - Manipulation de dates et formats
   - Helpers pour la pagination et le filtrage des données

##### Avantages architecturaux des éléments communs

1. **Sécurité renforcée**: Centralisation des mécanismes de protection (authentification, validation, assainissement)
2. **Maintenabilité améliorée**: Réduction de la duplication de code et standardisation des pratiques
3. **Cohérence des API**: Format uniforme des requêtes, réponses et erreurs
4. **Séparation des préoccupations**: Découplage entre la logique métier et les aspects transversaux
5. **Évolutivité**: Facilité d'ajout de fonctionnalités globales (monitoring, caching, logging)
6. **Testabilité**: Composants isolés et réutilisables, plus faciles à tester unitairement

Ces éléments communs forment ensemble une infrastructure solide qui répond aux exigences non-fonctionnelles du système (section 5), notamment en matière de sécurité, performance et évolutivité.

### 4.4 Sécurité

- Chiffrement des données sensibles (RIB, identité)
- Authentification sécurisée (2FA recommandé)
- Protection contre les attaques courantes (CSRF, XSS)
- Utilisation de Helmet pour renforcer la sécurité HTTP
- Conformité RGPD pour la gestion des données personnelles
- Logs et audit trail des actions sensibles

### 4.5 Intégrations externes

- Passerelle de paiement (Stripe, PayPal ou équivalent)
- Service de vérification de SIRET (API INSEE ou équivalent)
- Service de notifications push (Firebase Cloud Messaging ou équivalent)
- Services de géolocalisation
- Service d'envoi d'emails transactionnels

### 4.6 CI/CD et Déploiement

- **Environnements de déploiement**:
  - Développement: Déploiement automatique depuis la branche `develop`
  - Staging: Déploiement automatique depuis les branches `release/*`
  - Production: Déploiement manuel validé depuis la branche `main`
- **Pipeline d'intégration continue** avec GitHub Actions:
  - Validation automatique du code (linting, tests)
  - Builds automatisés pour chaque Pull Request
  - Tests de régression
- **Stratégie de déploiement**:
  - Backend: Déploiement sur services cloud (AWS, GCP, Azure)
  - Mobile: Publication sur App Store et Google Play

## 5. Exigences non fonctionnelles

### 5.1 Performance

- Temps de réponse < 2 secondes pour les opérations critiques
- Support de la mise en cache pour optimiser les performances
- Optimisation pour les connexions réseau variables (3G/4G/WiFi)
- Utilisation d'interceptors pour le monitoring des temps de réponse
- Mise en cache conditionnelle des réponses API fréquemment demandées
- Implémentation de timeouts pour éviter les opérations bloquantes

### 5.2 Disponibilité

- Objectif de disponibilité: 99.5%
- Plan de sauvegarde et récupération des données
- Mécanismes de graceful degradation en cas de défaillance de services externes
- Gestion robuste des erreurs pour maintenir le service même en cas de problèmes

### 5.3 Évolutivité

- Architecture permettant l'ajout de fonctionnalités supplémentaires
- Modèles de données extensibles
- Séparation claire des préoccupations via les modules et éléments communs
- Conception orientée interface pour faciliter les changements d'implémentation
- Structure de code modulaire permettant l'évolution indépendante des composants

### 5.4 Conformité juridique

- Respect du RGPD pour la protection des données
- Conformité avec les réglementations du travail et de l'auto-entrepreneuriat
- Respect des obligations fiscales (factures conformes)
- Mécanismes intégrés pour le droit à l'oubli et l'exportation des données personnelles
- Journalisation des accès aux données sensibles pour audit

## 6. Méthodologie de développement et Gestion du code source

### 6.1 Stratégie Git

- **Modèle de gestion de branches**: GitFlow adapté au projet
- **Branches principales**:
  - `main`: Code en production stable
  - `develop`: Branche d'intégration pour le développement
  - `release/*`: Préparation des versions pour déploiement
  - `feature/*`: Développement de nouvelles fonctionnalités
  - `hotfix/*`: Corrections urgentes en production
  - `bugfix/*`: Corrections de bugs non urgents

### 6.2 Conventions de nommage

- **Branches de fonctionnalités**: `feature/[phase-number]-[feature-name]`
- **Branches de correction**: `bugfix/[issue-number]-[short-description]` ou `hotfix/[issue-number]-[short-description]`
- **Messages de commit**: `[type]: [description concise] [#issue-number]`
  - Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`
- **Versions**: `v[major].[minor].[patch]`

### 6.3 Protection des branches et règles de collaboration

#### Niveaux de protection par type de branche

- **Branche `main`**:
  - Protection stricte contre les modifications directes
  - Minimum de 2 revues de code requises avant fusion
  - Validation obligatoire par les tests automatisés (lint, test, build)
  - Commits signés obligatoires pour l'intégrité du code
  - Maintien de l'historique linéaire (pas de force push)
  - Restriction des droits de fusion aux lead developers et responsables de déploiement

- **Branche `develop`**:
  - Protection contre les modifications directes
  - Minimum de 1 revue de code requise avant fusion
  - Validation par les tests automatisés (lint, test)
  - Pas de force push autorisé
  - Historique des approbations préservé (nouvelles approbations requises après changements)

- **Branches `release/*`**:
  - Protection intermédiaire
  - Revue de code obligatoire avant fusion
  - Tests complets obligatoires (lint, test, build)
  - Fusion réservée aux responsables de release

- **Branches `feature/*`, `bugfix/*`, `hotfix/*`**:
  - Créées à partir de la branche appropriée (`develop` ou `main` pour hotfixes)
  - Nommage conformément aux conventions établies
  - Doivent être à jour avec leur branche source avant fusion

### 6.4 Procédures spéciales

#### Processus pour les hotfixes

1. Création d'une branche `hotfix/[issue-number]-[description]` depuis `main`
2. Développement et test du correctif
3. Création d'une Pull Request vers `main` avec étiquette `hotfix`
4. Revue de code prioritaire (minimum 1 approbation, idéalement 2)
5. Après approbation et fusion dans `main`, création immédiate d'une PR pour propager le correctif dans `develop`
6. Déploiement d'urgence effectué après vérification en production

#### Gestion des conflits de fusion

1. Résolution locale des conflits (pull de la branche cible dans la branche de travail)
2. Vérification post-résolution du bon fonctionnement de l'application
3. Documentation des décisions prises durant la résolution
4. Mise à jour de la Pull Request avec tests obligatoires

#### Protection du code lié aux paiements et données sensibles

1. Revue de code spécifique obligatoire par un expert en sécurité
2. Tests de sécurité supplémentaires pour les modifications affectant:
   - Le traitement des paiements
   - Le stockage des données bancaires
   - Les informations personnelles identifiables
3. Hooks pre-commit pour détecter les fuites potentielles d'informations sensibles

### 6.5 Processus de développement collaboratif

- **Pull Requests** obligatoires pour toute modification du code
- **Structure standardisée des PR** via template GitHub incluant:
  - Description détaillée des changements
  - Référence aux issues concernées
  - Checklists de test, sécurité, performance et RGPD
  - Captures d'écran pour les changements d'interface
- **Code review** systématique avant intégration avec critères spécifiques selon le type de modification
- **Validation automatisée** du code par les pipelines CI/CD
- **Protection des branches principales** contre les modifications directes

### 6.6 Gestion de la qualité du code

- **Hooks pre-commit** pour linting et tests essentiels
- **Standards de codage** uniformes pour le frontend et le backend
- **Tests automatisés** à tous les niveaux (unitaires, intégration, E2E)
- **Documentation du code** intégrée au processus de développement
- **Revues de code régulières** pour maintenir la cohérence et la qualité

## 7. Futures évolutions possibles (hors scope initial)

- Application web complémentaire
- Dashboard d'administration
- Système de fidélité et parrainage
- Intégration avec des calendriers externes (iCal, Google Calendar)
- Fonctionnalité de chat en temps réel
- Support multilingue