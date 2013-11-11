---
layout: post
title: "Astuces pour une commande 'prompt' utile"
date: 2013-04-29 08:46
comments: false
categories:
---
Vous aimez votre terminal ? Moi aussi. Vous y passez beaucoup trop de temps ? Moi aussi. Qui n'a pas personnalisé l’affichage de sa commande "prompt" ? Dernièrement, j'ai découvert quelques astuces bien utiles. J'aurai aimé vous renvoyer vers les articles originaux, pour rendre à Caesar ce qui est à Caesar, mais [désormais](http://blog.chron.com/techblog/files/2013/03/googlereaderrip_thumb.jpg) je ne sais plus rechercher correctement dans mes fils rss et j'ai été incapable de retrouver mes sources.


## Votre terminal supporte l'unicode

Et cela peut être très utile pour personnaliser l'invite et retranscrire des informations complexes avec des pictogrammes. Le rappel de modification dans git décrit plus bas est un bon exemple, mais je suis persuadé qu'il y a bien d'autres cas d’utilisation possible parmi le [panel](http://goetter.fr/unicode/) disponible.


## Alerte rouge

Quand on a fini d’exécuter une commande ou un programme, on peut bien sûr interroger son code retour. Mais comme on ne le fait pas systématiquement et surtout lorsqu'on ne s'y attend pas, on peut passer à coté et perdre beaucoup de temps. Il est tellement plus simple de récupérer automatiquement cette information et l'afficher avec un code couleur. #EFFICACE

``` bash
COLOR_RED="\[\e[0;31m\]"
COLOR_NONE="\[\e[0m\]"

if test $? -eq 0
then
    PS1="${prompt}➔ "
else
    PS1="${prompt}${COLOR_RED}➔${COLOR_NONE} "
fi
```


## Afficher la branche courante

J'aime GIT. Lorsqu'on explore une arborescence, savoir qu'on est dans un "repository" peut être très utile. Même quand on le sait, afficher le nom de la branche courante peut éviter bien des ambiguïtés et des erreurs.

``` bash
prompt="\w$(__git_ps1)""
```


## N'oubliez plus de commiter

Avez vous déjà quitté un "repository" avec des modifications en cours sans finir par un "commit" ? Moi oui ! En tous cas voici un moyen de ne plus le faire. Il suffit d'interroger le "status" git et d'afficher un rappel lorsque c'est nécessaire. Mieux que la couleur, un pictogramme unicode est parfaitement désigné pour cette tâche.

``` bash
git status 2> /dev/null | grep -c : | awk '{if ($1 > 0) print "⚡"}'
```


## Retourner à la racine du repository courant

Toutes les projets n'ont pas des arborescences interminables, mais desfois il peut être utile de savoir ou commence un "repository". Ce petit raccourci peut être utile, j'en suis sûr.

``` bash
alias git-root='cd $(git rev-parse --show-toplevel)'
```


#### Liens utiles ####

- probablement une des [pages](http://mywiki.wooledge.org/BashPitfalls) concernant bash les plus utiles de tout l'internet
- une [interview](http://www.computerworld.com.au/article/222764/a-z_programming_languages_bash_bourne-again_shell/) plutôt intéressante
- [stackoverflow](http://stackoverflow.com/questions/957928/is-there-a-way-to-get-to-the-git-root-directory-in-one-command) a toujours raison
