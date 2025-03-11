# Plan d'implémentation de l'application de mise en relation professionnels de ménage et hébergeurs

Ce document présente le plan d'implémentation incrémentale pour le développement de l'application. Chaque phase comprend des tâches spécifiques, organisées pour permettre un développement cohérent et progressif.

## Phase 1: Configuration de l'environnement et architecture de base
- [x] Initialisation du projet React Native avec Expo (frontend)
- [ ] Configuration du système de contrôle de version (Git)
  - [x] Documentation de la stratégie Git dans `docs/git-configuration.md`
  - [x] Organisation des repositories frontend et backend
  - [ ] Création des repositories sur GitHub/GitLab
  - [x] Configuration effective des branches principales (`main`, `develop`) pour le backend
  - [x] Configuration effective des branches principales (`main`, `develop`) pour le frontend
  - [ ] Mise en place effective des templates de PR et règles de protection des branches
  - [x] Configuration des fichiers `.gitignore` pour frontend et backend
  - [x] Documentation détaillée des protections de branches dans `docs/branch-protection.md`
  - [x] Initialisation de Git dans le dossier backend
- [ ] Structure de base du projet backend
  - [x] Initialisation du projet NestJS
    - [x] Installation de NestJS CLI
    - [x] Génération du projet avec les options optimales
    - [x] Configuration de base de l'application
  - [x] Configuration technique
    - [x] Configuration de TypeScript (`tsconfig.json`)
    - [x] Configuration d'ESLint et Prettier
    - [x] Configuration de Jest pour les tests
    - [ ] Mise en place de Husky et lint-staged pour les hooks Git
  - [ ] Mise en place de la structure de dossiers modulaire
    - [x] Organisation des modules principaux (auth, users, etc.)
    - [ ] Configuration des dossiers communs (common, config)
      - [x] Création des fichiers de configuration de base (app.config.ts, database.config.ts, jwt.config.ts)
      - [⏳] Création des éléments communs (guards, filters, interceptors, etc.)
        - [x] Documentation détaillée du plan d'implémentation dans `docs/common-elements-implementation-plan.md`
        - [x] Implémentation de la structure de dossiers pour les éléments communs
        - [x] Implémentation des constantes de base (AppConstants, RolesConstants, ErrorCodesConstants, ValidationConstants)
        - [x] Implémentation des interfaces fondamentales (ApiResponse, PaginatedResult, ServiceResponse)
        - [x] Implémentation des utilitaires de base (SecurityUtils, DateUtils, PaginationUtils)
        - [x] Implémentation des décorateurs (Public, Roles, CurrentUser, Timeout)
        - [x] Implémentation des guards (RolesGuard, PublicEndpointGuard)
        - [x] Implémentation des interceptors
          - [x] timeout.interceptor.ts - Pour gérer les timeouts de requêtes
          - [ ] transform.interceptor.ts - Pour normaliser les réponses API
          - [ ] logging.interceptor.ts - Pour journaliser les requêtes/réponses
        - [x] Implémentation des filters
          - [x] http-exception.filter.ts - Pour standardiser les réponses d'erreur
          - [x] validation.filter.ts - Pour formater les erreurs de validation
          - [x] index.ts - Fichier d'exportation centralisé pour les filtres
        - [ ] Configuration des pipes et middlewares
        - [ ] Tests unitaires pour tous les éléments communs
    - [ ] Structure de base des services, contrôleurs et entités
  - [x] Installation des dépendances principales
    - [x] NestJS et modules essentiels (@nestjs/typeorm, @nestjs/passport, etc.)
    - [x] Bibliothèques de validation et transformation (class-validator, class-transformer)
    - [x] Outils de sécurité et d'authentification (passport, jwt, bcrypt)
  - [x] Documentation du backend
    - [x] Création d'un README.md détaillé avec instructions d'installation
    - [x] Documentation des conventions de code et structure
- [ ] Configuration de la base de données
  - [x] Choix entre MongoDB ou PostgreSQL
  - [ ] Mise en place du schéma de base de données
  - [ ] Configuration de l'ORM/ODM (TypeORM, Prisma)
- [ ] Mise en place du système d'authentification de base
  - [x] Configuration JWT (création de jwt.config.ts)
  - [ ] Routes d'inscription et de connexion

### Prochaines étapes immédiates
1. ✅ Initialiser Git dans le dossier backend
2. ✅ Configurer effectivement les branches principales (`main`, `develop`) pour le backend
3. ✅ Configurer effectivement les branches principales (`main`, `develop`) pour le frontend selon nos conventions documentées
4. ✅ Initialiser le projet NestJS dans le dossier backend
   - ✅ Installation de NestJS CLI
   - ✅ Génération de la structure de base
   - ✅ Configuration initiale (TypeScript, ESLint, Prettier)
5. ⏳ Mettre en place la structure de dossiers modulaire selon nos spécifications
   - ✅ Création des dossiers principaux (modules, common, config)
   - ✅ Création des fichiers de configuration de base (app.config.ts, database.config.ts, jwt.config.ts)
   - ✅ Création des modules principaux (auth, users, etc.)
   - ⏳ Adaptation des imports dans les fichiers existants
6. ✅ Installer et configurer les dépendances essentielles
   - ✅ Dépendances principales de NestJS et modules complémentaires
   - ✅ Bibliothèques de validation et transformation
   - ✅ Outils de sécurité et d'authentification
7. ⏳ Compléter la création des éléments communs selon le plan détaillé
   - ⏳ Implémentation par étapes des composants selon `docs/common-elements-implementation-plan.md`
     - ✅ Étape 1: Structure de base et utilitaires
       - ✅ Implémentation des constantes (app, roles, error-codes, validation)
       - ✅ Implémentation des interfaces fondamentales (api-response, paginated-result, service-response)
       - ✅ Implémentation des utilitaires de base (security, date, pagination)
     - ✅ Étape 2: Éléments d'authentification et d'autorisation
       - ✅ Implémentation des décorateurs (Public, Roles, CurrentUser, Timeout)
       - ✅ Implémentation des guards (RolesGuard, PublicEndpointGuard)
     - ⏳ Étape 3: Traitement des requêtes et réponses
       - ✅ Implémentation du TimeoutInterceptor pour gérer les timeouts de requêtes
       - ✅ Implémentation des filtres d'exception
         - ✅ HttpExceptionFilter pour standardiser les réponses d'erreur
         - ✅ ValidationFilter pour formater les erreurs de validation de manière structurée
         - ✅ Fichier index.ts pour centraliser les exports
       - [ ] Implémentation des interceptors restants
         - [ ] TransformInterceptor pour normaliser toutes les réponses API
         - [ ] LoggingInterceptor pour journaliser les requêtes et réponses
       - [ ] Configuration du ValidationPipe global avec options avancées
     - ⏳ Étape 4: Éléments avancés et spécifiques
       - [ ] Implémentation des middleware (logger, cors)
       - [ ] Implémentation des pipes spécifiques (ParseObjectIdPipe, SanitizePipe)
       - [ ] Implémentation des filtres spécifiques (DatabaseExceptionFilter)
     - ⏳ Étape 5: Tests et documentation
       - [ ] Tests unitaires pour tous les éléments communs
       - [ ] Documentation d'utilisation et d'intégration
       - [ ] Guide d'exemples pour main.ts
8. ⏳ Configurer Husky et lint-staged pour les hooks Git pre-commit
   - Installation des packages
   - Configuration dans package.json
   - Mise en place des scripts de validation
9. ⏳ Créer les repositories sur GitHub/GitLab
10. ⏳ Mettre en place les règles de protection des branches selon notre documentation
11. ⏳ Configurer l'ORM TypeORM pour PostgreSQL

## Phase 2: Authentification et profils utilisateurs

### Frontend - Interface d'inscription et de connexion
*Documentation détaillée disponible dans `docs/frontend-authentication-implementation.md`*

- [x] Préparation de l'environnement et configuration (1-2 jours)
  - [x] Installation des dépendances requises
    - [x] React Navigation pour la gestion des écrans d'authentification
    - [x] React Hook Form pour les formulaires (choisi à la place de Formik)
    - [x] Yup pour la validation des formulaires
    - [x] Axios pour les requêtes API
    - [x] AsyncStorage et expo-secure-store pour le stockage local sécurisé
    - [x] React Native Paper comme UI kit pour les composants d'interface
  - [x] Configuration des outils de développement
    - [x] ESLint et Prettier avec règles pour React Native
    - [x] Configuration de TypeScript pour les types d'authentification
      - [x] Création de la structure de dossiers pour les types (models, api, forms)
      - [x] Implémentation des types de modèles de base (User, Host, Cleaner, AuthTokens)
      - [x] Implémentation des types d'API (requests, responses)
      - [x] Implémentation des types de formulaires multi-étapes
      - [x] Création des schémas de validation Yup
      - [x] Ajout des utilitaires de manipulation de types (type guards)
      - [x] *Documentation détaillée disponible dans `docs/typescript-auth-types-implementation.md`*
  - [⏳] Mise en place de la structure de dossiers
    - [⏳] Création des dossiers pour composants, services, contextes et utilitaires
      - [x] Structure de base des dossiers composants (auth, common, host, cleaner)
      - [x] Mise en place du dossier constants avec thème et configuration API
      - [⏳] Configuration des dossiers services (api, storage)
      - [⏳] Configuration des dossiers utilitaires et hooks
    - [⏳] Configuration des fichiers de base pour les composants réutilisables

- [✅] Écran d'accueil avec choix de type d'utilisateur (2-3 jours)
  - [x] Dépendances spécifiques
    - [x] react-native-reanimated pour les animations fluides (déjà installé)
    - [x] @expo/vector-icons pour les icônes des cartes (déjà installé)
    - [x] Configuration du fichier babel.config.js pour Reanimated (déjà configuré)
  
  - [x] Préparation du système de thème (0.5 jour)
    - [x] Définition de la palette de couleurs dans constants/theme.ts (primaire pour hébergeurs, secondaire pour professionnels)
    - [x] Configuration du système de typographie (tailles, poids, hauteurs de ligne)
    - [x] Définition des constantes d'espacement (SPACING) et rayons de bordure (BORDER_RADIUS)
    - [x] Création de types TypeScript pour les propriétés des composants réutilisables dans `docs/welcome-screen-types.md`
  
  - [✅] Développement des composants modulaires selon l'architecture définie (1 jour)
    - [x] Structure des types de composants
      - [x] Création de la structure de dossiers `/types/components/auth`
      - [x] Implémentation des interfaces pour les props des composants dans `welcome-screen.types.ts`
      - [x] Documentation complète des interfaces dans `docs/welcome-screen-types.md`
      
    - [✅] Implémentation progressive des composants
      - [x] WelcomeHeader
        - [x] Intégration du logo (utilisation d'une icône Feather comme placeholder temporaire)
        - [x] Styles adaptés aux différentes tailles d'écran avec utilisation du système de thème
        - [x] Props pour personnalisation (logoSize)
        - [x] Attributs d'accessibilité pour lecteurs d'écran
      
      - [x] AppDescription
        - [x] Mise en page du titre accrocheur et de la description
        - [x] Intégration d'une illustration temporaire (utilisation d'icônes Material Community)
        - [x] Props pour personnalisation du contenu (title, description)
        - [x] Styles responsifs avec le système de thème
      
      - [x] UserTypeCard
        - [x] Implémentation de la carte interactive avec icône, titre et description
        - [x] Animation de scale avec Reanimated (feedback tactile)
        - [x] Différenciation visuelle selon le type d'utilisateur (HOST/CLEANER)
          - [x] Utilisation de la couleur primaire (#4361EE) pour les hébergeurs
          - [x] Utilisation de la couleur secondaire (#3F37C9) pour les professionnels
        - [x] Attributs d'accessibilité (accessibilityLabel, accessibilityRole)
      
      - [x] UserTypeSelection
        - [x] Conteneur pour les deux cartes de type d'utilisateur
        - [x] Espacement et mise en page responsive
        - [x] Gestion des callbacks de sélection
      
      - [x] AuthNavigationLink
        - [x] Lien "Déjà inscrit ? Se connecter" avec styles appropriés
        - [x] Gestion de l'état "pressed" pour feedback visuel (opacité réduite)
        - [x] Attributs d'accessibilité pour lecteurs d'écran
      
      - [x] FooterLinks
        - [x] Liens vers les conditions d'utilisation et politique de confidentialité
        - [x] Mise en page responsive avec flexbox (disposition horizontale)
        - [x] Séparateur visuel entre les liens (•)
        - [x] Attributs d'accessibilité pour lecteurs d'écran
        - [x] Zone tactile suffisamment grande pour une bonne accessibilité
  
  - [✅] Assemblage et animations (0.5 jour)
    - [✅] Intégration des composants dans l'écran principal (app/index.tsx)
    - [✅] Mise en place de la ScrollView pour accommoder les écrans plus petits
    - [✅] Configuration du StatusBar et SafeAreaView
    - [✅] Implémentation des animations séquentielles d'entrée
      - [✅] FadeIn pour le WelcomeHeader
      - [✅] FadeInDown séquentiel pour les autres composants
      - [✅] Timing et durées appropriés
    
  - [✅] Navigation et intégration (0.5 jour)
    - [✅] Configuration des routes dans Expo Router
    - [✅] Implémentation des handlers de navigation
      - [✅] Redirection vers /register/host ou /register/cleaner selon choix
      - [✅] Gestion du lien vers l'écran de connexion
    - [✅] Tests manuels des transitions entre écrans
  
  - [✅] Tests et accessibilité (0.5 jour)
    - [✅] Tests unitaires des composants principaux
      - [✅] Test du rendu correct avec différentes props
      - [✅] Test des callbacks et interactions
    - [✅] Tests d'intégration de l'écran complet
      - [✅] Vérification du flux de navigation
      - [✅] Test des animations
    - [✅] Vérification de l'accessibilité
      - [✅] Contraste suffisant pour le texte et les éléments interactifs
      - [✅] Tailles de cible tactile d'au moins 44×44 points
      - [✅] Fonctionnement correct avec VoiceOver/TalkBack
      - [✅] Labels d'accessibilité descriptifs pour tous les éléments interactifs
    - [✅] Tests sur différentes tailles d'écran
      - [✅] Vérification sur téléphones petits, moyens et grands
      - [✅] Vérification de l'adaptabilité sur tablettes

- [⏳] Formulaire d'inscription pour hébergeurs (4,5 jours)
  - [✅] Préparation (0,5 jour)
    - [✅] Création du fichier principal `app/(auth)/register/host.tsx`
    - [✅] Création de la structure de dossiers pour les composants
    - [✅] Définition des types et schémas de validation
    - [✅] Configuration du routage et la navigation
  
  - [✅] Développement des composants de formulaire réutilisables (1 jour)
    - [✅] Développement de composants de base
      - [✅] `FormInput` - Composant champ de texte réutilisable avec styles, label et gestion des erreurs
        - [✅] Support pour les icônes de début et de fin
        - [✅] Gestion des champs optionnels
        - [✅] Intégration avec React Hook Form via Controller
        - [✅] Support pour tous les types de clavier et options de TextInput
      
      - [✅] `FormPasswordInput` - Extension de FormInput pour les mots de passe
        - [✅] Toggle de visibilité du mot de passe
        - [✅] Configuration sécurisée par défaut (autoComplete="off", textContentType="oneTimeCode")
        - [✅] Support pour l'indicateur de force du mot de passe (préparation)
      
      - [✅] `FormCheckbox` - Composant de case à cocher pour les acceptations de conditions
        - [✅] Support pour contenus riches dans les labels (liens cliquables)
        - [✅] Animation de transition entre états (checked/unchecked)
        - [✅] Gestion des erreurs de validation 
        - [✅] Support complet pour l'accessibilité
      
      - [✅] `FormButton` - Bouton standardisé pour la navigation entre étapes
        - [✅] Variantes: primaire, secondaire, outline, text
        - [✅] Tailles: small, medium, large
        - [✅] États: normal, désactivé, chargement
        - [✅] Support pour icônes de début et de fin
        - [✅] Options de largeur adaptative ou complète
      
      - [✅] `FormError` - Composant d'affichage des erreurs générales
        - [✅] Support pour différents niveaux de gravité (error, warning, info)
        - [✅] Affichage d'un ou plusieurs messages
        - [✅] Style visuel distinctif avec icône
        - [✅] Intégration avec les rôles d'accessibilité (alert)
      
    - [✅] Fichier d'export centralisé `index.ts` pour tous les composants
      - [✅] Configuration des exports nommés pour tous les composants
      - [✅] Documentation des imports recommandés
  
  - [✅] Implémentation de la structure de base du formulaire (0,5 jour)
    - [✅] Création du composant principal `RegisterHostForm`
      - [✅] Configuration de React Hook Form avec schémas de validation
      - [✅] Gestion des étapes du formulaire avec état local
      - [✅] Structure pour la navigation entre étapes
      - [✅] Préparation pour l'intégration API
      
    - [✅] Création du composant `FormStepIndicator`
      - [✅] Indicateurs visuels pour les étapes (complétées, active, futures)
      - [✅] Support pour labels personnalisés
      - [✅] Adaptabilité responsive
      - [✅] Accessibilité pour les lecteurs d'écran
  
  - [✅] Implémentation des étapes individuelles (2 jours)
    - [✅] Étape 1: Informations personnelles (0,5 jour)
      - [✅] Création du composant `PersonalInfoStep`
      - [✅] Implémentation des champs avec validation (prénom, nom, email, téléphone)
      - [✅] Connexion avec le formulaire principal
      
    - [✅] Étape 2: Informations professionnelles (0,5 jour)
      - [✅] Création du composant `ProfessionalInfoStep`
      - [✅] Implémentation des champs avec validation conditionnelle (société, SIRET)
      - [✅] Connexion avec le formulaire principal
      
    - [✅] Étape 3: Adresse (0,5 jour)
      - [✅] Création du composant `AddressStep`
      - [✅] Implémentation des champs avec validation (adresse, code postal, ville)
      - [✅] Connexion avec le formulaire principal
      
    - [✅] Étape 4: Création de mot de passe (0,5 jour)
      - [✅] Création du composant `PasswordStep`
      - [✅] Création du composant `PasswordStrengthIndicator`
      - [✅] Implémentation des champs avec validation (mot de passe, confirmation)
      - [✅] Connexion avec le formulaire principal
      
    - [✅] Étape 5: Récapitulatif et conditions (0,5 jour)
      - [✅] Création du composant `SummaryStep`
      - [✅] Implémentation de l'affichage des données récapitulatives
      - [✅] Implémentation des cases à cocher des conditions
      - [✅] Connexion avec le formulaire principal
      
  - [❌] Finalisation et intégration API (0,5 jour)
    - [❌] Intégration complète du formulaire avec navigation et validation
    - [❌] Intégration avec les services API 
    - [❌] Gestion des erreurs API
    - [❌] Implémentation de la redirection post-inscription
      
  - [❌] Tests et optimisations (0,5 jour)
    - [❌] Test de toutes les validations
    - [❌] Test du flux complet d'inscription
    - [❌] Vérification de la responsivité sur différentes tailles d'écran
    - [❌] Optimisation des performances si nécessaire
  
  - [ ] Critères d'acceptation
    - [ ] Tous les champs obligatoires sont validés avant de passer à l'étape suivante
    - [ ] Le formulaire conserve les données lors de la navigation entre les étapes
    - [ ] Les messages d'erreur sont clairs et spécifiques
    - [ ] L'indicateur de progression reflète correctement l'étape actuelle
    - [ ] L'inscription est possible uniquement si toutes les validations sont passées
    - [ ] Les erreurs API sont affichées de manière compréhensible
    - [ ] L'utilisateur est redirigé vers la page de confirmation après inscription réussie

### Prochaines étapes prioritaires pour le formulaire d'inscription hébergeur

1. ✅ Implémenter les composants individuels pour chaque étape du formulaire :
   - ✅ `PersonalInfoStep` (Étape 1) avec tous les champs requis
   - ✅ `ProfessionalInfoStep` (Étape 2)
   - ✅ `AddressStep` (Étape 3)
   - ✅ `PasswordStep` (Étape 4)
   - ✅ `SummaryStep` (Étape 5)

2. ⏳ Finaliser l'intégration API et la soumission du formulaire :
   - ⏳ Développer le service API pour l'authentification (authService)
   - ⏳ Implémenter la méthode registerHost dans ce service
   - ⏳ Connecter le formulaire au service API dans handleSubmit
   - ⏳ Gérer les différents cas d'erreur (email déjà utilisé, erreur serveur, etc.)
   - ⏳ Implémenter la redirection vers la page de confirmation après inscription réussie

3. ⏳ Tester le formulaire complet :
   - ⏳ Vérifier toutes les validations individuelles des champs
   - ⏳ Tester le flux complet avec la navigation entre étapes
   - ⏳ Valider le comportement de désactivation conditionnelle des boutons
   - ⏳ Vérifier la soumission du formulaire et les états de chargement
   - ⏳ Tester la gestion des erreurs API

4. ⏳ Optimiser l'expérience utilisateur :
   - ⏳ Implémenter l'indicateur visuel de force du mot de passe
   - ⏳ Ajouter des animations de transition entre les étapes
   - ⏳ Améliorer la responsivité sur différentes tailles d'écran
   - ⏳ Optimiser les performances (notamment le chargement des étapes)

- [ ] Formulaire d'inscription pour professionnels de ménage (3-4 jours)
  - [ ] Création des composants de formulaire multi-étapes
    - [ ] Étape 1: Informations personnelles (comme pour hébergeurs)
    - [ ] Étape 2: Informations professionnelles (statut, société, SIRET)
    - [ ] Étape 3: Adresse complète
    - [ ] Étape 4: Préférences de travail (rayon de déplacement)
    - [ ] Étape 5: Informations bancaires (IBAN/BIC) avec sécurité renforcée
    - [ ] Étape 6: Création de mot de passe et validation
    - [ ] Étape 7: Acceptation des conditions et récapitulatif
  - [ ] Validation des données
    - [ ] Validation spécifique pour SIRET et coordonnées bancaires
    - [ ] Validation conditionnelle (ex: nom de société obligatoire selon statut)
  - [ ] Navigation entre les étapes (similaire à l'inscription hébergeur)
  - [ ] Soumission du formulaire
    - [ ] Sécurisation des données sensibles
    - [ ] Gestion des erreurs spécifiques aux professionnels

- [ ] Écran de connexion (1-2 jours)
  - [ ] Création des composants
    - [ ] LoginForm avec email, mot de passe et "Se souvenir de moi"
    - [ ] Liens vers inscription et mot de passe oublié
  - [ ] Validation et soumission
    - [ ] Validation des champs de connexion
    - [ ] Gestion des erreurs d'authentification
    - [ ] Stockage sécurisé des tokens
  - [ ] Redirection après connexion
    - [ ] Détection du type d'utilisateur
    - [ ] Navigation vers le tableau de bord approprié

- [ ] Interface de réinitialisation de mot de passe (1-2 jours)
  - [ ] Écran de demande de réinitialisation
    - [ ] Formulaire avec email
    - [ ] Écran de confirmation d'envoi
  - [ ] Écran de création de nouveau mot de passe
    - [ ] Validation du token de réinitialisation
    - [ ] Formulaire de nouveau mot de passe avec confirmation
    - [ ] Écran de confirmation de succès

- [ ] Écran de vérification d'email (1 jour)
  - [ ] Page de confirmation post-inscription
  - [ ] Page de redirection après clic sur le lien d'email
  - [ ] Gestion des différents états (succès, échec, déjà vérifié)

- [⏳] Gestion d'état et services (2-3 jours)
  - [⏳] Mise en place de Zustand pour l'état d'authentification
    - [⏳] Définition des actions et du store
    - [ ] Intégration dans l'application
  - [⏳] Service API pour l'authentification
    - [⏳] Fonctions pour inscription, connexion, réinitialisation, etc.
    - [⏳] Intercepteurs pour les tokens JWT
    - [ ] Gestion automatique du rafraîchissement des tokens
  - [⏳] Service de stockage
    - [⏳] Stockage sécurisé des tokens
    - [ ] Persistance des préférences utilisateur

- [ ] Tests et finalisation (2-3 jours)
  - [ ] Tests unitaires
    - [ ] Validation des formulaires
    - [ ] Comportement des composants
  - [ ] Tests d'intégration
    - [ ] Cycle complet d'inscription/connexion
    - [ ] Gestion des erreurs
  - [ ] Tests de compatibilité
    - [ ] Vérification sur iOS et Android
    - [ ] Test sur différentes tailles d'écran
  - [ ] Optimisations
    - [ ] Performance des formulaires
    - [ ] Gestion de la mémoire
    - [ ] Accessibilité

### Workflow Git pour l'écran d'accueil
- Branche de fonctionnalité: `feature/2-welcome-screen`
- Structure des commits:
  - `feat: implement WelcomeHeader component`
  - `feat: implement AppDescription component`
  - `feat: implement UserTypeCard with animations`
  - `feat: implement UserTypeSelection component`
  - `feat: implement AuthNavigationLink component`
  - `feat: implement FooterLinks component`
  - `feat: assemble welcome screen with animations`
  - `test: add unit tests for welcome screen components`
  - `docs: update README with welcome screen implementation details`
- Pull Request:
  - Titre: "Implement Welcome Screen with User Type Selection"
  - Description détaillée avec captures d'écran
  - Demande de revue par au moins un développeur front-end
  - Checklist complète (tests, accessibilité, performances)

### Workflow Git pour le formulaire d'inscription hébergeur
- Branche de fonctionnalité: `feature/2-host-registration-form`
- Structure des commits:
  - `feat: setup structure for host registration form`
  - `feat: implement FormStepIndicator component`
  - `feat: implement reusable form components (Input, Button, etc.)`
  - `feat: implement PersonalInfoStep component`
  - `feat: implement ProfessionalInfoStep component`
  - `feat: implement AddressStep component`
  - `feat: implement PasswordStep with strength indicator`
  - `feat: implement SummaryStep component`
  - `feat: connect form to API service`
  - `test: add validation tests for host registration form`
  - `style: improve responsive layout of registration steps`
  - `docs: update documentation for host registration process`
- Pull Request:
  - Titre: "Implement Multi-Step Registration Form for Hosts"
  - Description détaillée avec captures d'écran de chaque étape
  - Demande de revue par au moins un développeur front-end
  - Checklist complète (tests, accessibilité, performances, sécurité)

### Backend - Module d'authentification
  - [x] Structure du module Auth
    - [x] Création de la structure de dossiers selon les spécifications techniques
    - [x] Mise en place de auth.module.ts avec les imports nécessaires
    - [ ] Définition des providers et exports du module
  
  - [x] DTOs pour la validation et les transferts de données
    - [x] LoginDto pour la connexion
    - [x] RegisterHostDto pour l'inscription des hébergeurs
    - [x] RegisterCleanerDto pour l'inscription des professionnels de ménage
    - [x] ForgotPasswordDto pour la demande de réinitialisation
    - [x] ResetPasswordDto pour la réinitialisation du mot de passe
    - [x] AuthTokensDto pour la structure des tokens
    - [x] AuthResponseDto pour les réponses d'authentification standardisées
  
  - [x] Stratégies d'authentification (avec Passport)
    - [x] JWT Strategy pour la validation des tokens d'accès
    - [x] Local Strategy pour l'authentification par identifiants
    - [x] Refresh Token Strategy pour le rafraîchissement des tokens
  
  - [x] Guards pour la protection des routes
    - [x] JwtAuthGuard pour la protection des routes authentifiées
    - [x] LocalAuthGuard pour le processus de connexion
    - [x] RolesGuard pour la restriction basée sur les rôles
    - [x] RefreshTokenGuard pour la route de rafraîchissement des tokens

  - [x] Décorateurs d'authentification
    - [x] Public pour marquer les routes publiques
    - [x] Roles pour définir les restrictions d'accès par type d'utilisateur
  
  - [ ] Service d'authentification (auth.service.ts)
    - [ ] Méthode d'inscription avec logique distincte pour les deux types d'utilisateurs
    - [ ] Méthode de connexion avec génération de tokens JWT
    - [ ] Méthode de validation des utilisateurs
    - [ ] Méthode de rafraîchissement des tokens
    - [ ] Méthodes pour la vérification d'email
    - [ ] Fonctionnalités de réinitialisation de mot de passe
    - [ ] Utilitaires pour le hachage et la comparaison des mots de passe (bcrypt)
  
  - [ ] Contrôleur d'authentification (auth.controller.ts)
    - [ ] Endpoint d'inscription pour hébergeurs (/auth/register/host)
    - [ ] Endpoint d'inscription pour professionnels (/auth/register/cleaner)
    - [ ] Endpoint de connexion (/auth/login)
    - [ ] Endpoint de rafraîchissement des tokens (/auth/refresh-token)
    - [ ] Endpoint de vérification d'email (/auth/verify-email/:token)
    - [ ] Endpoints pour la réinitialisation de mot de passe
    - [ ] Endpoint de récupération du profil utilisateur (/auth/profile)
  
  - [ ] Tests du module Auth
    - [ ] Tests unitaires pour auth.service.ts
    - [ ] Tests d'intégration pour auth.controller.ts
    - [ ] Tests des stratégies et guards

### Backend - Gestion des utilisateurs
  - [x] Architecture du module Users
    - [x] Création de la structure de dossiers selon les spécifications techniques
    - [x] Documentation technique détaillée dans `docs/technical/users-module-implementation.md`
    - [x] Définition des relations entre entités (User, Host, Cleaner)
  
  - [x] Modèles/Schémas utilisateurs
    - [x] Conception de l'entité de base User
    - [x] Conception de l'entité Host (hébergeur)
    - [x] Conception de l'entité Cleaner (professionnel)
    - [x] Définition des relations one-to-one entre entités
    - [ ] Implémentation des entités avec TypeORM
  
  - [x] DTOs pour la validation et transfert de données
    - [x] Conception des DTOs pour création (CreateHostDto, CreateCleanerDto)
    - [x] Conception des DTOs pour mise à jour (UpdateHostDto, UpdateCleanerDto)
    - [x] Conception des DTOs pour les réponses (UserResponseDto)
    - [ ] Implémentation des DTOs avec class-validator
  
  - [x] Service utilisateurs (users.service.ts)
    - [x] Conception des méthodes de création d'utilisateurs (createHost, createCleaner)
    - [x] Conception des méthodes de recherche (findById, findByEmail, findAllHosts, findAllCleaners)
    - [x] Conception des méthodes de mise à jour (updateHost, updateCleaner)
    - [x] Conception des méthodes de vérification (verifyEmail, verifyIdentity)
    - [x] Conception des méthodes de gestion du cycle de vie (deactivateUser, deleteUser)
    - [ ] Implémentation du service avec injection des repositories
  
  - [x] Contrôleur utilisateurs (users.controller.ts)
    - [x] Conception des endpoints REST
    - [x] Protection des routes avec guards d'authentification
    - [x] Restriction d'accès basée sur les rôles
    - [ ] Implémentation du contrôleur avec décorateurs NestJS
  
  - [x] Mécanismes de sécurité pour données sensibles
    - [x] Conception du système de chiffrement des données bancaires
    - [x] Conception du système de journalisation des accès aux données sensibles
    - [x] Conformité RGPD (droit à l'oubli, droit d'accès)
    - [ ] Implémentation du service de chiffrement
  
  - [x] Repositories personnalisés si nécessaire
    - [x] Conception du HostRepository pour requêtes complexes
    - [x] Conception du CleanerRepository pour requêtes complexes
    - [ ] Implémentation des repositories
  
  - [ ] Tests du module Users
    - [ ] Tests unitaires pour users.service.ts
    - [ ] Tests d'intégration pour users.controller.ts
    - [ ] Tests des repositories
  
### Frontend - Profils utilisateurs
  - [ ] Interface de profil hébergeur
    - [ ] Affichage des informations personnelles
    - [ ] Affichage des informations professionnelles
    - [ ] Section informations de vérification
  - [ ] Interface de profil professionnel de ménage
    - [ ] Affichage des informations personnelles et professionnelles
    - [ ] Affichage du statut de vérification
    - [ ] Affichage sécurisé des informations bancaires
    - [ ] Section préférences de travail
  - [ ] Édition des informations de profil
    - [ ] Formulaires d'édition pour chaque section
    - [ ] Validation des modifications
    - [ ] Gestion des erreurs API
  
### Sécurité du système d'authentification
  - [ ] Implémentation du rate limiting pour prévenir les attaques par force brute
    - [ ] Configuration des limites de tentatives
    - [ ] Mise en place des délais progressifs
  - [ ] Validation stricte des entrées utilisateur avec class-validator
    - [ ] Règles de validation complexes
    - [ ] Sanitization des données
  - [ ] Logs d'audit pour les actions d'authentification
    - [ ] Enregistrement des connexions/déconnexions
    - [ ] Journalisation des modifications de profil
  - [ ] Expiration et invalidation appropriées des tokens
    - [ ] Gestion de la liste noire des tokens
    - [ ] Invalidation lors de changement de mot de passe
  
### Intégration et cycle de vie du développement
  - [ ] Points d'intégration frontend-backend
    - [ ] Synchronisation des formats de données
    - [ ] Tests d'intégration bout en bout
  - [ ] Documentation technique
    - [ ] Documentation des API d'authentification
    - [ ] Guide d'utilisation des composants
  
### Workflow Git pour cette phase
  - [ ] Branches de fonctionnalités: `feature/2-user-auth`, `feature/2-profile-management`, `feature/2-auth-security`
  - [ ] Tests et revue de code pour chaque PR
  - [ ] Fusion dans `develop` après validation

## Phase 3: Tableau de bord et navigation
- [⏳] Frontend - Mise en place d'Expo Router 
  - [⏳] Création de la structure de base du dossier `app/`
    - [x] Structure initiale avec groupes de routes (auth), (host), (cleaner)
    - [⏳] Complétion des fichiers manquants dans ces groupes
  - [⏳] Configuration des layouts principaux avec les fichiers `_layout.tsx`
    - [x] Layout racine (app/_layout.tsx)
    - [⏳] Layouts pour chaque groupe de routes
  - [⏳] Mise en place des groupes de routes avec parenthèses
    - [x] Groupe `(auth)` pour les routes d'authentification
    - [x] Groupe `(host)` pour le tableau de bord hébergeur
    - [x] Groupe `(cleaner)` pour le tableau de bord professionnel
  - [ ] Implémentation des navigateurs à onglets pour les tableaux de bord
    - [ ] Onglets hébergeur: Accueil, Annonces publiées, Messages, Notifications, Profil
    - [ ] Onglets professionnel: Recherches, Réservations, Messages, Notifications, Profil, Facturation
  - [ ] Mise en place du système de protection des routes
    - [ ] Hook personnalisé `useProtectedRoute`
    - [ ] Redirection conditionnelle selon l'état d'authentification
    - [ ] Gestion des permissions selon le type d'utilisateur
- [ ] Frontend - Écrans et composants du tableau de bord
  - [ ] Création des écrans de base pour chaque route dynamique
  - [ ] Développement des composants partagés (cartes, listes, boutons)
  - [ ] Mise en place des écrans vides pour chaque onglet
  - [ ] Composants de navigation (en-têtes, barre d'onglets personnalisée)
- [ ] Frontend - Intégration avec l'API
  - [ ] Service de récupération des données du tableau de bord
  - [ ] Gestion d'état avec Zustand pour chaque type de tableau de bord
  - [ ] Gestion des chargements et erreurs API
- [ ] Backend - Structure API pour tableaux de bord
  - [ ] Endpoints pour récupérer les données du tableau de bord
  - [ ] Endpoints pour les notifications
  - [ ] Endpoints pour le planning (professionnel de ménage)
  - [ ] Filtrage et pagination des données
- [ ] **Workflow Git pour cette phase**:
  - [ ] Branches de fonctionnalités: `feature/3-expo-router-setup`, `feature/3-dashboard-components`, `feature/3-api-endpoints`
  - [ ] Documentation du système de navigation dans le README et les composants

## Phase 4: Gestion des annonces (hébergeurs)
- [ ] Frontend - Création d'annonces
  - [ ] Formulaire de création d'annonce (tous les champs requis)
  - [ ] Calcul automatique du prix
  - [ ] Interface de prévisualisation de l'annonce
- [ ] Backend - Gestion des annonces
  - [ ] Modèle/Schéma pour les annonces
  - [ ] Endpoints pour créer/modifier/supprimer des annonces
  - [ ] Logique de calcul de prix
- [ ] Frontend - Liste des annonces publiées
  - [ ] Interface pour voir et gérer les annonces existantes
  - [ ] Filtres et recherche
- [ ] Backend - Requêtes d'annonces
  - [ ] Endpoints pour lister/filtrer les annonces publiées par un hébergeur
  - [ ] Gestion des états d'annonce (active, pourvue, terminée, etc.)

## Phase 5: Recherche et candidature (professionnels de ménage)
- [ ] Frontend - Interface de recherche
  - [ ] Développement de l'interface de swipe pour les annonces
  - [ ] Affichage des détails d'annonce
  - [ ] Système de filtre basé sur les préférences utilisateur
- [ ] Backend - Système de recherche
  - [ ] Endpoints de recherche d'annonces
  - [ ] Filtrage basé sur les préférences de l'utilisateur
  - [ ] Algorithme de correspondance
- [ ] Frontend - Système de candidature
  - [ ] Interface pour postuler à une annonce
  - [ ] Affichage des candidatures en cours
- [ ] Backend - Gestion des candidatures
  - [ ] Modèle/Schéma pour les candidatures
  - [ ] Endpoints pour créer/récupérer/annuler des candidatures
  - [ ] Système de notifications pour les hébergeurs

## Phase 6: Système de messagerie
- [ ] Frontend - Interface de messagerie
  - [ ] Liste des conversations
  - [ ] Interface de conversation individuelle
  - [ ] Indicateurs de messages non lus
- [ ] Backend - Gestion des messages
  - [ ] Modèle/Schéma pour les messages et conversations
  - [ ] Endpoints API pour la messagerie
  - [ ] Stockage et récupération des messages
- [ ] Intégration des notifications
  - [ ] Notifications push pour nouveaux messages
  - [ ] Badges de notification dans l'application

## Phase 7: Système de paiement et gestion des missions
- [ ] Backend - Intégration de la passerelle de paiement
  - [ ] Configuration de Stripe ou équivalent
  - [ ] Endpoints pour la gestion des paiements
  - [ ] Gestion des commissions (15%)
- [ ] Frontend - Interface de paiement
  - [ ] Écran de paiement sécurisé
  - [ ] Affichage des détails de transaction
- [ ] Backend - Gestion des missions
  - [ ] Modèle/Schéma pour les missions (passage de l'annonce à la mission confirmée)
  - [ ] Endpoints pour la gestion du cycle de vie des missions
  - [ ] Logique de déblocage de paiement après délai
- [ ] Frontend - Suivi des missions
  - [ ] Planning pour les professionnels de ménage
  - [ ] Interface de suivi pour les hébergeurs
  - [ ] Notifications de mission
- [ ] **Aspects de sécurité Git**:
  - [ ] Revues de code obligatoires par expert en sécurité pour le code lié aux paiements
  - [ ] Validation supplémentaire pour les modifications affectant les données bancaires
  - [ ] Hooks pre-commit spécifiques pour détecter les fuites d'informations sensibles

## Phase 8: Système de tâches et validation de mission
- [ ] Backend - Gestion des tâches
  - [ ] Modèle/Schéma pour les listes de tâches par mission
  - [ ] Endpoints pour la création et validation des tâches
- [ ] Frontend - Interface de gestion des tâches
  - [ ] Checklist pour le professionnel de ménage
  - [ ] Interface de signalement de problème
  - [ ] Upload de photos pour preuve
- [ ] Backend - Processus de validation
  - [ ] Logique de validation de mission
  - [ ] Gestion des réclamations
  - [ ] Déclenchement des paiements après délai

## Phase 9: Facturation et historique financier
- [ ] Backend - Système de facturation
  - [ ] Génération automatique de factures
  - [ ] Stockage des factures
  - [ ] Endpoints pour récupérer l'historique des factures
- [ ] Frontend - Interface de facturation
  - [ ] Affichage des factures pour les hébergeurs
  - [ ] Affichage des revenus pour les professionnels de ménage
  - [ ] Filtres par période

## Phase 10: Système de notation et d'avis
- [ ] Backend - Gestion des avis
  - [ ] Modèle/Schéma pour les avis et notations
  - [ ] Endpoints pour créer/récupérer les avis
  - [ ] Calcul des moyennes de notation
- [ ] Frontend - Interface d'avis
  - [ ] Formulaire de dépôt d'avis post-mission
  - [ ] Affichage des avis sur les profils
  - [ ] Indicateurs de notation

## Phase 11: Sécurité et performances
- [ ] Audit de sécurité
  - [ ] Revue du code pour vulnérabilités
  - [ ] Sécurisation des données sensibles
  - [ ] Protection CSRF, XSS, etc.
- [ ] Optimisation des performances
  - [ ] Mise en cache côté serveur
  - [ ] Optimisation des requêtes à la base de données
  - [ ] Lazy loading et pagination
- [ ] Tests de charge
  - [ ] Simulation d'utilisation intensive
  - [ ] Identification et correction des goulots d'étranglement
- [ ] **Workflow Git pour cette phase**:
  - [ ] Branches `bugfix/*` pour les problèmes de sécurité identifiés
  - [ ] Revue de code obligatoire pour les correctifs de sécurité
  - [ ] Fusion rapide des correctifs critiques

## Phase 12: Tests, déploiement et finalisation
- [ ] Tests approfondis
  - [ ] Tests unitaires
  - [ ] Tests d'intégration
  - [ ] Tests E2E
  - [ ] Tests utilisateurs
- [ ] Préparation au déploiement
  - [ ] Configuration des environnements de production
  - [ ] Mise en place CI/CD
    - [ ] Configuration des workflows GitHub Actions
    - [ ] Automatisation des tests pour chaque PR
    - [ ] Pipeline de déploiement vers les différents environnements
  - [ ] Documentation API
- [ ] Déploiement
  - [ ] Préparation des branches `release/*` pour le déploiement
  - [ ] Déploiement du backend sur serveur/cloud
  - [ ] Publication de l'application sur App Store / Google Play
  - [ ] Surveillance post-déploiement
  - [ ] Création des tags de version (`v1.0.0`)
- [ ] Finalisation
  - [ ] Formation des premiers utilisateurs
  - [ ] Recueil de feedback initial
  - [ ] Corrections de bugs post-déploiement (via branches `hotfix/*`)

## Méthodologie de développement Git

Pour toutes les phases du projet, la méthodologie suivante sera appliquée:

### Niveaux de protection des branches

#### Branche `main` (production)
- Protection stricte contre les modifications directes
- Minimum de 2 revues de code requises avant fusion
- Validation obligatoire par tous les tests (lint, test, build)
- Commits signés obligatoires
- Restriction des droits de fusion aux lead developers
- Pas de force push ou suppression autorisés

#### Branche `develop` (intégration)
- Protection contre les modifications directes
- Minimum de 1 revue de code requise
- Validation par les tests essentiels (lint, test)
- Nouvelles approbations requises après modifications

#### Branches `release/*`
- Protection intermédiaire avec revue obligatoire
- Tests complets requis avant fusion
- Réservées aux responsables de release

### Workflow pour chaque fonctionnalité
1. Créer une branche de fonctionnalité depuis `develop`: `feature/[phase]-[fonctionnalité]`
2. Développer avec des commits réguliers suivant la convention: `[type]: [description] [#issue]`
3. Créer une Pull Request vers `develop` en utilisant le template standardisé
4. Obtenir les approbations requises après code review
5. S'assurer que tous les tests automatisés passent
6. Merger dans `develop` après validation

### Gestion des bugs et correctifs
- Bugs non critiques: Branche `bugfix/[issue-number]-[description]` depuis `develop`
- Bugs critiques en production: Branche `hotfix/[issue-number]-[description]` depuis `main`
- Pour les hotfixes:
  1. Créer la branche depuis `main`
  2. Développer et tester le correctif
  3. Créer une PR vers `main` avec l'étiquette `hotfix`
  4. Après fusion dans `main`, propager immédiatement le correctif dans `develop`

### Protection spécifique pour code sensible
- Revue de code obligatoire par un expert en sécurité pour:
  - Code lié aux paiements
  - Stockage des données bancaires
  - Traitement des informations personnelles
- Tests de sécurité supplémentaires pour ces composants
- Hooks pre-commit pour détecter les fuites potentielles de données sensibles

### Préparation des releases
1. À la fin de chaque phase majeure, créer une branche `release/vX.Y.Z` depuis `develop`
2. Effectuer les derniers ajustements, tests et corrections
3. Merger dans `main` ET `develop` après validation
4. Tagger la version dans `main`

### Intégration continue (CI)
- Exécution automatique des tests à chaque push
- Analyse statique du code
- Build de test pour chaque PR
- Détection précoce des problèmes d'intégration

## Plan de développement progressif

Pour chaque phase, suivre cette approche:
1. **Planification détaillée**: Préciser les tâches spécifiques et les objectifs
2. **Développement**: Implémenter les fonctionnalités
3. **Tests**: Tester chaque fonctionnalité développée
4. **Revue**: Évaluer la qualité et la cohérence
5. **Intégration**: S'assurer que les nouvelles fonctionnalités s'intègrent correctement avec l'existant
6. **Documentation**: Documenter le code et les fonctionnalités

Cette approche permet de développer l'application de manière itérative, en s'assurant que chaque partie est fonctionnelle avant de passer à la suivante. Les utilisateurs pourront ainsi voir le projet progresser de manière tangible et cohérente.