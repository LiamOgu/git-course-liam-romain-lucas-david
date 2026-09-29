# Dépannage Git

Fiche de secours : que faire quand ça part mal. Les commandes sont données pour ce dépôt (branches `main` et `develop`).

> Règle d'or : avant toute commande destructive (`reset --hard`, `push --force`, `clean -fd`), vérifie que ton travail est commité ou stasché. En cas de doute, `git status` d'abord.

## Sommaire

- [Je veux annuler quelque chose](#je-veux-annuler-quelque-chose)
- [Résoudre un conflit](#résoudre-un-conflit)
- [Corriger le dernier commit](#corriger-le-dernier-commit)
- [J'ai commité sur la mauvaise branche](#jai-commité-sur-la-mauvaise-branche)
- [Annuler un commit déjà poussé](#annuler-un-commit-déjà-poussé)
- [Retrouver du travail perdu](#retrouver-du-travail-perdu)
- [Mettre de côté sans commiter](#mettre-de-côté-sans-commiter)
- [HEAD détaché](#head-détaché)
- [Erreurs courantes](#erreurs-courantes)

## Je veux annuler quelque chose

| Situation | Commande | Effet |
| --- | --- | --- |
| Annuler les modifs d'un fichier non commité | `git restore fichier.txt` | Écrase le fichier avec la version du dernier commit |
| Retirer un fichier de la zone de staging | `git restore --staged fichier.txt` | Garde les modifs dans le fichier |
| Annuler le dernier commit, garder les modifs | `git reset --soft HEAD~1` | Le commit disparaît, les modifs restent stagées |
| Annuler le dernier commit, garder les fichiers non stagés | `git reset HEAD~1` (ou `--mixed`) | Commit annulé, modifs dans le working tree |
| Annuler le dernier commit **et** les modifs | `git reset --hard HEAD~1` | ⚠️ Perte définitive des modifs non commitées |
| Annuler un commit **sans** réécrire l'historique | `git revert <hash>` | Crée un commit inverse, sûr si déjà poussé |

`reset` réécrit l'historique (à éviter sur une branche partagée), `revert` en ajoute (toujours sûr). Voir aussi [Annuler un commit déjà poussé](#annuler-un-commit-déjà-poussé).

## Résoudre un conflit

Un conflit arrive sur `merge`, `rebase`, `cherry-pick` ou `pull`. Git s'arrête en te disant `CONFLICT (content): Merge conflict in <fichier>`.

```bash
git status              # liste les fichiers en conflit
# ouvrir chaque fichier, repérer les marqueurs :
#   <<<<<<< HEAD
#   =======
#   >>>>>>> autre-branche
# garder le bon contenu, supprimer les marqueurs
git add fichier-resolu.txt
git merge --continue    # ou: git rebase --continue
```

| Commande | Quand |
| --- | --- |
| `git merge --continue` | Reprendre le merge après résolution |
| `git rebase --continue` | Reprendre le rebase après résolution |
| `git merge --abort` | Tout annuler, revenir à l'état d'avant le merge |
| `git rebase --abort` | Tout annuler, revenir à l'état d'avant le rebase |
| `git checkout --theirs fichier` | Garder la version de la branche entrante |
| `git checkout --ours fichier` | Garder la version de la branche courante |

À plusieurs sur le même fichier : résoudre le conflit **à deux** devant l'écran plutôt que seul, et prévenir sur le canal de l'équipe.

## Corriger le dernier commit

```bash
git commit --amend              # corriger le message (éditeur)
git commit --amend -m "message" # corriger le message directement
git add fichier-oublié.txt
git commit --amend --no-edit    # ajouter un fichier oublié au dernier commit
```

⚠️ `--amend` réécrit le commit. Si le commit est déjà poussé sur une branche partagée, il faudra un `push --force-with-lease` (voir ci-dessous). Sur `main` ou `develop` : ne pas le faire, préférer un commit de correction.

## J'ai commité sur la mauvaise branche

Les commits sont sur `develop` alors qu'ils devaient être sur une branche feature. Tant que rien n'est poussé :

```bash
git branch ma-feature            # créer la branche pointant sur le commit actuel
git reset --hard origin/develop  # remettre develop à l'état du remote
git switch ma-feature            # continuer sur la bonne branche
```

Si tu as déjà poussé sur `develop` : ne réécris rien, `git revert` le commit fautif.

## Annuler un commit déjà poussé

Sur une branche partagée (`main`, `develop`), la bonne méthode est `revert` : il n'efface rien, il ajoute un commit qui défait le précédent.

```bash
git revert <hash>       # annule un commit précis
git revert HEAD         # annule le dernier commit
git revert <h1>..<h2>   # annule une plage de commits
git push
```

`push --force` n'est à utiliser que sur **ta** branche feature, jamais sur `main`/`develop`. Si tu dois vraiment forcer, préfère `--force-with-lease`, qui refuse si quelqu'un a poussé entre-temps :

```bash
git push --force-with-lease origin ma-feature
```

## Retrouver du travail perdu

`reflog` garde la trace de tous les déplacements de `HEAD`, même après un `reset --hard`. C'est la bouée de secours.

```bash
git reflog                       # liste des états récents de HEAD
git reset --hard <hash-du-reflog> # revenir à un état avant la bêtise
```

Repère la ligne qui précède l'erreur, récupère son hash, et ramène la branche dessus.

## Mettre de côté sans commiter

Pour changer de branche sans commiter un travail en cours :

```bash
git stash                 # range les modifs et nettoie le working tree
git stash -m "wip: formule de paie"  # avec un message
git stash -u              # inclut les fichiers non suivis (untracked)
git stash list            # voir les stashs en attente
git stash pop             # réappliquer le dernier et le supprimer
git stash apply           # réappliquer sans le supprimer
git stash drop stash@{0}  # supprimer un stash précis
```

## HEAD détaché

Message `You are in 'detached HEAD' state` : tu as fait un `checkout` sur un commit ou un tag précis, pas sur une branche.

- Tu voulais juste regarder : `git switch -` (ou `git checkout develop`) pour revenir.
- Tu as fait du travail à garder : `git switch -c ma-branche` crée une branche depuis cet état et sauve tes commits.

## Erreurs courantes

| Message | Cause | Solution |
| --- | --- | --- |
| `Updates were rejected... fetch first` | Le remote a des commits que tu n'as pas | `git pull --rebase` puis `git push` |
| `Please commit your changes or stash them` | Fichiers modifiés qui bloquent un `switch`/`pull` | `git stash`, faire l'opération, `git stash pop` |
| `fatal: not a git repository` | Tu n'es pas dans le dossier du dépôt | `cd` dans le projet, vérifier avec `git rev-parse --show-toplevel` |
| `error: failed to push some refs` | Idem « fetch first » | Voir la ligne 1 |
| `fatal: refusing to merge unrelated histories` | Deux historiques sans ancêtre commun | À éviter ; si vraiment voulu : `git pull --allow-unrelated-histories` |
| `LF will be replaced by CRLF` | Fin de ligne Windows/Linux | Avertissement, pas une erreur ; régler avec `.gitattributes` |

## En dernier recours

1. Ne rien taper de destructif.
2. `git status` et `git reflog` pour comprendre l'état.
3. Copier l'erreur complète et demander à l'équipe (ou chercher le message exact).
4. Si le dépôt local est cassé mais que le travail est poussé : recloner dans un nouveau dossier et récupérer le travail local au besoin.
