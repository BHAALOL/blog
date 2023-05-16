---
title: "Installation de Hugo & Hebergement sur Github"
date: 2023-05-16T11:20:09+02:00
authors: [GuigZer]
draft: false
---

# Hugo
<!-- empty line -->
Hugo est un générateur de sites web statiques populaire. Il permet de créer des sites rapides et sécurisés sans besoin de base de données. Grâce à sa flexibilité et à sa communauté active, il offre de nombreuses fonctionnalités et thèmes personnalisables. Hugo est idéal pour ceux qui recherchent une solution simple et efficace pour créer des sites web statiques.
## Installation 
<!-- empty line -->
### Packet debian
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
