# Mémo Git

### Concepts
- **Dépôt (repository)**: Le projet suivi par Git (dossier caché .git)
- **Répertoire de travail (working directory)**: Les fichiers tels qu'ils sont sur le disque 
- **Zone de staging (index)**: Ce qui sera inclus dans le prochain commit
- **Commit**: Un instantané enregistré du projet, avec un message
- **Branche**: Une ligne de développement indépendante
- **HEAD**: Pointeur vers le commit (ou la branche) sur lequel on est
- **Remote**: Un dépôt distant (ex. sur GitHub), souvent nommé `origin`
- **Clone**: Copie locale complète d'un dépôt distant
- **Fork**: Autre version d'un dépot qui part d'une copie du dépot de quelqu'un d'autre
- **Pull request (PR)**: Demande d'intégration de sa branche dans une autre



### Cycle de base : 
- modifier
- `git add -A` ajoute les modifications à l'index
- `git commit -m "message"` commit
- `git push` envoie sur le serveur

### Config

```bash
git config --global user.name "Prénom Nom" # nom affiché dans les commits
git config --global user.email "moi@exemple.com" # email affiché dans les commits
git config --list # affiche la configuration
```

Niveaux de configuration : `--system` (toute la machine), `--global` (utilisateur OS),
`--local` (le dépôt courant, valeur par défaut). Le plus local l'emporte.

### Initialiser:
- `git init` pour un nouveau dossier
- `git clone <url>` pour partir d'un dépot existant

### Nouveaux fichiers

Un nouveau fichier est **non suivi** (untracked) tant qu'il n'a pas été ajouté.

```bash
git status # liste les fichiers non suivis, modifiés, prêts à être commités
```

### Staging

```bash
git add fichier # ajoute un fichier au staging
git add fichier1 fichier2 # ajoute plusieurs fichiers
git add . # ajoute tout le dossier courant (et sous-dossiers)
git add --all # ajoute tout le dépôt, suppressions comprises (alias : git add -A)

git restore --staged fichier # retire un fichier du staging (sans perdre les modifs)
git rm fichier # supprime le fichier et stage
git rm --cached fichier # arrête de suivre le fichier mais le garde sur le disque
git mv ancien nouveau # renomme/déplace un fichier et stage
```

### Commit

```bash
git commit -m "Message" # crée un commit avec le contenu du staging
git commit # ouvre l'éditeur pour écrire un message plus long
git commit -a -m "Message" # ajoute auto les fichiers DÉJÀ suivis puis commit (n'inclut pas les nouveaux fichiers)
```

Bon message : court, à l'impératif, qui dit ce que fait le commit
(ex. `Ajoute la page de contact`).

### Tags

```bash
git tag # liste les tags
git tag v1.0 # tag "léger" sur le commit courant
git tag -a v1.0 -m "Version 1.0" # tag annoté (auteur, date, message) — recommandé
git tag v0.9 <hash> # tag sur un ancien commit
git show v1.0 # détails du tag et du commit associé
git push origin v1.0 # envoie un tag sur le distant
git push --tags # envoie tous les tags
git tag -d v1.0 # supprime le tag en local
git push origin --delete v1.0 # supprime le tag sur le distant
```

### Stash

Pratique pour changer de branche sans commiter un travail en cours.

```bash
git stash # met de côté les modifs (suivies) et remet le repertoire au dernier commit
git stash push -m "WIP menu" # idem avec un message
git stash -u # inclut aussi les fichiers non suivis
git stash list # liste les stashs (stash@{0}, stash@{1}, ...)
git stash show -p stash@{0} # affiche le contenu d'un stash
git stash pop # réapplique le dernier stash et le supprime
git stash apply stash@{1} # réapplique un stash sans le supprimer
git stash drop stash@{1} # supprime un stash
git stash clear # supprime tous les stashs
git stash branch nom # crée une branche à partir d'un stash
```

### Historique

```bash
git log # historique complet des commits
git log -n 5 # les 5 derniers commits
git log -p fichier # historique d'un fichier avec les diffs
git show <hash> # détail d'un commit (message + modifications)
git diff # modifs pas encore ajoutées au staging
git diff --staged # modifs dans le staging (ce qui sera commité)
git diff main nom # différences entre deux branches
```

### Branches

```bash
git branch # liste les branches locales (* = branche courante)
git branch -a # liste aussi les branches distantes
git branch -v # branches avec leur dernier commit
git branch nom # crée une branche (sans y aller)
git switch nom # va sur une branche (ancienne syntaxe : git checkout nom)
git switch -c nom # crée une branche et y va (ancien : git checkout -b nom)
git branch -m ancien nouveau # renomme une branche
git branch -d nom # supprime une branche déjà fusionnée
git branch -D nom # force la suppression (même non fusionnée)
```

### Merge

```bash
git merge nom # fusionne la branche "nom" dans la branche courante
git merge --no-ff nom # force la création d'un commit de fusion
git merge --abort # annule une fusion en cours (en cas de conflit)
```

- **Fast-forward** : si `main` n'a pas bougé, Git avance simplement le pointeur.
- **Commit de fusion** : si les deux branches ont évolué, Git crée un commit qui les réunit.


### SSH

```bash
ssh-keygen -t ed25519 -C "moi@exemple.com"  # génère une paire de clés
eval "$(ssh-agent -s)" # démarre l'agent SSH
ssh-add ~/.ssh/id_ed25519 # ajoute la clé privée à l'agent
cat ~/.ssh/id_ed25519.pub # affiche la clé publique à copier
# -> GitHub : Settings > SSH and GPG keys > New SSH key > coller la clé
ssh -T git@github.com # teste la connexion
```

### Remote

```bash
git remote add origin <URL> # ajoute un dépôt distant nommé "origin"
git remote -v # liste les dépôts distants et leurs URL
git push -u origin main # premier envoi + lie main à origin/main
```

### Pull

```bash
git fetch origin # télécharge les nouveautés SANS toucher à tes fichiers
git merge origin/main # intégrer les modifs récupérées
git pull # = git fetch + git merge en une commande
git pull --rebase # = git fetch + git rebase (historique linéaire)
```

### Push

```bash
git push # envoie la branche courante vers son distant suivi
git push origin main # envoie explicitement main vers origin
```

Si le push est refusé (« rejected »), faire `git pull` d'abord.

### LFS

Stocke les gros fichiers (images, vidéos, binaires) hors du dépôt, seul un pointeur est
versionné.

```bash
git lfs install # active LFS (une fois par machine)
git lfs track "*.mp4" # suit les .mp4 avec LFS (écrit dans .gitattributes)
git add .gitattributes # commiter la règle de suivi
git lfs ls-files # liste les fichiers gérés par LFS
git lfs pull # télécharge les fichiers LFS
```
