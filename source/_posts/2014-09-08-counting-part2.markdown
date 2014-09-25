---
layout: post
title: "Regrouper et compter: Mongo (partie 2/5)"
date: 2014-06-28 11:12
comments: false
categories: 
---

Pour continuer cette série d'articles de présentation sur la thématique du regroupement et du comptage, j'ai souhaité aborder la base orientée documents [Mongo](http://en.wikipedia.org/wiki/MongoDB). L'explication est simple car parmi les bases de données non-relationnelles qui ont tendance à se démocratiser ces dernières années, c'est celle que je connais le mieux ;)

D'abord je souhaite rappeler que Mongo est un outil qui dans le paysage des outils de gestion de base de données est relativement jeune (2009). Bien que Mongo ait rencontré un succès massif en répondant à de nombreux besoins notamment de flexibilité et de performance, la manipulation des données ne me semble pas avoir été au cœur des préoccupations dès le début. Les capacités du système à répondre aux besoins d'analyse des données ont amené certains utilisateurs à de sérieuses déconvenues. En effet, en plus de ne mettre à disposition que des outils d'interrogation ["sommaires"](http://docs.mongodb.org/manual/tutorial/map-reduce-examples/) pour dire le moins, la performance de ceux-ci est grandement affecté par des choix souvent subtiles lors de la modélisation !


### Aggregation framework

Heureusement, le système évolue vite et des [améliorations](https://jira.mongodb.org/browse/SERVER-1752) sont sans cesse apportées. Une évolution majeure a été l'apparition du "framework d'aggregation" dans la version 2.2 (circa Aout 2012) qui apporte une bouffée d’oxygène pour quiconque souhaite manipuler une collection. Sans pour autant résoudre les problématiques de performance qui dépendent toujours notamment des choix de modélisation des données.


### Indexs

L'utilisation d'indexs est essentiel au fonctionnement de Mongo. Leur mise en place et leur maintenance nécessite un travail important. Malgré une amélioration des outils essentiels permettant d'analyser leur utilisation comme `.explain()` ou `.hint()`, cette tâche reste difficile et selon mon expérience quelquefois obscure malheureusement. Voici un plan d’exécution d'une interrogation simple utilisant une "projection" sur une collection qui montre qu'on ne peut solliciter que l'index quand c'est possible (`index_only: true`). Dans ce cas on économise l'accès à la collection ce qui évidemment améliore le temps de réponse de l'interrogation:

```
> db.wines.find({proprietaire : "Arnold"},{proprietaire: 1, _id: 0}).explain()
{
	"cursor" : "BtreeCursor proprietaire_1",
	"isMultiKey" : false,
	"n" : 3,
	"nscannedObjects" : 0,
	"nscanned" : 3,
	"nscannedObjectsAllPlans" : 0,
	"nscannedAllPlans" : 3,
	"scanAndOrder" : false,
	"indexOnly" : true,
	"nYields" : 1,
	"nChunkSkips" : 0,
	"millis" : 26,
	"indexBounds" : {
		"proprietaire" : [
			[
				"Arnold",
				"Arnold"
			]
		]
	},
	"server" : "xxxxxx.mongolab.com:xxxxxx",
	"filterSet" : false
}
```


Pour revenir à notre sujet du comptage du nombre d'éléments uniques, ce n'est toujours pas un des points fort de Mongo. Consulter le plan d’exécution de la fonction ".aggregate()" du framework d'aggregation n'est disponible que depuis la version 2.6 (début 2014). Malgré cela, selon mon expérience, il reste difficile d'analyser le comportement des requêtes de ce type et pire il toujours presque impossible d'[influer](https://jira.mongodb.org/browse/SERVER-7944) sur le comportement !? Par exemple, dans cette requête de regroupement et de comptage je ne peux que constater que l’accès se fait par un curseur "basique" (comprenez `FullScan`) qui semble ignorer l'index que j'ai pourtant réussi à utiliser précédemment et qui contient bien l'information dont on a besoin :(


```
> db.wines.aggregate(    [      { $group : { _id : "$proprietaire", books: { $sum : 1 } } }    ] )
{ "_id" : "Arnold", "books" : 3 }
{ "_id" : "Regis", "books" : ...

> db.wines.aggregate(    [      { $group : { _id : "$proprietaire", books: { $sum : 1 } } }    ],   {     explain:true   } )
{
	"stages" : [
		{
			"$cursor" : {
				"query" : {
					
				},
				"fields" : {
					"proprietaire" : 1,
					"_id" : 0
				},
				"plan" : {
					"cursor" : "BasicCursor",
					"isMultiKey" : false,
					"scanAndOrder" : false,
					"allPlans" : [
						{
							"cursor" : "BasicCursor",
							"isMultiKey" : false,
							"scanAndOrder" : false
						}
					]
				}
			}
		},
		{
			"$group" : {
				"_id" : "$proprietaire",
				"books" : {
					"$sum" : {
						"$const" : 1
					}
				}
			}
		}
	],
	"ok" : 1
}
```

### Ressources

- un [article](http://web.archive.org/web/20100128030005/http://kylebanker.com/blog/2009/11/mongodb-count-group) précurseur sur le comptage
- un [article](http://fiestacc.tumblr.com/post/11319522700/walkthrough-mongodb-data-modeling) fondateur sur les subtilités d'utilisation et de modélisation
- une [synthèse](http://highlyscalable.wordpress.com/2012/03/01/nosql-data-modeling-techniques/) sur les différentes techniques de modélisation adaptées aux systèmes dits "NOSQL"