---
layout: post
title: "Votre mot de passe est-il trop simple ?"
date: 2013-05-19 14:59
comments: false
---
Qui n'a pas été victime de la ["fatigue des mots de passe"](http://en.wikipedia.org/wiki/Password_fatigue) ? Je sais bien que les [Gestionnaires de mots de passe](http://en.wikipedia.org/wiki/Password_manager) présentent des avantages mais je n'ai jamais pu me faire à leur defauts. Sans aucune prétention, je vais vous décrire ma facon de retenir facilement un mot de passe unique et complexe pour chaque service que j'utilise sur internet avec une seule règle et d'éviter l'[effroyable](http://www.codinghorror.com/blog/2009/05/i-just-logged-in-as-you.html)!

Mon astuce n'est qu'une variante très simple du [chiffrement par décalage](http://en.wikipedia.org/wiki/Caesar_cipher), que j'ai imaginé en regardant un épisode de  ["sur écoute"](http://en.wikipedia.org/wiki/The_Wire). Dans la série, les hommes de main de [Stringer Bell](http://en.wikipedia.org/wiki/Stringer_Bell) s'échangent des numéros de téléphone  par [pagers](http://en.wikipedia.org/wiki/Pager). Ces numéros sont échangés de manière codée, ainsi si le message est intercepté par la police, il ne menera à rien. Dans un souci de commodité, les opérations de cryptage et de décryptage ont été simplifiées: elle se font sans algorithme mathématique mais simplement par une sorte de [symétrie](http://en.wikipedia.org/wiki/Point_reflection) appliquée à la disposition d'un clavier de numérotation téléphonique, et donc très facile à appliquer et à retenir. *#NOBRAINER*

"if it was a code that involves math, algebra or whatever, these littles yos in the project wouldn't be able to follow along. Ain't no math to it. It's just... how the phone looks when you look at it."

![the wire screencap]({{ root_url }}/images/thewire.png "Avon Barksdale's hint on hinding himself")

Pour être capable de mémoriser mes mots de passes, j'ai librement adapté cette idée aux claviers [AZERTY](http://en.wikipedia.org/wiki/AZERTY) que j'utilise la plupart du temps. Evidemment pour que le mot de passe de chaque service soit unique, il faut intégrer un élément propre: le nom du service evidemment ! Ainsi je transpose chaque lettre du nom d'un service, et elle devient la lettre suivante selon le sens des 3 lignes du clavier (en minuscule). `A` devient `Z`, `Z` devient `E`, `P` devient `Q`, `N` devient `A`, etc. Eventuellement il est possible de faire pareil avec les chiffres: `1` devient `2`, `2` devient `3`, `0` devient `1`, etc. Cette façon de faire est facile à mémoriser et à appliquer. #WIN

Aussi pour rendre plus robuste chacun des mots de passe (longeur, majuscules, caracteres spéciaux, etc), je rajoute un "radical" commun à tous les mots de passes. Finalement en n'ayant besoin de retenir que celui-ci, j'obtiens sans effort, des mots de passe uniques (suffisamment longs et complexes) pour chaque service et surtout dont la reversibilité n'est pas triviale.

Par exemple si mon radical était `!m0nR4d_Cal`, cela donnerait:

- `!m0nR4d_Caldyzvlpbrtgmpx` pour me connecter à stackoverflow.com
- `!m0nR4d_Calyxoyyrt` pour me connecter à twitter.com

*J'ai bien pris soin de finir mon radical par des lettres en minuscule, ainsi l'analogie entre le nom du service et une partie du mot de passe est plus difficile à faire que quelquechose comme `MONRADICALyxoyyrt`)*

Cette méthode n'est pas parfaite et n'est qu'un compromis, d'ailleurs dans l'épisode de la série dont je me suis inspiré, ["Prezbo"](http://en.wikipedia.org/wiki/Roland_Pryzbylewski) déjoue assez facilement le stratagème, probablement parcequ'il a pu récupérer plusieurs numéros différents et procéder par [analogie](http://en.wikipedia.org/wiki/Analogy). Evidemment je considère que la probabilité que quelqu'un récupère plusieurs de mes mots de passe (et se donne la peine de retrouver ma facon de faire) est assez faible pour continuer à utiliser cette technique. Aussi par prudence, je l'exclus pour les services dont j'éstime la sécurité critique (compte mail, paypal, dropbox etc).

#### Ressources ####

- [article de 2006](http://www.wired.com/politics/security/commentary/securitymatters/2006/12/72300?currentPage=all) dans lequel Bruce Schneier révèle qu'un des mots de passe les plus courants sur myspace était "Blink182"
- [182 pages](http://www.amazon.fr/Perfect-Password-Selection-Protection-Authentication/dp/1597490415) sur le sujet !?
- [password strength meter](http://www.passwordmeter.com/)
- [how big is your haystack](https://www.grc.com/haystack.htm)
- [WTF](http://stackoverflow.com/questions/98768/should-i-impose-a-maximum-length-on-passwords) !?
- [un film](http://en.wikipedia.org/wiki/Three_Days_of_the_Condor) que j'aime beaucoup :)

