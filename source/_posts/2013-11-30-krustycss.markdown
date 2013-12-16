---
layout: post
title: "SASS/Compass pour les nuls"
date: 2013-11-21 20:03
comments: false
categories: 
---

Je suis sûrement loin d'être un développeur web très expérimenté, pourtant l'écriture de ma première feuille de style CSS remonte à plus de 15 ans. Depuis ce temps j'ai bien eut le temps de comprendre que cet outil pouvait vite montrer ses limites, et réussir à organiser et maintenir une CSS fournie demandait des bons outils et beaucoup de discipline pour dire le moins. En modifiant le thème de base d'octopress, j'avais enfin l'occasion de m'introduire à SASS et Compass. Même sans en faire une utilisation sophistiquée (j'ai délibérément réduit ce blog à seulement deux pages plutôt basiques), je pense en avoir cerné les principaux avantages.

## Partials
Quiconque a déjà modifié une feuille de style CSS sait que d'en organiser le contenu peut vite ressembler à un casse tête. Même avec de la bonne volonté, le résultat ressemble le plus souvent à fichier à rallonge où il est difficile de s'y retrouver. Bien sûr, il a toujours été possible de s'organiser en séparant le contenu dans plusieurs feuilles de styles. Mais les combiner sur une même page avec la directive `@import`

```
@import url('style.css');
```

ou plusieurs balises <link>

```
<link rel="stylesheet" href="structure.css" media="all">
<link rel="stylesheet" href="banner.css" media="all">
```
pose des problèmes évidents de performance de rendu par les navigateurs qui doivent gérer (souvent mal) plusieurs requêtes en //. SASS propose une solution ! On réparti notre code dans plusieurs fichiers SCSS qu'on préfixe avec `_`: ce sont les _"partials"_. C'est une norme qui permet au moteur de savoir que pour ce fichier il ne faut pas généré de fichier CSS. Ensuite on utilise la directive `import` pour inclure ces _"partials"_. C'est un moyen astucieux d'[organisation](https://github.com/imathis/octopress/blob/master/.themes/classic/sass/screen.scss) !

Toutefois je souhaite modérer ce propos parce même si c'est une amélioration notable, même séparée dans plusieurs fichiers l'organisation reste compliquée. J'avoue avoir eut parfois du mal à m'y retrouver dans la division choisie dans octopress.

Et les outils habituels de developpement tels firebug font désormais face un problème et ne permettent pas mieux s'y retrouver puisqu'ils affichent la réréfence au css compilé. Heureusement il existe une solution: la variable `debug_info`. Bien entendu _octopress_ est un outils de developpeur et a pensé à [ça](https://github.com/imathis/octopress/pull/1120). Malheuresement je n'ai pas réussi à faire fonctionner le [plugin](https://addons.mozilla.org/fr/firefox/addon/firesass-for-firebug/) firefox sensé compléter _"firebug"_. Celui-ci ne semble plus [maintenu](https://github.com/nex3/firesass). 

On peut aussi chercher manuellement le fichier concerné! J'ai pris l'habitude d'utiliser [ack](http://beyondgrep.com/) qui est un outil de commodité très intéressant. Malheuresement il m'a aussi donné du fil à retordre dans ce cas parceque par défaut il exclut justement les fichiers de SASS. Il suffit de modifier son fichier `.ackrc` et d'y ajouter par exemple:

```
--type-add
scss=.sass,.scss
```

![ack_sass]({{ root_url }}/images/ack-scss.png "ack")



## Commentaires
Les commentaires sont toujours une bonne pratique d'organisation. Malheureusement dans une feuille de style publique, ils ne servent à rien si ce n'est gêner les performances puisqu'ils alourdissent le fichier. SASS en tant que préprocesseur est un outils idéal pour résoudre ce problème: on peut commenter autant qu'on le souhaite sans que ces commentaires n'apparaissent dans le fichiers CSS produit. #WIN


SCSS
```scss
// Commentaire SASS
/* Commentaire HTML */
$rouge: #FF0000;

alerte {
    font-color: $rouge;
}
```

CSS
```css
/* Commentaire HTML */

alerte {
    font-color: #FF0000;
}
```


## Variables/Opérateurs
Généralement plusieurs caractéristiques sont redondantes dans les définitions d'une même feuille de style: les couleurs, les polices, etc. SASS met à disposition le moyen de rendre ces définitions plus dynamiques avec les variables. On attribue une variable à tous les elements qui ont un attribut en comun et le modifier devient très simple. Les variables ont aussi la possibilité de supporter des opérations ! Exemple :

SCSS
```scss
$largeur: 400px;

grand {
    width: $largeur;
}

petit {
    width: $largeur/2;
}
```

CSS
```css
droite {
    width: 400px;
}

gauche {
    width: 200px;
}
```

## minification
La taille de votre CSS [compte](http://browserdiet.com/fr/#minify-css). Compass met à disposition la possibilité de minifier facilement votre feuille de style dans votre projet. Et c'est très pratique.

`output_style = :compressed`



Bien sûr Compass est un outils qui permet des usages beaucoup plus sophistiqués que les fonctions basiques que j'ai cité (mixin, génération automatique de sprites, etc). Il faut aussi bien se rappeler que SASS à un cout puisqu'il complique sensiblement le workflow de developpement !


## BONUS

 Je suis tombé sur cette [démo](http://davidwalsh.name/street-fighter) intéressante. J'ai trouvé que c'etait une bonne illustration du concept de _"mixin"_. Je l'ai simplement reproduit avec "Krusty". J'ai rapidement bricolé deux sprites... Rien de sensationnel mais voici le résultat:

<p data-height="348" data-theme-id="0" data-slug-hash="ELhdf" data-user="smknstd" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/smknstd/pen/ELhdf'>Krusty's fun CSS</a> by smokin studio (<a href='http://codepen.io/smknstd'>@smknstd</a>) on <a href='http://codepen.io'>CodePen</a></p>
<script async src="//codepen.io/assets/embed/ei.js"></script>


### Ressources

- [SCSS bundle](https://github.com/MarioRicalde/SCSS.tmbundle/tree/SublimeText2) de coloration syntaxique pour sublime text 2
- [extrait du speech](http://youtu.be/bqfoYaKCYUI?t=5m30s) de Paul Irish sur le workflow SASS

