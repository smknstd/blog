---
layout: post
title: "Regrouper et compter: SQL (partie 1/6)"
date: 2014-05-23 19:23
comments: false
categories: 
---
Compter des données ou en estimer la cardinalité est une opération dont les applications sont très variées mais surtout tellement utiles et courantes que tout le monde ou presque a besoin de le faire. Au fil de mes expériences, j'ai pu observer de nombreux moyens de le faire en fonction des contextes et de la grande variété des outils utilisés. Bien que ce soit une opération basique, sa mise en œuvre révèle souvent des subtilités. Cette série d'articles se veut être une brève présentation non-exhaustive et surtout un peu naïve des opérations de comptage sous différentes technologies. Je vais essayer d'aborder à certains moments quelques aspects liés aux performances, toutefois sans rentrer dans beaucoup de détails malheureusement.

Il m'a semblé naturel de commencer par les SGBDR, car c'est probablement l'outil le plus rependu (après le bricolage dans Microsoft Excel et ses [tableaux croisés dynamiques](http://en.wikipedia.org/wiki/Pivot_table) ;). Lorsque les données sont correctement rangées dans un SGBDR en état de marche, la facilité d'utilisation et la puissance du langage SQL est évidente. Même s'il est à la mode de remettre en cause la pertinence de cet outil pour certains besoins, se priver de ses possibilités reste souvent douloureux ! Sous son abstraction et son apparente simplicité, on oublie souvent que ce langage d’interrogation est issu d'un héritage théorique complexe.

### SQL

`COUNT` et `GROUP BY` sont les protagonistes principaux du comptage du nombre d'éléments uniques. Quand les besoins se compliquent, la palette d'outils généralement proposés par les principaux systèmes répond aux cas d'utilisation les plus complexes: `WHERE`, `DISTINCT` , `EXISTS` , `HAVING` , etc.

![mysql]({{ root_url }}/images/mysql-count.png "mysql count() group by")

Le volume de données traité reste un enjeu majeur de l'usage des SGBDR et le niveau d'exigence requis en dépend la plupart du temps. Les contraintes liés à la performance des différents systèmes ont historiquement souvent posés des problèmes à la patience des utilisateurs notamment lors de la consultation de rapports d'analyses. Il est courant d'avoir recours à des [pré-calcul](http://en.wikipedia.org/wiki/Aggregate_%28data_warehouse%29) pour résoudre ce problème mais cela mériterait un autre article pour être développé.

### Index Bitmap

L'outil essentiel pour améliorer les performances des SGBDR est l'index. Il est généralement bien connu et largement utilisé. Toutefois, il en existe de plusieurs types et l'un d'eux reste assez méconnu : l'index [bitmap](http://en.wikipedia.org/wiki/Bitmap_index). Si vous ne le connaissez pas, je vous encourage à étudier son principe de fonctionnement. Il peut avoir des usages variés comme aider aux jointures mais nécessite de nombreuses précautions et impose de nombreuses contraintes et un niveau de connaissance avancé. Par exemple, ces indexs ne doivent généralement porter que sur une seule colonne, ce qui n’empêche bien sûr pas de cumuler leurs utilisations car au contraire ils sont particulierement efficaces pour combiner les critères. Concernant notre calcul, cet index peut se révéler en théorie très efficace puisqu'il contient à lui seul toute l'information dont nous avons besoin et ainsi éviter le parcours de la table (et donc de précieux accès "disque").

Même si l'implémentation interne détaillée des indexs bitmaps dans la plupart des SGBDR reste obscure et mal documentée, on peut observer leur fonctionement au travers du [plan exécution](http://en.wikipedia.org/wiki/Query_plan). Ci-dessous, celui d'un comptage mettant à contribution un index bitmap sous Oracle 11g. Notez bien l'étape de conversion, qui compte simplement le nombre d'entrées, sans jamais se servir de leur adresse (rowid): 

![bitmap]({{ root_url }}/images/bitmap.png "bitmap plan")


### Bases vectorielles

Dans la famille des systèmes relationnels, une autre approche prometteuse souhaite répondre à la problématique du volume de données: les [bases vectorielles](http://en.wikipedia.org/wiki/Vectorwise). Basé sur une orientation [colonne](http://en.wikipedia.org/wiki/Column-oriented_DBMS) tout en gardant la commodité de l'interrogation d'un modèle relationnel, cette base promet des performances incroyables. Gardons à l'esprit que le gain se fait bien sûr au détriment de quelques sacrifices comme les contraintes ou le principe de transaction. Privé de polyvalence, elles sont exclues de nombreux domaines d'applications opérationnels.


### Ressources

- les [données](http://www.football-data.co.uk/germanym.php) du championnat de football allemand que j'ai utilisé pour certains exemples
- un [exemple détaillé](http://web.archive.org/web/20130113080935/http://backstage.soundcloud.com/2011/07/mysql-stats-old-faithful/) pas si vieux que j'aime beaucoup sur les problèmes de performance de regroupements et comptage et des subtilités du moteur de mysql avec de grosses volumétries