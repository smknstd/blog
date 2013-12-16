---
layout: post
title: "vim VS Sublime Text: ajout d'un numéro de séquence"
date: 2013-11-30 19:12
comments: false
categories: 
---

Quand vient la question de choisir son éditeur de texte, la discussion pour un développeur peut vite devenir [passionnelle](https://en.wikipedia.org/wiki/Editor_war)
. Loin de moi l'idée dans cet article de vouloir convertir quiconque de changer d'éditeur, par contre si je m'écoutais j'en déconseillerais certains :( Dans cet article je souhaite comparer l'ajout d'une séquence dans mes deux éditeurs préférés. 

Modifier le formatage ne pose jamais tant de problèmes et je sais facilement ajouter un numéro de séquence en début de ligne, avec une commande uniligne comme: `gawk '{ print NR";"$0 }' exemple.csv`. Mais pour des besoins plus spécifiques, j'aime bien utiliser mon éditeur de texte quand c'est possible.

## vim
J'adore _vim_ (et forcement _vi_ aussi). Honnêtement j'ai appris à l'utiliser par obligation plus que par choix en intervenant sur des serveurs distants. Maintenant il est impossible m'en passer. C'est un outils fascinant, et toutes les ressources disponibles m'étonneront toujours. Elles sont souvent une bonne leçon d'humilité.

Je sais qu'il existe des [scripts](http://vim.wikia.com/wiki/Generating_a_column_of_increasing_numbers) sûrement très pratiques, mais malheureusement, quand j'utilise cet éditeur je n'ai pas toujours le luxe de pouvoir ajouter des plugins. Ma méthode pour ajouter un numéro de séquence repose sur deux fonctionnalités géniales :

- les macros
- l'[incrémentation](https://twitter.com/vcotwdorso/status/1602346985) d'un nombre

 Voici comment je procède:

1. _je place le curseur au bon endroit_
1. `i` _j'active le mode "insertion"_
2. `1` _je tape le premier numéro de ma séquence (on peut évidemment commencer à 2)_
3. `esc` _je sors du mode "insertion"_
4. `qa` _j'active le mode macro et je commence la macro qui s’appelle "a"_
5. `x` _je coupe le nombre sous le curseur pour le récupérer dans le tampon (copier!?)_
6. `p` _je le recolle aussitôt au même endroit_
7. `j` _je déplace le curseur à l'endroit ou je veux dupliquer ma séquence, en l’occurrence à la ligne suivante donc je descend (j est l’équivalent de la flèche vers le bas)_
8. `p` _je colle le contenu du tampon sous le curseur_
9. `Ctrl+a` _j'incrémente le nombre sous le curseur [#doyoubelieveinmagic](https://en.wikipedia.org/wiki/Do_You_Believe_in_Magic_%28song%29)_
10. `q` _j'ai terminé ma macro, je sors donc de ce mode_
11. `10@a` _je répète autant de fois que nécessaire cette opération (10 dans cet exemple)_

On peut se servir des macros pour dupliquer simplement une chaîne de caractères sur plusieurs lignes comme par exemple `INSERT INTO MA_TABLE VALUES (`. Mais dans ce cas j'aime utiliser, une autre fonctionnalité géniale de VIM: le mode visuel !

Pour rajouter un préfixe à mes 10 lignes je peux procéder de la sorte:

1. `gg` _pour positionner mon curseur au début du fichier_
2. `Ctrl+v`_pour activer le mode visuel_
3. `10j` _pour sélectionner les 10 lignes suivantes, mais les autres commandes sont possibles, comme atteindre la dernière ligne par exemple_
4. `I` _j'active le mode insertion (avec i en majuscule)_
5. `INSERT INTO MA_TABLE VALUES(` _je tape mon texte sous le curseur_
6. `esc` _je sors du mode visuel, ma modification s'applique à toutes les lignes sélectionnées précédemment_ [#WIN](https://www.youtube.com/watch?v=GGXzlRoNtHU)

## Sublime Text
J'adore _Sublime Text_. Je ne saurai même pas quoi lui reprocher. Si vous ne le connaissez pas, vous trouverez facilement des tonnes d'articles intéressants à son sujet. Comme je l'utilise sur mon poste de travail, je profite des nombreux plugins disponibles. C'est d'ailleurs de l'un d'eux que j'aime me servir: [Text Pastry](https://github.com/duydao/Text-Pastry). Souple et puissant, il est aussi incroyablement simple à utiliser.

_Text Pastry_, est entièrement basé sur une des possibilités géniale de Sublime Text: la sélection multiple. Bien sûr il n'est pas toujours évident de sélectionner exactement ce qu'on souhaite, mais il y a toujours un moyen astucieux de le faire. Pour moi les deux opérations suivantes suffisent souvent:

- Add next occurrence of selected text to selection: `Ctrl + D`
- Add all occurrences of the selected text to the selection: `Alt + F3`

Une fois que toutes les occurrences souhaitées sont sélectionnées, il suffit de faire:

1. `CMD + Alt + n` _pour ouvrir l'invite de commande_
2. `\i1` _pour générer un numéro de séquence qui démarre à 1_

![demosubt2]({{ root_url }}/images/sublimetext.gif "demo text pastry")

Enfantin ! En comparant avec la méthode VIM, je trouve que cela permet de bien réaliser combien la possibilité de sélectionner plusieurs zones simultanément est puissante, et peut remplacer des procédures compliquées.

Ce qui est génial, c'est qu'à la place d'une simple séquence, on peut utiliser une liste de chaînes de caractères ou même simplement le [presse-papier](https://en.wikipedia.org/wiki/Clipboard_%28computing%29). Cela offre de belles perspectives, vous ne trouvez pas ?


### Ressources

- un [plugin](https://github.com/tpope/vim-speeddating) incroyable (avec un nom très drôle) pour vim qui permet d'incrémenter une date (à condition qu'elle soit au bon [format](http://en.wikipedia.org/wiki/ISO_8601))
- le [tutorial](http://vim-adventures.com/) qu'on ne présente plus
