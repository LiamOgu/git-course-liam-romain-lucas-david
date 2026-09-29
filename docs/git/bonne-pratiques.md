# Bonnes pratiques Git

## 1. Configuration de départ

Avant le premier commit, configurer son identité :

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "prenom.nom@email.com"
git config --global init.defaultBranch main
```

Vérifier la configuration :

```bash
git config --list
```

## 2. Les commits

### Faire des petits commits

Un commit = une modification logique. On évite les commits du type « tout le travail de la journée ».

| À éviter | À privilégier |
|---|---|
| Un commit qui modifie 30 fichiers pour 4 sujets différents | 4 commits, un par sujet |
| Commiter du code qui ne compile pas | Commiter du code qui fonctionne |

### Vérifier avant de commiter

```bash
git status
git diff --staged
```

On sait toujours ce qu'on envoie.

### Écrire des messages clairs

Règles simples :

- au présent ou à l'impératif : `Ajoute`, `Corrige`, `Supprime`
- court (50 caractères environ pour la première ligne)
- dit **ce qui change**, pas « modif » ou « update »

| Mauvais message | Bon message |
|---|---|
| `modif` | `Corrige le calcul du total TTC` |
| `test` | `Ajoute les tests du service utilisateur` |
| `fix bug` | `Corrige l'erreur 500 à la connexion` |

### Convention « Conventional Commits » (souvent utilisée en entreprise)

```
<type>: <description>
```

| Type | Usage |
|---|---|
| `feat` | Nouvelle fonctionnalité |
| `fix` | Correction de bug |
| `docs` | Documentation |
| `style` | Mise en forme, sans changement de logique |
| `refactor` | Réorganisation du code sans changer son comportement |
| `test` | Ajout ou modification de tests |
| `chore` | Maintenance (dépendances, configuration) |

Exemples :

```
feat: ajoute la page de connexion
fix: corrige l'affichage des dates
docs: met à jour le README
```

## 3. Les branches

### Ne jamais travailler directement sur `main`

`main` contient le code stable. Chaque fonctionnalité ou correctif se fait sur sa propre branche.

```bash
git switch main
git pull
git switch -c feature/page-connexion
```

### Nommer ses branches

| Préfixe | Usage | Exemple |
|---|---|---|
| `feature/` | Nouvelle fonctionnalité | `feature/panier` |
| `fix/` | Correction de bug | `fix/login-erreur-500` |
| `hotfix/` | Correction urgente en production | `hotfix/paiement` |
| `docs/` | Documentation | `docs/readme-installation` |

Noms en minuscules, mots séparés par des tirets, sans accents ni espaces.

### Garder des branches courtes

Une branche qui vit trois semaines accumule les conflits. On fusionne souvent, par petits morceaux.

### Supprimer les branches fusionnées

```bash
git branch -d feature/page-connexion
git push origin --delete feature/page-connexion
```

## 4. Synchronisation avec le dépôt distant

### Récupérer avant d'envoyer

```bash
git pull
git push
```

Toujours faire un `pull` avant un `push` pour intégrer le travail des autres.

### Mettre à jour sa branche avec `main`

```bash
git switch main
git pull
git switch feature/ma-branche
git merge main
```

### Nettoyer les branches distantes supprimées

```bash
git fetch --prune
```

## 5. Le fichier `.gitignore`

À créer **dès le début** du projet, à la racine.

Ce qu'on n'envoie jamais :

- les dépendances : `node_modules/`, `vendor/`, `venv/`
- les fichiers compilés : `bin/`, `obj/`, `dist/`, `__pycache__/`
- les fichiers de configuration locale : `.env`, `.vscode/`, `.idea/`
- les fichiers système : `.DS_Store`, `Thumbs.db`
- les logs : `*.log`

Exemple :

```gitignore
# Dépendances
node_modules/
venv/

# Fichiers compilés
dist/
bin/
obj/
__pycache__/

# Configuration locale
.env
.vscode/
.idea/

# Système
.DS_Store
Thumbs.db

# Logs
*.log
```

Des modèles prêts à l'emploi existent sur [github.com/github/gitignore](https://github.com/github/gitignore).

## 6. Sécurité

### Ne jamais commiter de secrets

Mots de passe, clés d'API, tokens, identifiants de base de données : tout ça va dans un fichier `.env` ignoré par Git.

On versionne à la place un fichier `.env.example` sans les vraies valeurs :

```
DB_HOST=localhost
DB_USER=
DB_PASSWORD=
```

### Si un secret a été poussé par erreur

Le supprimer dans un nouveau commit ne suffit pas : il reste dans l'historique. Il faut **changer le mot de passe ou révoquer la clé immédiatement**.

## 7. Annuler proprement

| Situation | Commande |
|---|---|
| Annuler les modifications d'un fichier | `git restore fichier` |
| Retirer un fichier du staging | `git restore --staged fichier` |
| Modifier le dernier commit (non poussé) | `git commit --amend` |
| Annuler un commit déjà poussé | `git revert <id>` |
| Annuler un commit local | `git reset --soft HEAD~1` |

**Règle d'or** : on ne réécrit jamais l'historique d'une branche partagée. Pas de `reset --hard`, `rebase` ou `push --force` sur `main`.

## 8. Travail en équipe

### Passer par des Pull Requests / Merge Requests

1. Pousser sa branche
2. Ouvrir une Pull Request (GitHub) ou une Merge Request (GitLab)
3. Faire relire le code par un collègue
4. Corriger si besoin
5. Fusionner dans `main`

### Une PR lisible

- un titre clair
- une description : ce qui change et pourquoi
- une taille raisonnable (moins de 400 lignes si possible)

### Protéger la branche `main`

Sur GitHub ou GitLab, activer la protection de branche : pas de push direct, relecture obligatoire avant fusion.

## 9. Le README

Chaque projet doit avoir un `README.md` à la racine avec au minimum :

- le nom et le but du projet
- les prérequis (langage, version, outils)
- les étapes d'installation
- la commande pour lancer le projet

## 10. Mémo du quotidien

```bash
# Début de journée
git switch main
git pull

# Nouvelle tâche
git switch -c feature/ma-tache

# Pendant le travail
git status
git add .
git commit -m "feat: description"

# Envoi
git push -u origin feature/ma-tache

# Après fusion de la PR
git switch main
git pull
git branch -d feature/ma-tache
git fetch --prune
```

## Checklist avant chaque push

- [ ] Le code fonctionne
- [ ] `git status` est propre
- [ ] Aucun secret ni fichier inutile dans le commit
- [ ] Les messages de commit sont clairs
- [ ] J'ai fait un `git pull` avant
- [ ] Je ne suis pas sur `main`