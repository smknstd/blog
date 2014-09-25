---
layout: post
title: "Regrouper et compter: Hadoop (partie 4/5)"
date: 2014-07-14 16:00
comments: false
categories: 
---

Pour ce quatrième article de cette série sur le comptage et le regroupement, j'ai souhaité aborder Hadoop que je n'ai eut l'occasion de manipuler que brièvement comparé à d'autres [technologies](http://en.wikipedia.org/wiki/Oracle_Grid_Engine) vaguement similaires. Comme les autres articles de cette série ne vous attendez pas à rentrer en profondeur car je me contenterai d'aborder rapidement la seule problématique du comptage.

### TL;DR

Hadoop, qui s'appuie notamment sur HDFS, est un framework Java qui permet de faire du traitement distribué sur des fichiers volumineux en mode batch. Le buzz autour du "bigdata" a probablement vendu beaucoup de [rêve](http://service-architecture.blogspot.fr/2014/01/six-reasons-your-big-data-hadoop.html) et je ne suis pas convaincu que cette technologie soit adapté à tant de problématiques qu'on aimerait le croire. Surtout avec ses nombreux coins sombres (jointures, debbug, pig, etc) il reste définitivement un outil de spécialistes difficile à mettre en oeuvre.

### Regrouper et compter !

Heureusement pour nous, aborder la question de la cardinalité va nous permettre de bien comprendre le principe de fonctionnement du paradigme [map/reduce](http://en.wikipedia.org/wiki/MapReduce). TL;DR: issu de la programmation fonctionnelle, le principe consiste a décomposer une tâche pour opérations paralelisables. Pour effectuer le comptage sur un ensemble, hadoop divise l'opération en plusieurs comptages sur des sous ensembles et agrège le résultat. 

![map_reduce]({{ root_url }}/images/mapreduce_sum.png "mapreduce1")

"regrouper" des données sur une clef est une fonction qui est donc au cœur du framework. Il est donc plutôt simple à implémenter puisqu'on se sert principalement du comportement déjà existant. Schématiquement: toutes les valeurs de clefs sont regroupées et rassemblées dans le "reducer". Il suffit de sélectionner les champs sur lesquels on souhaite faire un regroupement dans le "mapper", le regroupement en lui même est entièrement pris en charge par le framework !



Voici les quelques lignes de java qui suffisent à implémenter un comptage de cardinalité dans le "reducer":

``` java
public static class CountReducer extends Reducer<Text, MinMaxCountTuple, Text, MinMaxCountTuple> {
	// Our output value Writable
	private CountTuple result = new CountTuple();

	public void reduce(Text key, Iterable<CountTuple> values,Context context) throws IOException, InterruptedException {
		// Initialize our result
		result.setCount(0);
		int sum = 0;

		// Iterate through all input values for this key
		for (CountTuple val : values) {
			// Add to our sum the count for value
			sum += val.getCount();
		}
		// Set our count to the number of input values
		result.setCount(sum);
		context.write(key, result);
	}
}
```
Regardez comme ça à l'air simple sur cet exemple d'illustration (que j'ai volé qqpart ;).

![map_reduce]({{ root_url }}/images/mapreduce_combine.png "mapreduce2")

That's all, Folks !

### Ressources

- un [ouvrage](http://www.amazon.com/MapReduce-Design-Patterns-Effective-Algorithms/dp/1449327176) de référence sur le framework Map/Reduce
- une [vidéo](https://www.youtube.com/watch?v=oIkhgagvrjI) amusante d'explications sur un des compteurs les plus célèbres de notre époque