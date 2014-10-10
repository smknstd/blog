---
layout: post
title: "Regrouper et compter: Algorithmes d'approximation (partie 5/6)"
date: 2014-08-03 14:20
comments: false
categories: 
---

Bienvenue dans le 5ème article de cette série dédiée au regroupement et comptage, une opération courante pour ne pas dire systématique dans les applications actuelles. Pour rappel, j'essaie dans chaque article de décrire assez naïvement les possibilités et l'approche avec différentes technologies.

Quand la taille des datasets que l'on souhaite traiter augmente, l'approche stricte des algorithmes classiques se montre souvent trop gourmande et dépasse vite les ressources disponibles. La consommation de mémoire ou le temps d’exécution notamment peuvent devenir problématiques. Il existe une famille d'algorithmes qui propose une approche différente radicalement moins consommatrice en mémoire, à de nombreux problèmes dont le comptage de cardinalité: [Streaming algorithms](http://en.wikipedia.org/wiki/Streaming_algorithm).

Loin d'avoir les connaissances suffisantes, je vais m'abstenir d'essayer d'expliquer leur fonctionnement détaillé, je vais me contenter de vous expliquer leur utilité et quelques cas d'utilisations des plus populaires d'entre eux. En simplifiant beaucoup, ces outils permettent de traiter les éléments uniques de manière miraculeuse et donc de procéder à des regroupements. En contre partie, la plupart d'entre eux sont basés sur des structures probabilistes qui ne garantissent pas toujours l'exactitude du résultat. Certaines valeurs sont inexactes selon un certains taux d'erreur ([défini](http://hur.st/bloomfilter)) généralement faible :)

__TL;DR:__ En sacrifiant l'exactitude absolue des résultats, une famille d'algorithme permet de réduire radicalement les coûts de calcul, notamment pour les besoins habituels des statistiques.

Issu de la recherche, le domaine n'est pas nouveau mais reste un sujet réservé aux experts. De nombreux outils qui font face à de grands volumes de données ou à de faible latences les implémentent dans leur fonctionnement interne. Toutefois leur utilisation commence à se populariser, et même si aucun outil comme ceux de bases de données ne les met à disposition directement (comme structure de données par exemple), des librairies dans de nombreux langages de programmation tentent elles d'en faire des implémentations.

#### Bloom filters

"[Bloomfilter](http://en.wikipedia.org/wiki/Bloom_filter)" est probablement le plus répandu. En simplifiant beaucoup, il permet de rapidement répondre par "oui/non" à une question du type "Est ce que cette clef existe dans ce dataset ?". En gros il est généralement utilisé pour éviter d'effectuer une opération coûteuse quand ce n'est pas nécessaire ! Il est souvent implémenté sans qu'on le sache au cœur de nombreuses applications comme les moteurs de base de données. C'est notamment google qui a popularisé son utilisation, mais  il est désormais connu que tous les géants du web s'en servent. Voici quelques cas d'utilisation:

- optimisation de requête: éviter de parcourir une partition si l'information n'est pas présente
- black list: interdire l’accès si l'information est présente
- publicité en ligne: éviter d'afficher une publicité déjà montrée
- statistiques web : compter les visites uniques
- etc

En plus des "faux positifs" qui excluent certains domaines d'application qui ne peuvent pas se permettre d'erreur, d'autres limites à l'utilisation de cet outil existent. Citons notamment l'impossibilité de retirer une clef de la structure, une fois insérée ! 

Pour rejoindre le sujet de notre article, une variation de cet outils existe qui peut répondre à notre problématique du regroupement et du comptage. Une version améliorée appelée "counting bloom filters" permet de stocker en plus dans la structure de données pour chaque valeur distincte de clef, un compteur. Évidemment les coûts liés à cette fonctionnalité ne sont pas négligeables (stockage, accès, temps de réponse). Une implémentation en java qui se sert de Redis comme support a particulièrement retenu mon attention: [Orestes Bloom filter library](https://github.com/Baqend/Orestes-Bloomfilter).


#### Hyperloglog

Hyperloglog est un autre outil encore plus récent basé sur les travaux du malheureusement défunt chercheur français [Philippe Flajolet](http://inria-alumni.fr/index.php/fr/page/article/id/202). Il met en œuvre une structure de données probabiliste qui permet lui de compter les éléments uniques dans une collection (comme dit précédemment au prix d'une faible approximation de faux positifs).

Concernant le sujet de l'article, il ne répond pas exactement à la même problématique puisqu'il ne permet pas de compter le nombre d’éléments pour une clef. Toutefois récemment (La page wikipedia de "[hyperloglog](http://en.wikipedia.org/wiki/HyperLogLog)" n'a été créée qu'en mars 2014 !?), il fait l'objet de beaucoup d'attention. En plus de nombreuses librairies qui l’implémentent, le système de gestion de "clé/valeur" `redis`, en a fait une [implémentation](http://antirez.com/news/75) (disponible depuis avril 2014 avec la version 2.8.9). 


#### Count-min sketch

Loin de moi l'idée de faire l'eventail complet de tous ces algorithmes, mais un dernier d'entre eux répond à la problématique de cette série d'articles: ["count-min sketch"](http://en.wikipedia.org/wiki/Count%E2%80%93min_sketch).

Celui ci n'est qu'une variation du bloom filter évoqué plus tôt. J'ai pour ma part fait quelques essais très concluants avec une implémentation en java que je ne saurai trop vous conseiller: [stream-lib](https://github.com/addthis/stream-lib). Ci-dessous la mise en œuvre "clef en main" en ligne de commande d'un regroupement et comptage:

![count]({{ root_url }}/images/count-bundesliga.png "count unixVSbloom")


### Ressources

- une [video](https://www.youtube.com/watch?v=Hpd3f_MLdXo) de présentation avec des exemples du "monde réel"
- une [série](http://blog.kiip.me/engineering/sketching-scaling-everyday-hyperloglog/) d'articles sur les techniques d'approximation
- un [article](http://highscalability.com/blog/2012/4/5/big-data-counting-how-to-count-a-billion-distinct-objects-us.html) générique sur la cardinalité plein à craquer de buzzwords
- un [article](http://antirez.com/news/75) extrêmement bien écrit sur l’implémentation d'hyperloglog dans Redis
- un [article](http://research.neustar.biz/2012/10/25/sketch-of-the-day-hyperloglog-cornerstone-of-a-big-data-infrastructure/) sur hyperloglog
- un [article de 2009](http://spyced.blogspot.fr/2009/01/all-you-ever-wanted-to-know-about.html) sur les bloomfilters
- un autre [article](http://billmill.org/bloomfilter-tutorial/) sur les bloomfilters avec de nombreuses références