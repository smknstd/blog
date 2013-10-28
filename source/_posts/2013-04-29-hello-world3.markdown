---
layout: post
title: "Astuces pour une commande 'prompt' utile"
date: 2013-04-29 08:46
comments: false
categories: 
---
Vous aimez votre terminal ? moi aussi. Comme j'y passe souvent beaucoup trop de temps, j'ai découvert au cours du temps quelques astuces bien utiles. J'aurai aimé citer les sources de cette note, et rendre à Caesar ce qui est à Caesar, mais [désormais](http://blog.chron.com/techblog/files/2013/03/googlereaderrip_thumb.jpg) je ne sais plus rechercher correctement dans mes fils rss. :(


## Votre terminal supporte l'unicode

Très utile pour personnaliser l'invite et retranscrire des informations complexes avec des pictogrammes. Le rappel de modification git decrit plsu bas est un bon exemple, mais je suis persuadé qu'il y a bien d'autres cas d'utilisatioon possible parmis le [panel](http://goetter.fr/unicode/) disponible.


## Something went wrong : RED ALERT

Quand on a fini d'executer une commande, on peut bien sûr intérroger son code retour, mais il est beaucoup plus simple de récupérer automatiquement cette information et l'afficher avec un code couleur ! Très simple et pourtant diablement efficace.

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

J'aime GIT. Mais je fais des erreurs. Afficher le nom de la branche courante des qu'on se place dans un repository peut eviter bien des problèmes. Honetement, je ne peux plus m'en passer.

``` bash
prompt="\w$(__git_ps1)""
```


## N'oubliez plus de commiter

Avez vous déjà quitter un repository avec des modifications non commitées ? Voici un moyen de ne plus le faire. On affiche en permanence l'etat (avec un pictogramme unicode !): infaillible !

``` bash
git status 2> /dev/null | grep -c : | awk '{if ($1 > 0) print "⚡"}'
```


## retourner à la racine du repository courant

Toutes les projets n'ont pas des raborescences interminables, mais ce petit raccourci peut être utile, j'en suis sûr.

``` bash
alias git-root='cd $(git rev-parse --show-toplevel)'
```


#### Liens utiles ####

- probably the most useful [page](http://mywiki.wooledge.org/BashPitfalls) about bash on the whole internet
- pretty interesting [interview](http://www.computerworld.com.au/article/222764/a-z_programming_languages_bash_bourne-again_shell/)
- [stackoverflow](http://stackoverflow.com/questions/957928/is-there-a-way-to-get-to-the-git-root-directory-in-one-command)'s always right