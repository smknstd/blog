---
layout: post
title: "Survivre aux caractères non-imprimables"
date: 2013-10-22 23:56
comments: true
categories:
---

Lorsqu'on manipule des données, on se heurte bien souvent à des subtilités comme l'encodage des caractères. Si souvent d'ailleurs, que s'en méfier devient un automatisme, voir une seconde nature. Par contre, je n'ai jamais réussi à m'habituer aux caractères [non imprimables](http://en.wikipedia.org/wiki/Control_character).

En écrivant des flux d'[intégration de données](http://en.wikipedia.org/wiki/Data_integration), j'ai souvent rencontré des erreurs inattendues et des comportements anormaux sur différentes technologies.

Même dans l'investigation d'un problème, le plus dur est parfois de simplement les identifier:

```
>  sqlplus -l -s user/password@INSTANCE @test.sql | cat
REVISION DES PRIX 2011 coef 1.107138
reçu le 22/07/2011 mis en application le
22/08/2011 (retour congé)
```

Heureusement il existe [plusieurs](http://www.thelinuxtips.com/tag/cat-v-command/) [moyens](http://stackoverflow.com/questions/6943928/show-special-characters-in-unix-while-using-less-command) de les afficher:

```
>  sqlplus -l -s user/password@INSTANCE @test.sql | cat -v
REVISION DES PRIX 2011 coef 1.107138^M
reM-gu le 22/07/2011 mis en application le
22/08/2011 (retour congM-i)^M
```

Et pour moi, l'outil de loin le plus utile s'appelle: [od](http://en.wikipedia.org/wiki/Od_%28Unix%29). Avec ses options de détails caractère par caractère, il permet non seulement d'imprimer à l'écran l'imprimable, mais il s'avère aussi utile sur de nombreuses problématiques, notemment pour élucider certaines ambiguités d'encodage. Le connaissiez-vous ?

```
>  sqlplus -l -s user/password@INSTANCE @test.sql | od -bc

0000000 122 105 126 111 123 111 117 116 040 104 105 123 040 120 122 111
          R   E   V   I   S   I   O   N       D   E   S       P   R   I
0000020 130 040 062 060 061 061 040 143 157 145 146 040 061 056 061 060
          X       2   0   1   1       c   o   e   f       1   .   1   0
0000040 067 061 063 070 015 012 162 145 347 165 040 154 145 040 062 062
          7   1   3   8  \r  \n   r   e 347   u       l   e       2   2
0000060 057 060 067 057 062 060 061 061 040 155 151 163 040 145 156 040
          /   0   7   /   2   0   1   1       m   i   s       e   n
0000100 141 160 160 154 151 143 141 164 151 157 156 040 154 145 012 062
          a   p   p   l   i   c   a   t   i   o   n       l   e  \n   2
0000120 062 057 060 070 057 062 060 061 061 040 050 162 145 164 157 165
          2   /   0   8   /   2   0   1   1       (   r   e   t   o   u
0000140 162 040 143 157 156 147 351 051 015 012 012
          r       c   o   n   g 351   )  \r  \n  \n
0000153
```
