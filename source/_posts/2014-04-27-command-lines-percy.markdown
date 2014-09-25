---
layout: post
title: "Mes outils en ligne de commande préférés"
date: 2014-04-27 16:21
comments: false
categories: 
---
J'aime travailler dans le terminal, j'y édite même ce blog. C'est un endroit familier ou chacun a ses petites habitudes, ses petites astuces. Dans cet article j'ai souhaité humblement partager quelques outils en ligne de commande plus ou moins méconnues et indispensables que j'ai découvert avec le temps et qui complètent les outils habituels que tout le monde connaît. Parfois, on peut contourner un problème pendant longtemps avant de se rendre compte qu'il existe une solution très simple pour y remédier. c'est sûrement pourquoi j'aime particulièrement ces outils peu conventionnels mais qui ont le mérite d'exister. Voici une compilation de mes favoris:

##[ack](http://beyondgrep.com/)
Pour chercher une chaine de caractère dans plusieurs fichiers avec grep, ce n'est pas très pratique. On peut utiliser l'opérateur * ou un assemblage d'options qui peut vite se compliquer avec l'option -exec de find et avoir recours à [xargs](http://en.wikipedia.org/wiki/Xargs). Heureusement quand on cherche dans un code source, `ack-grep` propose une solution très intéressante. Pour ceux qui ne le connaissent pas encore, cet outils est vraiment très pratique. Les fonctions appréciées permettent par exemple de filtrer très facilement le type de fichier dans lesquels chercher ou d'en ignorer certains ou encore coloriser le résultat. Comme souvent dans la communauté les débats sont très vite passionnés, et estimer quel outil est "mieux" qu'un autre semble être un besoin primaire. Même si cela m'amuse beaucoup, j’espère qu'à l'image de l'url du site officielle qui est passée de "betterthangrep" à "beyondgrep" les esprits sauront s’apaiser et [nuancer](http://betterthanack.com/) leur propos.

##[xclip](http://linuxtidbits.wordpress.com/2008/02/22/command-line-to-clipboard/)
Copier/coller est un outil fondamental. Malheureusement en mode console, cela passe souvent par l'utilisation de la souris ou un fichier intermédiaire superflu. `xclip` est un outils simple et pratique pour récupérer dans le [presse-papier](http://en.wikipedia.org/wiki/Clipboard_%28computing%29) le contenu d'un fichier et surtout la sortie standard d'une ligne de commande grâce au pipe.

##[watch](http://en.wikipedia.org/wiki/Watch_%28Unix%29)
Lorsqu'on scrute le résultat d'un programme, en attendant de voir un résultat, on a tendance a répéter inlassablement certaines opérations. En travaillant avec des fichiers de logs, j'ai gagné beaucoup de temps grâce à `watch`, qui permet d’exécuter une commande à intervalles réguliers en rafraîchissant l'affichage. J’imagine qu'il doit sûrement y avoir des tonnes d'usages.

##[tac](http://en.wikipedia.org/wiki/Tac_%28Unix%29)
Bon évidemment dans cet article toutes mes trucs et astuces ne sont pas totalement essentielles ou indispensables. Je ne pense pas avoir eut besoin de `tac` très souvent mais j'aime vraiment beaucoup l'esprit de cette commande.

##[yes](http://en.wikipedia.org/wiki/Yes_%28Unix%29)
`yes` est une commande historique répondant à un besoin qui n'existe plus et que je n'ai d'ailleurs jamais rencontré. Mais j'aime sa logique et on peut facilement le détourner de son usage original: pour moi il remplace quelquefois une boucle et me permet de créer facilement un fichier de test ou d'alimenter une entrée standard et c'est déjà pas mal, non ?

##complétion
On s'éloigne du concept initial de l'article, car la complétion est bien plus qu'une commande, mais c'est un outils de la console forcément lié et indispensable. Je souhaite en parler car il est facile d'oublier sa puissance et j'ai au moins deux exemples pour l'illustrer. Comme tous les utilisateurs de BASH, je me sers beaucoup de l'historique. Pour l'utiliser correctement il est essentiel de le [configurer](http://blog.sanctum.geek.nz/better-bash-history/) mais pour y naviguer correctement il existe une complétion. En tapant les premières lettres de la commande souhaitée, `ctrl + r`et `ctrl + s` permettent de naviguer uniquement dans les commandes qui ont la même racine. C'est tellement pratique qu'il est intéressant d'en faire des [raccourcis](http://lkdjiin.github.io/blog/2014/02/20/astuce-bash-ameliorer-lhistorique/).

La complétion existe sur les commandes disponibles ou les fichiers je ne vous apprends rien, mais pour certains programmes on peut disposer d'une complétion sur les options. Si comme moi vous utilisez bash et vous allez fouiller dans `/etc/bash_completion.d`, vous serez surpris du nombre de programmes pour lesquels cette fonctionnalité est déjà installé ! C'est par exemple le cas pour [GIT](http://git-scm.com/book/fr/Les-bases-de-Git-Trucs-et-astuces#Auto-Compl%C3%A9tion) et je dois vous avouer que j'ai découvert en affichant la liste complète de ses options que je n'avais aucune idée de l’existence d'un bon nombre d'entre elles ;-)

![completion]({{ root_url }}/images/git-completion.png "completion")