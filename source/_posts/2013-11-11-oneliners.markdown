---
layout: post
title: "unilignes: l'héritage d'awk dans les langages interprétés modernes"
date: 2013-11-11 19:37
comments: false
categories: 
---

Il y a seulement quelques années, lorsque j'ai commencé à utiliser la commande shell, j'ai très vite pu apprécié la puissance et l'ingéniosité de certains outils surtout dans la manipulation courante des données. A ce stade, je savais écrire un programme en C ou en java, mais Xavier, un programmeur expérimenté de 25 ans mon aîné que j'avais désigné comme mon mentor, a particulièrement attiré mon attention sur cet étrange outil dont j'ignorais tout: awk.

J'ai rapidement compris que malgré son apparence rebutante, il allait me devenir indispensable! Associé au ["pipe"](http://doc.cat-v.org/unix/pipes/), il est capable de résoudre de véritables casse-têtes en une commande d'une seule ligne. Bien sûr le prix d'une ligne c'est qu'elle est souvent à rallonge, mais pour de nombreuses opérations elle évite d'écrire un script. Au moins deux spécificités sont particulièrement appréciées dans Awk:

1. il traite un fichier ligne par ligne
2. il permet de découper chaque ligne selon un délimiteur et manipuler facilement les champs ainsi découpés

_Attention ces exemples ne sont vraiment pas originaux !_ Pour toutes les lignes d'un fichier CSV, on peut garder uniquement la 4ème colonne:

`gawk -F; '{print $4}' ./exemple.csv`

Aussi il dispose d'un système de "modèle/action" bien pratique et qui rend par exemple très facile de filtrer les lignes sur certains critères. On peut par exemple facilement exclure certaines lignes (les 3 premières lignes):

`gawk -F; 'NR>3 { print $4 }' ./exemple.csv`

Mais awk est aussi un véritable langage de programmation puisqu'il dispose d'outils élaborés (tableaux, boucles, types, librairies de fonctions, etc). Awk date de la fin des années 70 et en m’intéressant à la [littérature de l'époque](http://cm.bell-labs.com/cm/cs/awkbook/index.html), j'ai réalisé qu'il avait comme ambition de résoudre des problèmes bien plus sophistiqués que peuvent le faire des unilignes. Évidemment, ces programmes sont aujourd'hui obsolètes, mais en plus d'être [court](https://news.ycombinator.com/item?id=2933058), la souplesse et la puissance de son modèle ont fait école. Il est bien connu que certains des [langages interprétés](http://en.wikipedia.org/wiki/Interpreted_language) majeurs comme perl et ruby, s'en sont largement inspiré.

En parcourant cette instructive [matrice de comparaison](http://hyperpolyglot.org/scripting), je me suis rappelé que cela n’était pas toujours bien connu. Les options nécessaires pour simuler le comportement d'awk sont les mêmes dans les deux langages. En voici l'énumération et une explication propre:


```
-a: turns on auto-split-mode: this splits the input into an array. The default separator is one (or many) spaces.

-n: makes Perl/Ruby process the input in an implicit while loop around the input, which is processed line-wise: "as long as there is another line, process it!"

-e: execute the next command line argument as a program.
```

Spécifiquement pour PERL, la gestion des sauts de lignes:

```
-l: "chomps" the input (which here means: removes newlines if present) from each line, and automatically adds a newline to each output newline
```

Enfin voici une retranscription d'un petit programme awk qui compile quelques fonctionnalités généralement appréciées:

- filtre des données sur un champ donné
- numérotation des lignes
- selection d'un champ donné à partir de son numéro d’index
- sélection du dernier champ

`gawk -F; ' $1 = "toto" { print NR ";" $4 ";"$NF }' | sort -t";" -k 2 | uniq`

en PERL

`perl -F; -lane 'print "$.;$F[4];$F[-1]" if $F[0] = "toto"' | sort -t";" -k 2 | uniq`

et enfin en RUBY

`ruby -F; -ane 'print "$.;$F[4];$F.last" if $F[0] = "toto"' | sort -t";" -k 2 | uniq`



### Ressources
- [le célèbres .txt de Eric Pement sur awk](http://www.pement.org/awk/awk1line.txt)
- [interview intéressante](http://www.computerworld.com.au/article/216844/a-z_programming_languages_awk)
- [inestimable ressource sur les "one-liners" en PERL](http://web.archive.org/web/20090602215912/http://sial.org/howto/perl/one-liner/) (merci à la bonne âme qui l'a archivée)
- [très bon tutoriel PERL](http://articles.mongueurs.net/magazines/linuxmag50.html) en français
- [article magistrale sur l'héritage d'awk dans ruby](http://tomayko.com/writings/awkward-ruby)
- unilignes [socials](https://twitter.com/rubyoneliner) ?!


