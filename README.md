# Cours Git

Support de cours Git : commandes essentielles, workflows d'équipe, bonnes pratiques et dépannage.

Projet réalisé par **Liam**, **Romain**, **Lucas** et **David**.

## Contenu

| Document | Sujet |
| --- | --- |
| [Commandes](docs/git/commandes.md) | Les commandes Git principales, du `git init` à la collaboration avec un dépôt distant |
| [Workflows](docs/git/workflows.md) | Organisation du travail en équipe : branches, nommage, fusion, revue de code |
| [Bonnes pratiques](docs/git/bonne-pratiques.md) | Configuration, messages de commit, `.gitignore`, ce qu'il ne faut pas commiter |
| [Dépannage](docs/git/depannage.md) | Fiche de secours : annuler, réparer, résoudre un conflit, retrouver du travail perdu |

## Prérequis

- Git installé (`git --version`)

Si `git --version` ne renvoie rien, installer Git depuis [git-scm.com](https://git-scm.com/downloads).

## Pour commencer

Cloner le dépôt :

```bash
git clone https://github.com/LiamOgu/git-course-liam-romain-lucas-david.git
cd git-course-liam-romain-lucas-david
```

Configurer son identité si ce n'est pas déjà fait :

```bash
git config --global user.name "Prénom Nom"
git config --global user.email "prenom.nom@email.com"
```

## Organisation du dépôt

| Branche | Rôle |
| --- | --- |
| `main` | Version stable, à jour après validation |
| `develop` | Branche d'intégration, cible des pull requests |
| `docs/<sujet>` | Branches de travail, une par document |

Le flux de travail est décrit dans [docs/git/workflows.md](docs/git/workflows.md) : créer une branche depuis `develop`, commiter, pousser, ouvrir une pull request vers `develop`.
