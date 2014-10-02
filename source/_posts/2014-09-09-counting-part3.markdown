---
layout: post
title: "Regrouper et compter: Unix (partie 3/6)"
date: 2014-07-04 18:00
comments: false
categories: 
---
Pour continuer cette série d’articles de présentation sur la thématique du regroupement et du comptage, j'ai souhaité aborder ce qui pour moi reste souvent mon premier choix et un automatisme : les outils en ligne de commande d'UNIX ! Lorsque vous utilisez un de ces systèmes, vous héritez d'innombrables programmes simples, efficaces et robustes. Pourquoi perdre du temps avec de nouveaux outils quand ceux qu'on a déjà conviennent ?

Lorsqu'on souhiate compter le nombre d'éléments uniques on peut s'en sortir très simplement avec seulement deux d'entre eux:

- sort
- [uniq](http://en.wikipedia.org/wiki/Uniq) et son option `-c`

#### Limites

Même si l'implémentation de ces outils utilise ce qu'il y a de plus [performant](http://en.wikipedia.org/wiki/Merge_sort), leur utilisation reste coûteuse pour le système. En plus du temps d’exécution, la taille des données à regrouper et compter ne peut pas dépasser les capacités physiques de la machine (RAM notamment).

### "Les bons outils font les bons ouvriers"

La puissance du modèle UNIX fournit beaucoup de souplesse et permet d'effectuer de nombreuses opérations en cumulant successivement plusieurs fonctionnalités grâce à l'utilisation du [pipe](http://doc.cat-v.org/unix/pipes/). Pour ne citer que quelques-autres de ces indispensables outils:

- grep
- wc
- join
- cut

En plus d'être bien documentés, "libres" et faciles à utiliser, là encore ces outils sont généralement très [efficients](http://lists.freebsd.org/pipermail/freebsd-current/2010-August/019310.html).

Une fois qu'on s'y est habitué, il est difficile de s'en passer. J'ai découvert que même lorsqu'on ne disposait que d'un environnement Windows, il est possible de profiter de quelques-uns de ces outils grâce à certaines solutions comme [MKStoolkit](http://en.wikipedia.org/wiki/MKS_Toolkit) ou [cygwin](http://en.wikipedia.org/wiki/Cygwin). Précisons que c'est une utilisation dégradée par rapport aux outils originaux, car selon mon expérience la qualité du portage et de l'implémentation laissent largement à désirer.

![mkstoolkit]({{ root_url }}/images/mkstoolkit.png "nutcracker")


### Ressources

- un [article](http://www.gregreda.com/2013/07/15/unix-commands-for-data-science/) sur les outils d'UNIX en lignes de commande
- un [article](http://vkundeti.blogspot.fr/2008/03/tech-algorithmic-details-of-unix-sort.html) qui s'interesse à l'algorithme de la commande "sort"
- quelques [explications](http://lists.freebsd.org/pipermail/freebsd-current/2010-August/019310.html) du dev de GNU grep
- [page](http://roesler-ac.de/wolfram/acro/Cmd.htm) d'explication des acronymes unix ;)
