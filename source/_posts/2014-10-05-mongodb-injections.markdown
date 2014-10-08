---
layout: post
title: "Méfiez vous des injections même avec MongoDB"
date: 2014-09-24 08:11
comments: false
categories: 
---

L'[injection SQL](http://en.wikipedia.org/wiki/SQL_injection) est probablement l'un des sujets les plus populaires dans le domaine de la sécurité web. C'est aussi l'un des sujet les mieux [documenté](http://www.root-me.org/fr/Documentation/Web/Injection-SQL) et donc le mieux [pris en compte](http://stackoverflow.com/questions/60174/how-can-i-prevent-sql-injection-in-php).

Lorsqu'une application utilise un système de base de données qui ne dispose pas de la commodité du langage SQL, elle est par la même occasion épargnée par les risques d'injection généralement liés. Pour autant, il ne faudrait pas systématiquement se croire à l’abri de comportements malicieux.

### MongoDB

Pour interroger une collection ou modifier des données, on n'écrit pas de requête comme on pourrait le faire en SQL, et surtout cela ne prend pas la forme d'une chaîne de caractères. A travers les librairies "client" des différents langages, on utilise généralement une méthode spécifique pour l'opération souhaitée qui se traduit ensuite par la création d'un objet BSON. En conséquence la logique de l'injection SQL ne peut pas s'appliquer. On ne pourra jamais profiter d'une opération de consultation pour modifier ou supprimer des données ! #YOUPI

#### Javascript coté serveur

Toutefois, comme le tempère la [documentation officielle](http://docs.mongodb.org/manual/faq/developers/#how-does-mongodb-address-sql-or-query-injection), il faut se méfier de certaines opérations spécifiques qui peuvent exécuter du Javascript arbitraire directement sur serveur comme `$where` ou `group`. 

> You must exercise care in these cases to prevent users from submitting malicious JavaScript.

#### Caractère réservé aux opérateurs

D'autre part, Mongo réserve le caractère `$` à certains opérateurs comme ceux de [comparaison](http://docs.mongodb.org/manual/reference/operator/query-comparison/) par exemple.

> Field names in MongoDB’s query language have semantic meaning. The dollar sign (i.e $) is a reserved character used to represent operators (i.e. $inc.) Thus, you should ensure that your application’s users cannot inject operators into their inputs.

On peut [trouver](http://cirw.in/blog/hash-injection), [plusieurs](https://www.idontplaydarts.com/2010/07/mongodb-is-vulnerable-to-sql-injection-in-php-at-least/) [ressources](http://blog.websecurify.com/2014/08/hacking-nodejs-and-mongodb.html) qui mettent en lumières des cas pratiques dans lesquels le comportement attendu d'une requête est modifié grâce à l'utilisation malicieuse d'un __opérateur__ à la place d'une valeur habituelle.

Tiré d'un de ces articles, voici un exemple frappant (parmi d'autres) dans lequel un utilisateur, grâce à une implémentation php trop permissive, a pu substituer l'opérateur "not equal" (`$ne`) à la valeur du mot de passe et réussir l'authentification (sauf dans le cas où le mot de passe était valorisé à 1 lol). Bien sûr dans cet exemple un peu naïf, on valorise les paramètres directement dans l'url: `login.php?username=admin&passwd[$ne]=1`. Voilà comment on pourrait représenter l’interprétation problématique et trop permissive qui en découle:

	$collection->find(array(
    	"username" => "admin",
    	"passwd" => array("$ne" => 1)
	));


Ces exemples montrent bien que même si l'interrogation sur MongoDB est par nature plus solide face aux injections, il faut malgré tout rester vigilent lorsqu'on conçoit l'interface liée. Tout comme on a appris le faire avec SQL, avec Mongo, il faut aussi porter un soin particulier à vérifier le contenu des valeurs paramétrées par l'utilisateur.

Passez une bonne journée :)


### Ressources

- une bonne petite [blague](http://xkcd.com/327/)
- une [note](http://php.net/manual/en/mongo.security.php) officielle sur la sécurité du client php de mongo