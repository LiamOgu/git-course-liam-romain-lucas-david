# Les Workflows Git

## Introduction

Un workflow Git est un ensemble de recommandations et de processus pour utiliser Git de manière efficace en équipe. Il définit comment les branches sont créées, nommées, fusionnées et supprimées au sein d'un projet.

## Pourquoi un workflow est important ?

- **Collaboration**: Facilite le travail en équipe sans conflits
- **Traçabilité**: Maintient un historique clair et organisé
- **Qualité**: Permet de mettre en place des contrôles de qualité
- **Déploiement**: Automatise les processus de release et de production

---

## 1. Git Flow

### Concept

Git Flow est un workflow rigoureux avec deux branches principales et plusieurs branches de support.

### Branches principales

- **main (master)**: Contient le code stable, prêt pour la production
- **develop**: Branche d'intégration pour les développements

### Branches de support

- **feature/** : Pour développer de nouvelles fonctionnalités
- **release/** : Pour préparer une nouvelle version
- **hotfix/** : Pour corriger les bugs en production

### Flux de travail

```
1. Créer une branche feature à partir de develop
   git checkout -b feature/ma-fonctionnalite develop

2. Développer et commiter
   git add .
   git commit -m "feat: add new feature"

3. Créer une pull request (PR) sur develop
4. Après approbation et test, fusionner dans develop
5. Créer une branche release quand prêt
   git checkout -b release/1.0.0 develop

6. Tester et corriger les bugs si nécessaire
7. Fusionner dans main et develop
8. Taguer la release
   git tag -a v1.0.0 -m "Version 1.0.0"
```

### Avantages
- Structure claire et prévisible
- Gestion explicite des versions

### Inconvénients
- Complexe pour les petites équipes
- Plus de branches à gérer

---

## 2. GitHub Flow

### Concept

GitHub Flow est un workflow simplifié et agile, idéal pour les déploiements continus.

### Branches
- **main**: Contient le code stable
- **feature branches**: Branches temporaires pour chaque fonctionnalité

### Flux de travail

```
1. Créer une branche feature à partir de main
   git checkout -b feature/ma-fonctionnalite

2. Développer localement
   git add .
   git commit -m "feat: description"
   git push origin feature/ma-fonctionnalite

3. Créer une Pull Request sur GitHub

4. Discussion et review du code

5. Une fois approuvée, fusionner dans main
   git checkout main
   git pull origin main
   git merge --no-ff feature/ma-fonctionnalite
   git push origin main

6. Supprimer la branche
   git branch -d feature/ma-fonctionnalite
   git push origin --delete feature/ma-fonctionnalite
```

### Avantages
- Simple et facile à comprendre
- Idéal pour le déploiement continu
- Moins de branches à maintenir

### Inconvénients
- Moins de structure pour les grandes équipes
- Nécessite une CI/CD robuste

---

## 3. GitLab Flow

### Concept

GitLab Flow combine la simplicité de GitHub Flow avec la structure de Git Flow.

### Branches
- **main**: Code de production
- **Branches environnement**: pre-production, staging, etc.
- **Feature branches**: Pour les développements

### Flux de travail

```
1. Créer une branche feature depuis main
   git checkout -b feature/ma-fonctionnalite

2. Développer et faire des commits

3. Créer une Merge Request

4. Une fois approuvée, fusionner dans main
   
5. Le code est automatiquement déployé en staging
   
6. Si les tests passent, déployer manuellement ou automatiquement en production
```

### Avantages
- Flexibilité avec gestion d'environnements
- Plus simple que Git Flow
- Bon pour les déploiements par environnement

---

## 4. Trunk-Based Development

### Concept

Tous les développeurs travaillent sur une seule branche (main/trunk) avec des commits fréquents et des feature flags pour les fonctionnalités incomplètes.

### Flux de travail

```
1. Créer une branche courte durée depuis main
   git checkout -b feature-courte-duree

2. Développer et commiter rapidement (en quelques heures)

3. Fusionner rapidement dans main avec des tests
   git checkout main
   git merge feature-courte-duree

4. Utiliser des feature flags pour les fonctionnalités incomplètes
   if (isFeatureEnabled('NEW_FEATURE')) {
     // Code de la nouvelle fonctionnalité
   }
```

### Avantages
- Peu de conflits de fusion
- Déploiement très fréquent
- Meilleure intégration continue

### Inconvénients
- Nécessite une excellente CI/CD
- Discipline stricte requise
- Complexité des feature flags

---

## Bonnes pratiques communes

### 1. Nommage des branches

Utiliser une convention cohérente :

```
feature/description-de-la-fonctionnalite
bugfix/description-du-bug
hotfix/description-de-la-correction
docs/description-des-docs
refactor/description-du-refactoring
```

### 2. Commits clairs

```
Commandes :
git add .
git commit -m "type: description courte"

Types recommandés :
- feat: Nouvelle fonctionnalité
- fix: Correction de bug
- docs: Modification de documentation
- style: Formatage, pas de logique modifiée
- refactor: Refactorisation du code
- perf: Amélioration de performance
- test: Ajout de tests
```

### 3. Pull Requests efficaces

```
- Garder les PR petites et focalisées
- Écrire une description claire
- Répondre aux commentaires rapidement
- Demander des reviews à des collègues
- Utiliser les checklists
```

### 4. Fusion des branches

```
Utiliser --no-ff pour garder une trace :
git merge --no-ff feature/ma-fonctionnalite

Ou utiliser squash pour un historique propre :
git merge --squash feature/ma-fonctionnalite
```

### 5. Synchronisation avec main

```
Récupérer les derniers changements :
git fetch origin
git rebase origin/main

Ou merger si vous préférez :
git merge origin/main
```

---

## Choisir son workflow

| Workflow | Équipe | Projet | Déploiement |
|----------|--------|--------|------------|
| **Git Flow** | Grande équipe | Grandes versions | Planifié |
| **GitHub Flow** | Petite/moyenne | Continu | Continu |
| **GitLab Flow** | Moyenne/grande | Complexe | Multi-env |
| **Trunk-Based** | Expérimentée | Haute fréquence | Continu |

---

## Ressources supplémentaires

- [Git Flow cheatsheet](https://danielkummer.github.io/git-flow-cheatsheet/)
- [GitHub Flow guide](https://guides.github.com/introduction/flow/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Commit Message Best Practices](https://chris.beams.io/posts/git-commit/)
