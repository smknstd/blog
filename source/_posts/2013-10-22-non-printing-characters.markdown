---
layout: post
title: "Survivre aux caractères non-imprimables"
date: 2013-10-22 23:56
comments: false
categories:
---

Lorsqu’on manipule des données, on se heurte bien souvent à quelques subtilités. La première qui me vient à l'esprit est bien sûr l’encodage des caractères. C'est un très vaste sujet et je ne me sens pas capable de le résumer sur ce blog. Je me contenterai de citer quelques notes utiles dans la partie ressources, et vous rappeler deux ou trois choses qui m'ont fait perdre beaucoup de temps:


1. l'onglet ["translation"](http://tartarus.org/~simon/putty-snapshots/htmldoc/Chapter4.html#config-translation) de putty

  ![putty]({{ root_url }}/images/putty.png "putty")

2. que généralement rien n’empêche une chaîne d’être composée de plusieurs "jeux de caractères" différents. Bien que difficile à croire j'ai vécu ce cauchemard éveillé à plusieurs reprises, dans des fichiers la plupart du temps mais aussi des [URLs](http://blog.lunatech.com/2009/02/03/what-every-web-developer-must-know-about-url-encoding).

3. Détecter l'encodage d'un fichier ou d'une chaîne de caractère sans la connaître peut s'avérer un vrai casse tête voir impossible. En effet une suite de bits peut avoir un sens dans plusieurs "charsets" différents !? Tentez votre chance avec `file -i` ou lancez vous avec un outils plus sophistiqué basé sur des heuristiques comme [enca](https://github.com/nijel/enca).

##Détecter les caractères [NON-imprimables](http://en.wikipedia.org/wiki/Control_character)

En tant que [Digital Native](http://en.wikipedia.org/wiki/Digital_native), je n'ai pas spécialement d’expérience avec les [terminaux](http://en.wikipedia.org/wiki/Computer_terminal) et je n’ai jamais réussi à m’habituer aux caractères non imprimables dit "de contrôle". Il existe aussi d'autres réjouissances dont je ne connais pas l’exhaustivité comme par exemple [ceux-ci](http://en.wikipedia.org/wiki/Non-breaking_space). Heureusement dans le monde moderne #NEWSCHOOL ils se font heureusement extrêmement rares.

Par ailleurs, la problématique peut ressembler à celle des [sauts de ligne](http://en.wikipedia.org/wiki/Newline), mais dans une moindre mesure à mon avis car le problème est souvent plus évident à constater puisque plus remarquable et le sujet est nettement mieux documenté.

En écrivant des flux d'[intégration de données](http://en.wikipedia.org/wiki/Data_integration) entre des systèmes hétérogènes (et parfois exotiques), j'ai souvent rencontré des erreurs inattendues et des comportements anormaux des applications et programmes à leur encontre. Dans l'investigation d'un problème, le plus dur est parfois de simplement identifier ces caractères (de merde):

    > sqlplus -l -s user/password@INSTANCE @ctrlchar.sql | cat
    REVISION DES PRIX 2011 coef 1.107138
    reçu le 22/07/2011 mis en application le
    22/08/2011 (retour congé)


##Les outils que j'aurai aimé connaître depuis le début !

Heureusement il existe [plusieurs](http://stackoverflow.com/questions/6943928/show-special-characters-in-unix-while-using-less-command) moyens de s'en sortir pour les détecter, les afficher et éventuellement les [supprimer](http://en.wikipedia.org/wiki/Sed). Le tout est de les connaître !

### [cat -v](http://www.thelinuxtips.com/tag/cat-v-command/)

    > sqlplus -l -s user/password@INSTANCE @ctrlchar.sql | cat -v
    REVISION DES PRIX 2011 coef 1.107138^M
    reM-gu le 22/07/2011 mis en application le
    22/08/2011 (retour congM-i)^M


### [od](http://en.wikipedia.org/wiki/Od_%28Unix%29), le magnifique

Quelle bénédiction le jour ou j'ai découvert cet outils. Il m'a définitivement sauvé la vie. Il permet non seulement d'imprimer à l'écran l'_in-imprimable_, mais il peut s’avérer aussi utile sur de nombreuses autres problématiques comme l'encodage évoqué précédemment. Selon moi sa grande force est de pouvoir confronter au travers d'un double affichage (octal/interprétation) et caractère par caractère !

    > sqlplus -l -s user/password@INSTANCE @ctrlchar.sql | od -bc
   
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


### [xxd](http://en.wikipedia.org/wiki/Hex_dump) ou "Hex dump"

Outil très similaire à OD. Je le connais moins bien. Il a peut être des possibilités légèrement différentes.


##Ressources

- quelques passages de ce livre en rapport avec le sujet: [bad data](http://www.amazon.com/Bad-Data-Handbook-Cleaning-Back/dp/1449321887)
- si l'aspect historique de l'encodage vous intéresse, il y a [beaucoup](http://www.tbray.org/ongoing/When/200x/2003/04/26/UTF) à [dire](http://www.cl.cam.ac.uk/~mgk25/unicode.html)