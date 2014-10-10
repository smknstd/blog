---
layout: post
title: "Regrouper et compter: Panda (partie 6/6)"
date: 2014-09-01 09:14
comments: false
categories: 
---

Dans cette série d'articles consacrée aux différentes approches d'une opération de regroupement et de comptage, j'ai essayé de ne pas me concentrer uniquement sur les bases de données. Pour ce dernier article, j'ai voulu rappeler qu'il existe bien d'autres outils spécialisés qui peuvent répondre à des besoins variés. Il existe notamment des outils sophistiqués de manipulation et de calcul statistique plus ou moins récents comme le langage [R](http://en.wikipedia.org/wiki/NumPy) (~1993) ou [SAS](http://fr.wikipedia.org/wiki/SAS_%28langage%29) (~années 60). Rien de nouveau sous le soleil, mais depuis quelques années (~2012) une petite librairie python fait beaucoup de bruit dans ce paysage: le framework [Panda](https://github.com/pydata/pandas) !

### Panda ?

Panda est une boite à outils de manipulation et d'analyse de données en mémoire ! Ce n'est pas un outil aussi spécialisé que peut l'être R par exemple, mais plutôt une libraire généraliste et facile à mettre en œuvre. Basé sur une [structure](http://en.wikipedia.org/wiki/NumPy) interne de données très performante, il est vraiment souple et très facile à prendre en main. Il est aussi très bien documenté et offre de vastes possibilités, ce qui en fait un outil redoutablement efficace !

Bien sûr, toutes les opérations se font en mémoire donc on est forcement limité par les ressources de sa machine. Mais en ne chargeant que les données nécessaires pour chaque opération, et en se rappelant qu'il n'est pas rare de voir un PC portable avec 16go de RAM, cela ouvre de belles perspectives, non ?

Panda se présente donc comme une solution convaincante pour manipuler et analyser des données volumineuses, surtout si vous devez le faire rapidement et que ce n'est pas un besoin récurrent. Il n'est pas vraiment question ici, d'implémenter un mode batch. 

### Regrouper et compter

Comme évoqué précédemment, Panda est vraiment facile à mettre en œuvre. Il existe de nombreuses possibilités pour charger tous types de données structurées, le plus simple étant bien évidemment un bon vieux fichier CSV. Ensuite il ne vous reste plus qu'à vous familiariser avec les deux structures de données principales du framework: les `Series` (tableaux) et les `Dataframes` (~tableaux multidimensions). Le reste est [simple comme bonjour](https://www.youtube.com/watch?v=fvxFzBLv1s0) !

Le comptage `.count()` et le regroupement `.groupby()` sont des opérations très intuitives, performantes et bien [documentées](http://pandas.pydata.org/pandas-docs/stable/groupby.html#groupby). Je ne suis pas sûr que tout le monde puisse en dire [autant](http://stackoverflow.com/questions/10352346/group-by-in-sas).

Je n'ai pas grand chose à rajouter alors je finis sur cet aperçu d'un regroupement très simple au travers d'[IPython](http://en.wikipedia.org/wiki/IPython). Auf Wiedersehen !

![groupbypanda]({{ root_url }}/images/panda_groupby.jpg "group by avec panda")


### Ressources

- une très bonne [introduction](http://sametmax.com/le-pandas-cest-bon-mangez-en/) à Panda en français !
- un blog avec des [publications régulières](http://www.gregreda.com/tag/pandas.html) et bien écrites sur l'utilisation de la librairie
- une [cheatsheet](https://s3.amazonaws.com/quandl-static-content/Documents/Quandl+-+Pandas,+SciPy,+NumPy+Cheat+Sheet.pdf) qui ne fait pas de mal
