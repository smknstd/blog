---
layout: post
title: "Regrouper et compter: Hadoop (partie 4/6)"
date: 2014-07-14 16:00
comments: false
categories: 
---

Pour ce quatrième article de cette série sur le comptage et le regroupement, j'ai souhaité aborder Hadoop. Je préfère être honnête: je n'ai eut l'occasion de manipuler ce système que brièvement comparé à d'autres [technologies](http://en.wikipedia.org/wiki/Oracle_Grid_Engine) vaguement similaires et je n'en ai qu'une connaissance limitée. Toutefois, hadoop est je pense un système qui compte et je pense qu'il est important d'en comprendre le principe. Comme les autres articles de cette série, ne vous attendez pas à rentrer en profondeur car je me contenterai d'aborder rapidement la seule problématique du regroupement et du comptage.

### Hadoop

Hadoop, qui s'appuie notamment sur HDFS, est un framework Java qui permet de faire du traitement distribué à grande échelle sur des fichiers volumineux en mode batch. Developpé et mis en oeuvre par Yahoo au milieu des années 2000, il a ensuite nourrit beaucoup de fantasmes. Le buzz autour du "bigdata" a probablement vendu beaucoup de [rêve](http://service-architecture.blogspot.fr/2014/01/six-reasons-your-big-data-hadoop.html) et je ne suis pas convaincu que cette technologie soit adaptée à tant de problématiques et surtout de contextes qu'on aimerait le croire. Surtout avec ses nombreux coins sombres (jointures, debug, pig, etc) il reste définitivement un outil de spécialistes, difficile et coûteux à mettre en oeuvre.

### Regrouper et compter !

Heureusement pour nous, aborder la question de la cardinalité va nous permettre de bien comprendre le principe de fonctionnement du paradigme [map/reduce](http://en.wikipedia.org/wiki/MapReduce) puisqu'il est un des cas typiques d'utilisation. 

__TL;DR__: issu de la programmation fonctionnelle, son principe consiste a décomposer une tâche en sous-opérations parallélisables. Pour effectuer le comptage sur un ensemble, hadoop divise l'opération en plusieurs comptages sur des sous ensembles qu'il va pouvoir paralléliser et agrège le résultat.

![map_reduce]({{ root_url }}/images/mapreduce_sum.png "mapreduce1")

Aggréger ou "regrouper" des données sur une clef est une fonction qui est au cœur du framework. Notre fonction souhaité de comptage est donc plutôt simple à implémenter puisqu'on se sert principalement du comportement déjà existant. Schématiquement: toutes les valeurs de clefs sont regroupées et rassemblées dans le "reducer". Il suffit de sélectionner les champs sur lesquels on souhaite faire un regroupement dans le "mapper" et de mettre en place un compteur, le regroupement en lui même est entièrement pris en charge par le framework !


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
Regardez comme ça à l'air évident sur cet exemple d'illustration (que j'ai volé qqpart ;). Ne vous méprenez pas, cela à l'air très simple, pourtant comme évoqué en introduction ce concept appliqué à un cluster de machines de grande taille peut décomposer et paralléliser la tâche sur de multiples Datasets. Autrement dit effectuer une tâche immense très rapidement et uniquement avec du matériel "modeste" :)

![map_reduce]({{ root_url }}/images/mapreduce_combine.png "mapreduce2")

That's all, Folks !

### Ressources

- un [ouvrage](http://www.amazon.com/MapReduce-Design-Patterns-Effective-Algorithms/dp/1449327176) de référence sur le framework Map/Reduce
- une [vidéo](https://www.youtube.com/watch?v=oIkhgagvrjI) amusante d'explications sur un des compteurs les plus célèbres de notre époque
- map/reduce [éxpliqué](http://www.chrisstucchio.com/blog/2011/mapreduce_explained.html) aux enfants
