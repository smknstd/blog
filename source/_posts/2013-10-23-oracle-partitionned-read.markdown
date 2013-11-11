---
layout: post
title: "Extraction Oracle massive en //: la méthode de lecture partitionnée"
date: 2013-09-12 23:58
comments: true
categories:
---
En tant que défenseur des logiciels libres, il est parfois dur de travailler avec des logiciels propriétaires. Oracle est [surement](http://www.reuters.com/article/2013/02/27/us-ellison-airline-idUSBRE91Q11R20130227) le pire éditeur qu'on puisse imaginer, mais il faut avouer que son SGBD est un outil malgré tout [sophistiqué](http://www.xaprb.com/blog/2009/03/13/50-things-to-know-before-migrating-oracle-to-mysql/).

Lorsqu'on souhaite exécuter des taches en parallèle, plusieurs outils [plus](http://docs.oracle.com/cd/E11882_01/server.112/e25523/parallel002.htm) ou [moins](http://docs.oracle.com/cd/E11882_01/appdev.112/e25788/d_parallel_ex.htm) indispensables sont mis à disposition. Toutefois certains besoins peuvent sortir des cas d'utilisation prévus. J'ai souhaité traiter massivement les données d'une table, pour les traiter en lots indépendants sur plusieurs machines différentes. L'opération d'extraction des données au travers d'une seule connexion peut représenter une goulot d’étranglement. J'ai alors imaginé la méthode décrite ici pour essayer d’accélérer le processus d'extraction vers un cluster de machines.


## La stratégie

Au lieu d'extraire toutes les données d'une table au travers d'une seule et unique connexion, puis dans un deuxième temps de partitionner les données. L'idée consiste à extraire simultanément des différentes parties de la table depuis des connexions différentes (sessions).

Voici une représentation schématique du contexte dans lequel j'ai pu appliquer ce principe avec succès: le traitement de données s'effectue dans un cluster de 4 machines.

![photo_blocnote]({{ root_url }}/images/oracle_avant_apres.png "schema")

## La construction des requêtes

Pour procéder, je me suis largement inspiré des autres outils procédant en // et notamment de cet étonnant [article](http://www.jlcomp.demon.co.uk/big_upd.html) traitant d'une problématique proche et liée au [bug de l'an 2000](http://en.wikipedia.org/wiki/Year_2000_problem) !? La méthode se base sur la structure avantageuse du champ ROWID (même si le principe reste le même dans toutes les versions, les détails de l'explication suivante sont valable pour les versions 10 et 11), qui permet non pas d’interroger la table suivant sa définition logique habituelle mais suivant sa représentation dans le moteur de stockage, autrement dit à un plus bas niveau. Il faut utiliser le champ ROWID pour créer des plages de données exclusives et équilibrées au maximum.

Voici un extrait de la documentation officielle:

> An extended rowid has a four-piece format, OOOOOOFFFBBBBBBRRR:
>
>    OOOOOO: The data object number that identifies the database segment (AAAAao in the example). Schema objects in the same segment, such as a cluster of tables, have the same data object number.
>
>    FFF: The tablespace-relative datafile number of the datafile that contains the row (file AAT in the example).
>
>    BBBBBB: The data block that contains the row (block AAABrX in the example). Block numbers are relative to their datafile, not tablespace. Therefore, two rows with identical block numbers could reside in two different datafiles of the same tablespace.
>    RRR: The row in the block.
>

En se servant du catalogue système de l'instance, on essaie de cartographier au mieux la disposition des données dans le "file system". A partir de ces informations, il faut essayer de découper des groupes de blocks pour répartir le plus équitablement possible entre les partitions. Aussi il faut prendre soin en choisissant les bornes des plages de ROWID à n'exclure aucune données. Dans la pratique les informations nécessaires sont données principalement par les "Datafiles", puis les groupes de "blocks" occupés.

On peut lister l'identifiant de l'objet de cette manière:

`SELECT DATA_OBJECT_ID FROM DBA_OBJECTS WHERE OWNER = 'USER1' AND OBJECT_NAME = 'TABLE1'`

On peut lister les Datafiles concernés par les données de la table de cette manière:

`SELECT DISTINCT FILE_ID FROM DBA_EXTENTS WHERE SEGMENT_NAME='TABLE1' AND SEGMENT_TYPE='TABLE'`

On peut lister les différents "extents" concernés par les données de la table de cette manière:

`SELECT BLOCK_ID, BLOCKS FROM DBA_EXTENTS WHERE SEGMENT_NAME='TABLE1' AND SEGMENT_TYPE='TABLE'`

Pour la construction de la limite inférieure d'une plage de ROWID,  on se content d'un numéro de block fourni par le catalogue (BLOCK_ID) et on renseigne le numéro de ligne par la valeur la plus faible possible : "AAA".

Pour la construction de la limite supérieur, il faut calculer le numéro du dernier block à partir de la taille fournie par le catalogue (BLOCKS) et on renseigne le numéro de ligne par la valeur de la dernière ligne. En théorie cette dernière information n'est pas si facile à deviner, car le nombre de lignes dans un block dépend de plusieurs facteurs:

- la taille du block
- la taille des lignes
- certains paramètres comme PCTFREE qui définissent la taille réservée (pour une possible extension)

On peut déterminer la taille des blocks de cette manière:

`SELECT VALUE FROM V$PARAMETER WHERE NAME LIKE 'DB_BLOCK_SIZE'`
Pour moi le plus simple consiste a renseigner la valeur la plus élevée possible. Malheureusement cette valeur théorique n'est pas bien documentée. Sans pouvoir citer de source, j'ai utilisé "H//".

Notons bien, que cette méthode profite de plusieurs opportunités:

- bien sûr de la structure du champ ROWID et de son format d'[encodage](http://en.wikipedia.org/wiki/Base64) qui permet non seulement de construire les bornes des plages mais aussi l'utilisation des opérateurs "supérieur" et "inférieur" pour chercher à l’intérieur
- le fait que l’exécution de la requête ne soit pas gênée par des identifiants "ROWID" manquants

Au final on écrit autant de requête qu'on souhaite de partitions de données sous la forme suivante:

`SELECT * FROM TABLE1 WHERE TABLE1.ROWID BETWEEN 'AAARvrAAEAAAAVpAAA' AND 'AAARvrAAEAAAAVuH//' AND (COL1 > 10)`

## Quelques variantes

On peut imaginer partager les données de la table en partitions plus simplement, en parcourant pour chaque requête toute la table mais en se contentant de garder qu'une partie des données. Évidemment, ce mécanisme se ressentirait directement au niveau des performances.

`SELECT * FROM TABLE1 WHERE MOD(DBMS_ROWID.ROWID_ROW_NUMBER(TABLE1.ROWID), 4) = 0 AND COL1 > 10`

Si on dispose de tables partitionnées, alors il est tout a fait imaginable tirer profit des ces partitions en écrivant des requêtes du type:

`SELECT * FROM TABLE1 PARTITION(PART1) WHERE COL1 > 10`

## Les limites

1. Cette méthode n'a d’intérêt que si l'on n'a aucune exigence sur la répartition des données entre les différentes partitions. En effet les données ne sont réparties dans les blocks selon aucun critère particulier. S'il est nécessaire de regrouper les données d'une quelconque manière (date, clef, hash, etc) cette méthode devient immédiatement inutile ou alors d'un intérêt très limité puisqu'il serait nécessaire de rassembler les données.

2. Cette méthode est réservée à des extractions basiques et n'est bien évidemment pas possible sur des requêtes effectuant des jointures entre plusieurs tables ou des opérations de groupement. Par ailleurs des filtres sur les données elle-mêmes sont possibles.
