---
layout: post
title: "Extraction Oracle massive en //: la methode de lecture partitionnée"
date: 2013-09-12 23:58
comments: true
categories: 
---
Un avantage de travailler avec Oracle est bien sûr de bénéficier des outils proposés par l'éditeur. La gestion des executions de tâche en [parallèle](http://docs.oracle.com/cd/E11882_01/server.112/e25523/parallel002.htm) est à mon avis indispensable. D'autres  [outils](http://docs.oracle.com/cd/E11882_01/appdev.112/e25788/d_parallel_ex.htm) plus méconnus existent. Toutefois certains besoins sortent des cas d'utilisation prévus. Quand on souhaite traiter massivement des données issus d'Oracle, pour les traiter en //, l'opération d'extraction des données en un seul bloc peut représenter une goulot d'etranglement. La méthode décrite ici propose d'accélerer le processus d'extraction d'une table Oracle pour un traitement des données en //.


## La stratégie

Au lieu d'extraire toutes les données d'une table au travers d'une seule et unique connexion, puis dans un deuxieme temps de partitionner les données. L'idée consiste à extraire simultanement des différentes parties de la table depusi des connexions différentes. Même si ces connexions concurrentes peuvent être effectuée depuis une même machine, un avantage important est qu'il est possible d'effectuer ces connexions depuis des machines distinctes.

Voici une représentation schématique d'un exemple concret d'un contexte dans lequel j'ai pu appliquer ce principe avec succès: le traitement de données s'effectue dans un cluster de 4 machines.

![photo_blocnote]({{ root_url }}/images/oracle_avant_apres.png "schema")

## La construction des requètes

Pour procéder, je me suis largement inspiré des autres outils procedant en // et notemment de cet etonnant [article](http://www.jlcomp.demon.co.uk/big_upd.html) traitant d'une problématique proche et liée au [bug de l'an 2000](http://en.wikipedia.org/wiki/Year_2000_problem) !? Autrement dit, la methode se base sur la structure avantageuse du champ ROWID (Même si le principe reste le même dans toutes les versions, les details de l'explication suivante sont valable pour les versions 10 et 11), qu'on va utiliser pour créer autant de plages de ROWID qu'on a besoin de faire de partitions.

Voici un extrait de la documentation officielle:

`An extended rowid has a four-piece format, OOOOOOFFFBBBBBBRRR:

    OOOOOO: The data object number that identifies the database segment (AAAAao in the example). Schema objects in the same segment, such as a cluster of tables, have the same data object number.

    FFF: The tablespace-relative datafile number of the datafile that contains the row (file AAT in the example).

    BBBBBB: The data block that contains the row (block AAABrX in the example). Block numbers are relative to their datafile, not tablespace. Therefore, two rows with identical block numbers could reside in two different datafiles of the same tablespace.

    RRR: The row in the block.`

En se servant du catalogue systeme de l'instance, il est possible pour la table donnée, d'identifier le ou les "Datafiles", puis les groupes de "blocks" qu'elle occupe. A partir de ces informations, on va pouvoir estimer comment sont réparties les données et surtout essayer de découper des groupes de blocks pour répartir le mieux possible les données Autrement dit equitablement entre les partitions.  

On peut lister l'identifiant de l'objet de cette manière:

`SELECT DATA_OBJECT_ID FROM DBA_OBJECTS WHERE OWNER = 'USER1' AND OBJECT_NAME = 'TABLE1'`

On peut lister les Datafiles concernés par les données de la table de cette manière:

`SELECT DISTINCT FILE_ID FROM DBA_EXTENTS WHERE SEGMENT_NAME='TABLE1' AND SEGMENT_TYPE='TABLE'`

On peut lister les différents "extents" concernés par les données de la table de cette manière:

`SELECT BLOCK_ID, BLOCKS FROM DBA_EXTENTS WHERE SEGMENT_NAME='TABLE1' AND SEGMENT_TYPE='TABLE'`

Pour la construction de la limite inferieure d'une plage de ROWID,  on se content d'un numéro de block fourni par le catalogue (BLOCK_ID) et on renseigne le numero de ligne par la valeur la plus faible possible : "AAA".

Pour la construction de la limite supérieur, il faut calculer le numero du dernier block à partir de la taille fournie par le catalogue (BLOCKS) et on renseigne le numéro de ligne par la valeur de la derniere ligne. En théorie cette dernière information n'est pas si facile à deviner, car le nombre de lignes dans un block dépend de plusieurs facteurs:
- la taille du block
- la taille des lignes
- certains parametres comme PCTFREE qui définissent la taille reservée (pour une possible extension)

On peut déterminer la taille des blocks de cette manière:

`SELECT VALUE FROM V$PARAMETER WHERE NAME LIKE 'DB_BLOCK_SIZE'`
Pour moi le plus simple consiste a renseigner la valeur la plus élévée possible. Malheuresement cette valeur théorique n'est pas bien documentée. Sans pouvoir citer de source, j'ai utilisé "H//".

Notons bien, que cette méthode profite de plusieurs opportunités:
- bien sûr de la structure du champ ROWID et de son format d'[encodage](http://en.wikipedia.org/wiki/Base64) qui permet non seulement de construire les bornes des plages mais aussi l'utilisation des opérateurs "supérieur" et "inferieur" pour chercher à l'interieur
- le fait que l'execution de la requete ne soit pas gênée par des identifiants "ROWID" manquants

Au final on écrit autant de requete qu'on souhaite de partitions de données sous la forme suivante:

`SELECT * FROM TABLE1 WHERE TABLE1.ROWID BETWEEN 'AAARvrAAEAAAAVpAAA' AND 'AAARvrAAEAAAAVuH//' AND (COL1 > 10)`

## Quelques variantes

On peut imaginer partager les données de la table en partitions plus simplement, en parcourant pour chaque requete toute la table mais en se contentant de garder qu'une partie des données. Evidemment, ce mechanisme se ressentirait directement au niveau des performances.

`SELECT * FROM TABLE1 WHERE MOD(DBMS_ROWID.ROWID_ROW_NUMBER(TABLE1.ROWID), 4) = 0 AND COL1 > 10`

Si on dispose de tables partitionnées, alors il est tout a fait imaginable tirer profit des ces partitions en ecrivant des requetes du type:

`SELECT * FROM TABLE1 PARTITION(PART1) WHERE COL1 > 10`

## Les limites

1. Cette methode n'a d'interet que si l'on n'a aucune exigence sur la répartition des données entre les différentes partitions. En effet les données ne sont réparties dans les blocks selon aucun critère particulier. S'il est necessaire de regrouper les données d'une quelqonque manière (date, clef, hash, etc) cette methode devient immédiatement inutile ou alors d'un interet très limité puisqu'il serait necessaire de rassembler les données.

2. Cette methode est reservée à des extractions basiques et n'est bien evidemment pas possible sur des requetes effectuant des jointures entre plusieurs tables ou des opérations de groupement. Par ailleurs des filtres sur les données elle-mêmes sont possibles.

