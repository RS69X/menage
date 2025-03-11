# Types pour l'écran d'accueil

Ce document décrit les interfaces TypeScript utilisées pour les composants de l'écran d'accueil.

## Vue d'ensemble

L'écran d'accueil est composé de plusieurs composants modulaires qui nécessitent des interfaces de types bien définies pour garantir la cohérence et la type-safety. Ces composants sont conçus pour être réutilisables, maintenables et accessibles.

## Hiérarchie des types

```
src/types/
└── components/
    └── auth/
        └── welcome-screen.types.ts   # Contient toutes les interfaces pour l'écran d'accueil
```

## Interfaces de types

Les interfaces suivantes sont définies dans `src/types/components/auth/welcome-screen.types.ts`:

```typescript
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

## Intégration avec les types existants

Ces interfaces utilisent des types existants tels que `UserType` défini dans `src/types/models/user.ts`:

```typescript
/**
 * Enum des types d'utilisateurs
 */
export enum UserType {
  HOST = 'host',
  CLEANER = 'cleaner'
}
```

## Utilisation dans les composants

Exemple d'utilisation dans un composant:

```typescript
import { UserTypeCardProps } from '@/types/components/auth/welcome-screen.types';

const UserTypeCard: React.FC<UserTypeCardProps> = ({
  type,
  title,
  description,
  icon,
  onPress,
}) => {
  // Implémentation du composant
};
```

## Bonnes pratiques

- Utiliser des interfaces plutôt que des types pour les props des composants
- Documenter chaque prop avec des commentaires JSDoc
- Spécifier les valeurs par défaut dans la documentation
- Utiliser des types optionnels (?) pour les props non obligatoires