---
title: "Installation de Hugo & Hebergement sur Github"
date: 2023-05-16T11:20:09+02:00
authors: [GuigZer]
draft: false
---

Hugo est un générateur de sites web statiques populaire. Il permet de créer des sites rapides et sécurisés sans besoin de base de données. Grâce à sa flexibilité et à sa communauté active, il offre de nombreuses fonctionnalités et thèmes personnalisables. Hugo est idéal pour ceux qui recherchent une solution simple et efficace pour créer des sites web statiques.
<!--more-->
## Installation du Packet debian

Pour commencer il est nécéssaire d'installer le paquet Hugo pour votre OS (Dans mon cas Debian 11). Le paquet n'étant pas forcément a jour sur les dépot officier Debian il est préférable d'installer le binaire dispo sur le repo git du projet → **[Release Hugo](https://github.com/gohugoio/hugo/releases "Release Hugo")** . Dans mon cas il faut prendre la version hugo_**__extended__** elle sera utile pour le futur theme.
```console
user@serv:~# mkdir hugo
user@serv:~# cd hugo
user@serv:~/hugo# wget https://github.com/gohugoio/hugo/releases/download/[...]_linux-amd64.deb
user@serv:~/hugo# dpkg -i [...]_linux-amd64.deb
```
### Initialisation du nouveau site et création du dépot git

```console
user@serv:~/hugo# hugo site [nom du site]
user@serv:~/hugo# cd [nom du site]
user@serv:~/hugo/[nds]# git init
user@serv:~/hugo/[nds]# git submodule add https://github.com/HEIGE-PCloud/DoIt.git themes/DoIt
user@serv:~/hugo/[nds]# hugo server
```

Dans l'ordre :
+ on demande à Hugo de créer un nouveau site  
+ Initialisation un nouveau dépot git
+ Ajout un sous module git qui est enfait le theme que l'ont souhaite utiliser
+ Hugo build le site.

## Push du dépot sur git

1. Créer un dépot sur GitHub
2. Push le dépot local (Serveur) sur GitHub
```console
git remote add origin git@github.com:[Username]/[nds].git
git branch -M main
git push -u origin main
```
3. Dans votre dépot GitHub il faut aller dans **Settings > Pages** au milleux de la page vous devez voir **Build and deployment** 
![Github Image](gh-pages-1.png)
4. Changer le type de source de `Deploy form branch` vers `GitHub Actions`
![Github Image](gh-pages-2.png)
5. Créer un fichier dans le dépot local
```console
touch .github/workflows/hugo.yaml
```
6. Copier le code ci-dessous dans le fichier hugo.yaml
.github/workflows/hugo.yaml
```yaml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches:
      - main

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.111.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb          
      - name: Install Dart Sass Embedded
        run: sudo snap install dart-sass-embedded
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"          
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```
7. Commit les changement puis le push sur GitHub.
8. Dans la page du dépot GitHub selectionner **Actions**, Il devrais apparaitre **Add workflow**
![Github Image](gh-pages-3.png) 
9. Quand GitHub aura fini de build **Add workflow** apparaitra avec une petite coche verte
![Github Image](gh-pages-4.png) 
10. Selectionner le **Add workflow** et vous y trouverez l'accès au site.
