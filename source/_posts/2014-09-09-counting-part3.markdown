---
layout: post
title: "Regrouper et compter: Unix (partie 3/5)"
date: 2014-07-04 18:00
comments: false
categories: 
---
Pour continuer cette série d’articles de présentation sur la thématique du regroupement et du comptage, j'ai souhaité aborder ce qui pour moi reste souvent mon premier choix et un automatisme : les outils en ligne de commande d'UNIX ! Lorsque vous utilisez un de ces systèmes, vous héritez d'innombrables programmes simples, efficaces et robustes. Pourquoi perdre du temps avec de nouveaux outils quand ceux qu'on a déjà conviennent ?

Lorsqu'on souhiate compter le nombre d'éléments uniques on peut s'en sortir très simplement avec seulement deux d'entre eux:

- sort
- [uniq](http://en.wikipedia.org/wiki/Uniq) et son option `-c`

La puissance du modèle UNIX fournit beaucoup de souplesse et permet d'effectuer de nombreuses opérations en cumulant successivement plusieurs fonctionnalités grâce à l'utilisation du [pipe](http://doc.cat-v.org/unix/pipes/). Pour ne citer que quelques-autres de ces indispensables outils:

- grep
- wc
- join
- cut

En plus d'être bien documentés, open source et faciles à utiliser, ces outils sont généralement [très](http://lists.freebsd.org/pipermail/freebsd-current/2010-August/019310.html) [efficients](http://en.wikipedia.org/wiki/Merge_sort).

Une fois qu'on s'y est habitué, il est difficile de s'en passer. J'ai découvert que même lorsqu'on ne disposait que d'un environnement Windows, il est possible de profiter de quelques-uns de ces outils grâce à certaines solutions comme [MKStoolkit](http://en.wikipedia.org/wiki/MKS_Toolkit) ou [cygwin](http://en.wikipedia.org/wiki/Cygwin). Précisons que c'est une utilisation dégradée par rapport aux outils originaux, car selon mon expérience la qualité du portage et de l'implémentation laissent largement à désirer.

![mkstoolkit]({{ root_url }}/images/mkstoolkit.png "nutcracker")

### Big Data

Quand la taille des datasets augmente, l'approche stricte des algorithmes classiques se montre souvent trop gourmande en ressources. La consommation de mémoire et le temps d’exécution notamment peuvent devenir problématiques. Certaines techniques proposent une approche probabiliste pour améliorer les performances. Citons "Hyperloglog" basé sur les travaux du malheureusement défunt chercheur français [Philippe Flajolet](http://inria-alumni.fr/index.php/fr/page/article/id/202), qui permet de compter les éléments uniques dans une collection au prix d'une faible approximation (faux positifs). Mais d'autres algorithmes qui partagent l'approche comme "[Bloomfilter](http://en.wikipedia.org/wiki/Bloom_filter)" existent. Ils sont implémentés par les géants du web (cassandra, hadoop, google, etc) depuis plusieurs années. Les ressources notamment issues de la recherche abondent, si vous souhaitez tenter de comprendre comment ces algorithmes fonctionnent ;)

Il est évident que ces outils représentent une petite révolution et que leur utilisation va tendre à se démocratiser. La page wikipedia de "[hyperloglog](http://en.wikipedia.org/wiki/HyperLogLog)" n'a été créée qu'en mars 2014 !? Le célèbre système de gestion de "clé/valeur" `redis`, en a déjà fait une [implémentation](http://antirez.com/news/75) (disponible depuis avril 2014 avec la version 2.8.9) que j'aimerai d'ailleurs décrire dans un prochain article :) J'ai pour ma part fait quelques essais très concluents avec une implémentation en java que je ne saurai trop vous conseiller: [stream-lib](https://github.com/addthis/stream-lib)

![count]({{ root_url }}/images/count-bundesliga.png "count unixVSbloom")

### Ressources

- un [article](http://www.gregreda.com/2013/07/15/unix-commands-for-data-science/) sur les outils d'UNIX en lignes de commande
- quelques [explications](http://lists.freebsd.org/pipermail/freebsd-current/2010-August/019310.html) du dev de GNU grep
- un [article](http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html) générique sur la cardinalité plein à craquer de buzzwords
- un [article](http://research.neustar.biz/2012/10/25/sketch-of-the-day-hyperloglog-cornerstone-of-a-big-data-infrastructure/) sur hyperloglog
- un [article de 2009](http://spyced.blogspot.fr/2009/01/all-you-ever-wanted-to-know-about.html) sur les bloomfilters
- un autre [article](http://billmill.org/bloomfilter-tutorial/) sur les bloomfilters avec de nombreuses références
