# Configuration des outils de développement Frontend

Ce document décrit la configuration des outils de développement (ESLint et Prettier) pour le frontend de l'application. Ces outils garantissent une qualité de code homogène et facilitent la collaboration entre développeurs.

## Outils configurés

### ESLint

ESLint est configuré pour analyser statiquement le code JavaScript/TypeScript et détecter les problèmes potentiels, les erreurs de syntaxe et les mauvaises pratiques.

### Prettier

Prettier est un formateur de code qui garantit une mise en forme cohérente du code selon des règles prédéfinies, éliminant les débats sur le style de code.

## Configuration mise en place

### Fichiers de configuration

- `.eslintrc.js` : Configuration principale d'ESLint
- `.prettierrc.js` : Configuration de Prettier
- `.eslintignore` : Fichiers et dossiers à exclure de l'analyse ESLint
- `.vscode/settings.json` : Configuration de l'intégration avec VSCode

### Conventions de code principales

#### Règles ESLint

Notre configuration ESLint inclut plusieurs ensembles de règles :

- **Règles de base** : Standards généraux de JavaScript/TypeScript
- **Règles React** : Bonnes pratiques spécifiques à React
- **Règles React Hooks** : Validation des règles d'utilisation des Hooks
- **Règles React Native** : Standards propres au développement React Native
- **Règles d'import** : Organisation et ordre des imports
- **Intégration avec Prettier** : Pour éviter les conflits entre les deux outils

#### Format de code (Prettier)

Notre formatage inclut les règles suivantes :
- Simple quotes pour les chaînes (`'example'`)
- Virgules finales systématiques (`trailingComma: 'all'`)
- Largeur de ligne fixée à 100 caractères
- Indentation de 2 espaces
- Points-virgules obligatoires

## Utilisation des outils

### Scripts disponibles

Le fichier `package.json` contient plusieurs scripts pour utiliser ces outils :

- `npm run lint` : Exécute ESLint pour vérifier le code
- `npm run lint:fix` : Corrige automatiquement les problèmes détectables par ESLint
- `npm run format` : Formate tous les fichiers avec Prettier
- `npm run format:check` : Vérifie si le code est correctement formaté sans modifier les fichiers
- `npm run check` : Exécute à la fois la vérification ESLint et Prettier

### Intégration avec VSCode

Un fichier de configuration VSCode (`.vscode/settings.json`) a été ajouté pour :
- Formater automatiquement les fichiers à la sauvegarde avec Prettier
- Appliquer les corrections ESLint à la sauvegarde
- Configurer l'éditeur pour respecter nos conventions

#### Extensions VSCode recommandées

Pour une expérience optimale, installez ces extensions VSCode :
- ESLint (`dbaeumer.vscode-eslint`)
- Prettier (`esbenp.prettier-vscode`)

### Workflow de développement recommandé

1. **Avant de commencer à coder** :
   - Assurez-vous que votre éditeur est correctement configuré
   - Vérifiez que les extensions nécessaires sont installées

2. **Pendant le développement** :
   - Laissez VSCode formater automatiquement votre code à la sauvegarde
   - Consultez les erreurs et avertissements ESLint dans l'éditeur

3. **Avant de committer** :
   - Exécutez `npm run check` pour vous assurer que tout est conforme
   - Corrigez les problèmes restants avec `npm run lint:fix` et `npm run format`

4. **En cas de désaccord avec une règle** :
   - Discutez-en avec l'équipe avant d'ajuster la configuration
   - Documentez toute exception avec des commentaires explicites

## Résolution des problèmes courants

### Conflits entre ESLint et Prettier

Si vous rencontrez des conflits entre ESLint et Prettier, vérifiez que :
- Vous utilisez la dernière version des configurations
- Les extensions VSCode sont à jour
- Vous n'avez pas de configuration locale qui interfère avec celle du projet

### Erreurs qui persistent après correction

Si des erreurs persistent malgré l'utilisation de `lint:fix` :
1. Certaines erreurs nécessitent une correction manuelle
2. Lisez attentivement le message d'erreur pour comprendre ce qui doit être corrigé
3. Consultez la documentation ESLint pour la règle spécifique

### Performance

Si l'analyse ESLint devient lente :
- Assurez-vous que `.eslintignore` exclut correctement les fichiers non pertinents
- Considérez l'utilisation de la mise en cache ESLint (`--cache` flag)

## Évolution de la configuration

Cette configuration peut évoluer au fil du temps. Toute modification devrait :
1. Être discutée en équipe
2. Être documentée dans ce document
3. Être communiquée à tous les développeurs

## Ressources utiles

- [Documentation ESLint](https://eslint.org/docs/user-guide/getting-started)
- [Documentation Prettier](https://prettier.io/docs/en/index.html)
- [Guide des règles ESLint pour React](https://github.com/jsx-eslint/eslint-plugin-react)
- [Guide des règles ESLint pour React Native](https://github.com/Intellicode/eslint-plugin-react-native)