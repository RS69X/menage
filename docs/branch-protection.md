# Configuration des protections de branches

Ce document décrit la configuration des règles de protection pour les branches principales du projet. Ces règles sont essentielles pour maintenir la qualité et la sécurité du code, particulièrement important pour une application gérant des données sensibles et des transactions financières.

## Pourquoi protéger les branches?

Notre application manipule:
- Des données personnelles (identité, coordonnées)
- Des informations bancaires (RIB, paiements)
- Des informations de géolocalisation
- Des données commerciales sensibles

La protection des branches garantit que:
- Le code en production est toujours stable et validé
- Les modifications sont systématiquement revues
- L'historique des modifications est préservé et traçable
- Les déploiements suivent un processus rigoureux

## Configuration sur GitHub

### Protection de la branche `main`

La branche `main` contient le code en production et nécessite le plus haut niveau de protection.

#### Configuration recommandée:

1. Accédez à `Settings > Branches > Branch protection rules` et cliquez sur `Add rule`
2. Dans "Branch name pattern", entrez `main`
3. Activez les options suivantes:
   - ✅ "Require pull request reviews before merging"
     - Nombre de revues requises: 2
     - ✅ "Dismiss stale pull request approvals when new commits are pushed"
     - ✅ "Require review from Code Owners"
   - ✅ "Require status checks to pass before merging"
     - ✅ "Require branches to be up to date before merging"
     - Statuts requis: `lint`, `test`, `build`
   - ✅ "Require signed commits"
   - ✅ "Include administrators"
   - ✅ "Restrict who can push to matching branches"
     - Ajoutez uniquement les lead developers et le responsable des déploiements
   - ✅ "Allow force pushes" - Désactivé
   - ✅ "Allow deletions" - Désactivé

### Protection de la branche `develop`

La branche `develop` est la branche d'intégration principale et nécessite également une protection robuste.

#### Configuration recommandée:

1. Accédez à `Settings > Branches > Branch protection rules` et cliquez sur `Add rule`
2. Dans "Branch name pattern", entrez `develop`
3. Activez les options suivantes:
   - ✅ "Require pull request reviews before merging"
     - Nombre de revues requises: 1
     - ✅ "Dismiss stale pull request approvals when new commits are pushed"
   - ✅ "Require status checks to pass before merging"
     - ✅ "Require branches to be up to date before merging"
     - Statuts requis: `lint`, `test`
   - ✅ "Include administrators"
   - ✅ "Allow force pushes" - Désactivé
   - ✅ "Allow deletions" - Désactivé

### Protection des branches `release/*`

Les branches de release préparent le code pour la production et nécessitent une vérification approfondie.

#### Configuration recommandée:

1. Accédez à `Settings > Branches > Branch protection rules` et cliquez sur `Add rule`
2. Dans "Branch name pattern", entrez `release/*`
3. Activez les options suivantes:
   - ✅ "Require pull request reviews before merging"
     - Nombre de revues requises: 1
   - ✅ "Require status checks to pass before merging"
     - Statuts requis: `lint`, `test`, `build`
   - ✅ "Include administrators"

## Configuration sur GitLab

### Protection de la branche `main`

1. Accédez à `Settings > Repository > Protected Branches`
2. Sélectionnez `main` dans la liste déroulante
3. Configurez comme suit:
   - Allowed to merge: `Maintainers` (Lead Developers)
   - Allowed to push: `No one`
   - Activez "Require approval from code owners"
   - Code owner approval required: `All`
   - Nombre d'approbations requises: 2

### Protection de la branche `develop`

1. Accédez à `Settings > Repository > Protected Branches`
2. Sélectionnez `develop` dans la liste déroulante
3. Configurez comme suit:
   - Allowed to merge: `Developers + Maintainers`
   - Allowed to push: `No one`
   - Nombre d'approbations requises: 1

### Protection des branches `release/*`

1. Accédez à `Settings > Repository > Protected Branches`
2. Dans "Branch wildcard pattern", entrez `release/*`
3. Configurez comme suit:
   - Allowed to merge: `Maintainers` (Lead Developers)
   - Allowed to push: `Maintainers` (Lead Developers)
   - Nombre d'approbations requises: 1

## Procédures spéciales

### Procédure pour les hotfixes

En cas de bug critique en production nécessitant une correction immédiate:

1. Créez une branche `hotfix/[issue-number]-[description]` à partir de `main`
2. Développez et testez le correctif
3. Créez une Pull Request vers `main` avec l'étiquette `hotfix`
4. Demandez une revue urgente (minimum 1 approbation, idéalement 2)
5. Une fois approuvée et fusionnée dans `main`, créez immédiatement une Pull Request pour merger le correctif dans `develop`

### Gestion des conflicts lors des merges

En cas de conflit lors d'une tentative de merge:

1. Pullez la branche cible (`develop` ou `main`) dans votre branche locale
2. Résolvez les conflits localement
3. Testez que l'application fonctionne correctement après la résolution
4. Commitez et poussez les changements
5. La Pull Request sera automatiquement mise à jour

## Bonnes pratiques additionnelles

- Ne jamais contourner les protections de branches, même en cas d'urgence
- Documenter toute exception dans le ticket d'issue associé
- Toujours créer des branches à partir de la branche appropriée (`develop` pour les features, `main` pour les hotfixes)
- Maintenir les branches à jour avec leur source pour minimiser les conflits
- Limiter la taille des Pull Requests pour faciliter la revue
- Utiliser des commits atomiques avec des messages clairs