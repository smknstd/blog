---
layout: post
title: "Les arguments de fonctions en javascript"
date: 2015-01-25 15:40
comments: false
categories: 
---
Cet article souhaite décrire une caractéristique de javascript pas toujours très bien connue des programmeurs. C'est loin d'être un concept essentiel mais je pense qu'il mérite une attention. En javascript, au moment d'appeler une fonction, on peut préciser autant de paramètre que l'on souhaite, peu importe ceux qui sont précisés dans sa déclaration. Si on en liste moins, ceux-ci prendront la valeur `undefined`. Mais on peut aussi en lister plus !

### Le tableau des arguments

Le tableau `arguments` est un paramètre supplémentaire disponible lorsque les fonctions sont invoquées. Il donne à la fonction l'accès à tous les arguments fournis avec l'invocation, dont les arguments en excédent lorsqu'ils ne sont pas attribués à des paramètres existants. Il est donc possible d'écrire des fonctions qui prennent un nombre "indéfini" de paramètres !?

```
var sum = function (  ) {
    var i;
    var sum = 0;
    for (i = 0; i < arguments.length; i++) {
        sum += arguments[i];
    }
    return sum;
};

console.log(sum(1,2,3,4)); // 10
```

Il est souvent dit que cette feature n'a pas beaucoup d'utilité, elle est toutefois très puissante. 


### Une erreur d'implémentation

Mais javascript est un langage à l'histoire spéciale et il ne serait pas si discuté, si il n'amenait pas son lot de particularités. En effet, comme le cite l'ouvrage de référence "JavaScript: The Good Parts" de Douglas Crockford publié en 2008:

> Because of a design error, arguments is not really an array. It is an array-like object. arguments has a length property, but it lacks all of the array methods.

En effet, il est important de savoir que c'est en fait un Objet javascript qui ressemble beaucoup à un tableau (propriété `legnth` et utilisation possible de la bouvle `for`).

Heuresement il existe un moyen simple de le transformer en un "vrai" tableau et utiliser alors tous les outils de manipulation de tableaux que l'on souhaite comme `foreach` ou `join`. Voici deux variantes pour le faire:

```
var argArray = Array.prototype.slice.call(arguments);
```
ou 

```
var aRealArray = [].slice.call(arguments);
```

### Attention aux références

Il existe une autre particularité à ce "pseudo" tableau. Les parametres nommés dans la déclaration d'une fonction et `arguments` référencent les mêmes valeurs. En conséquence quand on modifie une valeur d'un paramètre "nommé" à l'interieur d'une fonction, sa valeur est aussi modifié dans le tableau `arguments` et reciproquement.

``` 
function yolo(state) {
  x = 'yes'
  alert(arguments[0])
}

yolo('no'); //yes
```
On dit généralement que modifier les valeurs de `arguments` n'est pas une __bonne pratique__ ;)


### Ressources

- [function arguments](http://jsforallof.us/2014/07/29/arguments/)
- [45 Useful JavaScript Tips, Tricks and Best Practices](http://modernweb.com/2013/12/23/45-useful-javascript-tips-tricks-and-best-practices/?utm_source=javascriptweekly&utm_medium=email)
- [Javascript tutorial](http://javascript.info/tutorial/arguments)
