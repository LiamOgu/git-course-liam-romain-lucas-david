 # Cours : les principales commandes Git

Git est un système de gestion de versions distribué. Il permet de conserver
l'historique d'un projet, de travailler à plusieurs et de revenir à une version
précédente en cas de problème.

Ce cours présente les commandes les plus utiles, de la création d'un dépôt à la
collaboration avec un dépôt distant.

## 1. Comprendre le fonctionnement de Git

Git suit les modifications dans trois espaces principaux :

1. **Le répertoire de travail** : les fichiers que l'on modifie.
2. **La zone de préparation** (*staging area*) : les modifications choisies pour
	 le prochain commit.
3. **Le dépôt local** : l'historique enregistré par les commits.

Le dépôt distant, par exemple sur GitHub, est une copie accessible par les
autres membres du projet.

Le cycle classique est donc :

```text
modifier -> git add -> git commit -> git push
```

## 2. Installer et configurer Git

Vérifier que Git est installé :

```bash
git --version
```

Configurer son identité. Ces informations sont enregistrées dans les commits :

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "adresse@example.com"
```

Afficher la configuration actuelle :

```bash
git config --list
```

Afficher une valeur précise :

```bash
git config user.name
git config user.email
```

L'option `--global` applique la configuration à tous les dépôts de l'ordinateur.
Sans cette option, la configuration ne concerne que le dépôt courant.

## 3. Créer ou récupérer un dépôt

### Initialiser un dépôt local

Dans le dossier du projet :

```bash
git init
```

Cette commande crée un dossier caché `.git` qui contient l'historique et la
configuration du dépôt.

### Cloner un dépôt existant

```bash
git clone https://github.com/utilisateur/projet.git
```

Git crée un nouveau dossier, télécharge les fichiers et configure le dépôt
distant appelé `origin`.

Cloner dans un dossier portant un autre nom :

```bash
git clone https://github.com/utilisateur/projet.git mon-projet
```

## 4. Observer l'état du projet

### `git status`

La commande la plus utile au quotidien :

```bash
git status
```

Elle indique notamment :

- la branche courante ;
- les fichiers modifiés ;
- les fichiers prêts à être commités ;
- les fichiers non suivis par Git.

### `git diff`

Voir les modifications qui ne sont pas encore ajoutées à la zone de préparation :

```bash
git diff
```

Voir les modifications déjà préparées :

```bash
git diff --staged
```

Comparer deux commits :

```bash
git diff commit-ancien commit-recent
```

## 5. Ajouter et enregistrer des modifications

### Ajouter des fichiers avec `git add`

Ajouter un fichier précis :

```bash
git add README.md
```

Ajouter plusieurs fichiers :

```bash
git add fichier1.md fichier2.md
```

Ajouter toutes les modifications du dossier courant :

```bash
git add .
```

Il est préférable de vérifier les fichiers ajoutés avec `git status` avant de
créer le commit.

### Créer un commit avec `git commit`

```bash
git commit -m "Ajoute la documentation Git"
```

Un bon message de commit décrit l'action réalisée. Exemples :

```text
Ajoute la page de présentation
Corrige le lien vers la documentation
Met à jour les dépendances
```

Voir le contenu du dernier commit :

```bash
git show
```

Ajouter les fichiers déjà suivis et créer directement un commit :

```bash
git commit -am "Corrige la mise en forme"
```

Attention : `-a` ne prend pas en compte les nouveaux fichiers non suivis. Ils
doivent d'abord être ajoutés avec `git add`.

### Annuler la préparation d'un fichier

Retirer un fichier de la zone de préparation sans supprimer ses modifications :

```bash
git restore --staged fichier.md
```

## 6. Consulter l'historique

Afficher l'historique complet :

```bash
git log
```

Afficher un résumé lisible sur une seule ligne par commit :

```bash
git log --oneline
```

Afficher l'historique sous forme d'arbre avec les branches :

```bash
git log --oneline --graph --all --decorate
```

Limiter le nombre de commits affichés :

```bash
git log -5
```

Rechercher un mot dans les messages de commit :

```bash
git log --grep="documentation"
```

Voir qui a modifié chaque ligne d'un fichier :

```bash
git blame README.md
```

## 7. Revenir sur une modification

### Annuler une modification locale

Restaurer un fichier modifié depuis le dernier commit :

```bash
git restore fichier.md
```

Cette commande supprime les modifications locales non commités du fichier.
Il faut donc l'utiliser avec prudence.

Restaurer tous les fichiers modifiés du répertoire courant :

```bash
git restore .
```

### Modifier le dernier commit

Ajouter un fichier oublié au dernier commit :

```bash
git add fichier-oublie.md
git commit --amend --no-edit
```

Modifier aussi le message du dernier commit :

```bash
git commit --amend -m "Nouveau message"
```

Éviter de modifier un commit déjà partagé, car cela réécrit l'historique.

### Annuler un commit avec `git revert`

Créer un nouveau commit qui annule un commit existant :

```bash
git revert <identifiant-du-commit>
```

Cette méthode est recommandée pour annuler un commit déjà envoyé sur un dépôt
partagé, car elle conserve un historique sûr et compréhensible.

### Revenir à un ancien état avec `git reset`

Déplacer la branche vers un commit précédent :

```bash
git reset --soft HEAD~1
git reset --mixed HEAD~1
git reset --hard HEAD~1
```

- `--soft` conserve les modifications dans la zone de préparation ;
- `--mixed` conserve les modifications dans le répertoire de travail ;
- `--hard` supprime les modifications concernées.

`git reset --hard` peut entraîner une perte de données. Ne l'utilisez pas sur
un travail que vous souhaitez conserver.

## 8. Travailler avec les branches

Une branche permet de développer une fonctionnalité ou une correction sans
modifier directement la branche principale.

Afficher les branches locales :

```bash
git branch
```

Créer une branche :

```bash
git branch nouvelle-fonctionnalite
```

Changer de branche :

```bash
git switch nouvelle-fonctionnalite
```

Créer une branche et s'y déplacer immédiatement :

```bash
git switch -c nouvelle-fonctionnalite
```

La syntaxe historique équivalente est :

```bash
git checkout -b nouvelle-fonctionnalite
```

Renommer la branche courante :

```bash
git branch -m nouveau-nom
```

Supprimer une branche locale déjà fusionnée :

```bash
git branch -d nouvelle-fonctionnalite
```

Forcer la suppression d'une branche non fusionnée :

```bash
git branch -D nouvelle-fonctionnalite
```

## 9. Fusionner des branches

Se placer sur la branche qui doit recevoir les changements, puis lancer la
fusion :

```bash
git switch main
git merge nouvelle-fonctionnalite
```

### Résoudre un conflit

Un conflit apparaît lorsque Git ne peut pas choisir automatiquement entre deux
modifications. Les fichiers concernés contiennent alors des marqueurs :

```text
<<<<<<< HEAD
Version actuelle
=======
Version de l'autre branche
>>>>>>> nouvelle-fonctionnalite
```

Pour résoudre le conflit :

1. Ouvrir le fichier et conserver la bonne version, ou combiner les deux.
2. Supprimer les marqueurs `<<<<<<<`, `=======` et `>>>>>>>`.
3. Ajouter le fichier résolu.
4. Terminer la fusion avec un commit.

```bash
git add fichier-en-conflit.md
git commit
```

Annuler une fusion en cours :

```bash
git merge --abort
```

## 10. Synchroniser avec un dépôt distant

### Voir les dépôts distants

```bash
git remote -v
```

Ajouter un dépôt distant :

```bash
git remote add origin https://github.com/utilisateur/projet.git
```

Renommer un dépôt distant :

```bash
git remote rename origin upstream
```

### Télécharger les informations avec `git fetch`

```bash
git fetch origin
```

`fetch` télécharge les nouveaux commits et les références des branches sans
modifier les fichiers de travail.

### Récupérer les changements avec `git pull`

```bash
git pull origin main
```

`pull` réalise généralement un `fetch` suivi d'une fusion. Avant de l'utiliser,
il est conseillé de vérifier que le travail local est commit é ou mis de côté.

### Envoyer ses commits avec `git push`

```bash
git push origin main
```

Lors du premier envoi d'une nouvelle branche :

```bash
git push -u origin nouvelle-fonctionnalite
```

L'option `-u` mémorise la branche distante. Les prochains envois peuvent alors
être faits avec :

```bash
git push
```

## 11. Mettre temporairement son travail de côté

`git stash` permet de ranger momentanément des modifications non commités :

```bash
git stash
```

Afficher les sauvegardes temporaires :

```bash
git stash list
```

Récupérer la dernière sauvegarde et la supprimer de la liste :

```bash
git stash pop
```

Récupérer une sauvegarde sans la supprimer :

```bash
git stash apply
```

Supprimer une sauvegarde précise :

```bash
git stash drop stash@{0}
```

## 12. Ignorer certains fichiers

Le fichier `.gitignore` liste les fichiers que Git ne doit pas suivre. Exemple :

```gitignore
# Dépendances
node_modules/

# Fichiers générés
dist/
build/

# Fichiers locaux et secrets
.env
*.log

# Fichiers du système
.DS_Store
```

Après avoir créé ou modifié `.gitignore`, vérifier l'état du dépôt :

```bash
git status
```

Un fichier déjà suivi ne sera pas ignoré rétroactivement. Il faut d'abord le
retirer de l'index, sans le supprimer du disque :

```bash
git rm --cached fichier-secret.env
```

## 13. Marquer une version avec un tag

Un tag identifie une version importante, par exemple une release :

```bash
git tag v1.0.0
```

Créer un tag annoté avec un message :

```bash
git tag -a v1.0.0 -m "Version 1.0.0"
```

Afficher les tags :

```bash
git tag
```

Envoyer un tag sur le dépôt distant :

```bash
git push origin v1.0.0
```

Envoyer tous les tags :

```bash
git push origin --tags
```

## 14. Commandes utiles pour diagnostiquer un problème

Vérifier les modifications non enregistrées :

```bash
git status
git diff
```

Retrouver un commit perdu ou une ancienne position de HEAD :

```bash
git reflog
```

Afficher les branches locales et distantes :

```bash
git branch -a
```

Vérifier l'origine d'un fichier ignoré :

```bash
git check-ignore -v fichier.log
```

Obtenir l'aide intégrée de Git :

```bash
git help <commande>
git <commande> --help
```

Exemple :

```bash
git help commit
```

## 15. Exemple de workflow complet

Voici un scénario courant pour développer une fonctionnalité :

```bash
# Récupérer le projet et ses dernières modifications
git clone https://github.com/utilisateur/projet.git
cd projet
git pull origin main

# Créer une branche de travail
git switch -c ajout-documentation

# Modifier les fichiers, puis vérifier les changements
git status
git diff

# Préparer et enregistrer le travail
git add docs/git/commandes.md
git diff --staged
git commit -m "Ajoute un cours sur les commandes Git"

# Publier la branche
git push -u origin ajout-documentation
```

Après vérification de la branche, elle peut être fusionnée dans `main` via une
*pull request* sur la plateforme utilisée par le projet.

## 16. Bonnes pratiques à retenir

- Faire des commits petits et cohérents.
- Écrire des messages de commit clairs, à l'impératif.
- Vérifier `git status` et `git diff` avant chaque commit.
- Ne jamais versionner de mots de passe, clés privées ou fichiers `.env`.
- Mettre à jour sa branche avant de commencer une nouvelle tâche.
- Utiliser une branche dédiée pour chaque fonctionnalité ou correction.
- Préférer `git revert` pour annuler un commit déjà partagé.
- Éviter `git push --force` sur une branche utilisée par plusieurs personnes.
- Ne pas utiliser `git reset --hard` sans avoir vérifié que les modifications
	peuvent être supprimées.

## Résumé des commandes essentielles

| Besoin | Commande |
| --- | --- |
| Vérifier l'état du dépôt | `git status` |
| Ajouter un fichier | `git add fichier` |
| Créer un commit | `git commit -m "Message"` |
| Voir l'historique | `git log --oneline` |
| Créer une branche | `git switch -c nom` |
| Fusionner une branche | `git merge nom` |
| Télécharger les changements | `git fetch` |
| Récupérer et fusionner | `git pull` |
| Envoyer ses commits | `git push` |
| Mettre de côté son travail | `git stash` |
| Annuler un commit partagé | `git revert commit` |
