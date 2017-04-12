Une (petite) présentation du schmilblick
========================================
Avant d'attaquer la bête, nous avons, tout d'abord, besoin de la définir. Tout
d'abord, qu'est-ce que la repr...

> C'est vrai ça, c'est quoi la rapré... représentation invert... cette chose ?

La représentation intervallaire !

Cette technique peut permettre de situer un  élément dans une **hiérarchie**.
Pour donner une image de cette notion de hiérarchie, prenons l'exemple d'un
système de forums.

Un tel système peut proposer $[1,\infty[$ catégories, elles-même composées de
$[0,\infty[$ forums, eux-mêmes pouvant  être composés de $[0,\infty[$
sous-forums. Voilà ce qu'est la notion de hiérarchie : si je suis dans un
sous-forum, qui lui est donc dans un forum, lui-même dans une catégorie, ça
veut dire que je suis à la troisième hiérarchie. Il peut, bien entendu, y avoir
plusieurs éléments ayant le même niveau, comme, toujours à l'image d'un système
de forums, plusieurs forums affiliés à une catégorie.

Un point essentiel – si ce n'est le plus important ! – à retenir dans la
représentation intervallaire (je ne vois pas ce qu'il y a de compliqué à
retenir là-dedans, ce n'est quand même pas de l'acide acétyli... acide
acytéli... bref, un certain médicament...), c'est la notion de **borne
gauche**, **borne droite**. Voici un schéma[^schema] pour illustrer cette
notion :

[^schema]: Réalisé par [Shadow_f](http://fr.openclassrooms.com/membres/shadowf-91614)

![Concept de la RI](/media/galleries/287/67f2cd29-0f28-4759-bf94-009d2e8a62db.jpg.960x960_q85.jpg)

Comme vous pouvez le voir, chaque borne de chaque élément se voit attribuer un
nombre. Ce nombre permet de déterminer la position de l'élément dans
l'ensemble. Notez également que **la borne gauche est *toujours* inférieure à
la borne droite**. Notez bien ce point, il est très important  !

À propos de points importants, j'en profite pour présenter un autre aspect de
la représentation intervallaire : il existe plusieurs type de nœud, deux pour
être plus précis. Soit il s'agit d'un nœud dit "nœud complexe" (ou sous-arbre),
qui comprend donc des nœuds fils, soit il peut s'agir d'une feuille, qui, à
l'image d'un arbre, ne sera qu'un simple élément, et ne comporte pas de
descendance quelconque.

Pour identifier mathématiquement les deux, il faut juste **calculer la
différence entre la borne droite et la borne gauche**. Si le résultat est égal
à 1, il s'agit d'une feuille, sinon d'un nœud. On peut même pousser le
raisonnement un peu plus loin : cette différence nous donne le double nombre
d'enfants de ce nœud auquel on lui rajoute un. Vérifions-le pour une feuille
(disons le nœud  ayant les bornes 3 et 4) :

$$ \begin{array} {lcl}
    \text{borne droite} - \text{borne gauche} & = & 4 - 3 \\\
                                              & = & 1     \\\
                                              & = & 0 + 1 \\\
                                              & = & \boxed{0} \times 2  + 1
\end{array}$$

Le nœud aux bornes 3 et 4 est donc une feuille, et ne comporte donc aucun
élément fils. Vérifions le également pour un des nœuds du premier niveau du
schéma, celui ayant les bornes 2 et 7 :

$$\begin{array} {lcl}
    \text{borne droite} - \text{borne gauche} & = & 7 - 2 \\\
                                              & = & 5     \\\
                                              & = & 4 + 1 \\\
                                              & = & \boxed{2} \times 2  + 1
\end{array}$$

La différence des bornes de ce nœud est donc supérieure à 1 : il s'agit donc
d'un nœud "complexe". On s'aperçoit également qu'il comporte 2 enfants, ce qui
est vérifiable sur le schéma.

Passons maintenant à la préparation pour pouvoir pratiquer dans les deux parties
suivantes de ce tutoriel. Je montrerai également pourquoi il est difficile (et
coûteux) d'utiliser les "auto-jointures" -- soit l'utilisation avec abus de la
clause `WHERE`, des sous-requêtes et des jointures -- pour accéder aux
différents nœuds... Et qu'à moins d'avoir des idées tordues, cela devient vite
impraticable.

**NB :** Dans cette sous-partie, je considère que vous avez un minimum de
connaissances en SQL. Sinon, vous avez un bon tutoriel sur le SQL[^taguan-sql]
par Taguan que je ne peux que vous recommander.

[^taguan-sql]: [Administrez vos bases de données avec MySQL](http://fr.openclassrooms.com/informatique/cours/administrez-vos-bases-de-donnees-avec-mysql)

La représentation intervallaire *versus* l'héritage récursif
============================================================
L'héritage récursif ? Quelle prise de tête...
---------------------------------------------
Voici la table type que nous allons utiliser pour illustrer (la faiblesse de)
l'héritage récursif :

```sql
CREATE TABLE tuto_herit (
        id mediumint(8) NOT NULL AUTO_INCREMENT,
        parent_id mediumint(8) DEFAULT NULL,
        /* ... */

        PRIMARY KEY  (id),
        KEY parent_id (parent_id)
    );

INSERT INTO tuto_herit (id, parent_id)
    VALUES
        (1, NULL),
        (2, 1),
        (3, 2),
        (4, 2),
        (5, 1),
        (6, 1),
        (7, 1),
        (8, 7),
        (9, 7)/*,[...]*/;
```

À partir du nœud portant l'id n°8, comment faire, à l'aide des jointures, pour
accéder à l'ensemble de ses parents ? Vous ne devez, bien entendu, n'avoir
qu'un seul parent par ligne (pas tous, sinon ce serait trop facile, surtout si
on connaît la profondeur du nœud !).

...

...

Vous n'y arrivez pas ? Pour tout vous avouer, moi non plus ! Ici, on peut
facilement avoir tous les parents sur la **même ligne** à l'aide de *quelques*
jointures (en utilisant l'héritage de la colonne `parent_id`) ; or, ce n'était
pas ce qui était demandé : on voulait un nœud par ligne. Certes, les jointures,
c'est pratique, mais à la longue... c'est vite pesant et peu pratique.

**NB :** pour les curieux qui veulent absolument résoudre ce problème, une piste
serait d'utiliser une procédure récursive pour trouver ce qu'on cherche, mais ça
reste consommateur de ressources si l'arbre est trop profond (dans notre cas,
cela peut être considéré comme trop profond). Mais ce sujet ne nous intéresse
pas ici.

La représentation intervallaire ? Yeah !
----------------------------------------
Reprenons la première table, et adaptons-la dans un arbre :

```sql
CREATE TABLE tuto_ri (
        id mediumint(8) NOT NULL AUTO_INCREMENT,
        node_depth mediumint(8) NOT NULL DEFAULT 0,
        node_left mediumint(8) NOT NULL,
        node_right mediumint(8) NOT NULL,
        /* ... */

        PRIMARY KEY  (id),
        KEY node (node_left, node_right)
    );

INSERT INTO tuto_ri (id, node_depth, node_left, node_right)
    VALUES
        (1, 0, 1, 18),
        (2, 1, 2, 7),
        (3, 2, 3, 4),
        (4, 2, 5, 6),
        (5, 1, 8, 9),
        (6, 1, 10, 11),
        (7, 1, 12, 17),
        (8, 2, 13, 14),
        (9, 2, 15, 16)/*,[...]*/;
```

Allez, même exercice que tout à l'heure : recherchez l'id et la hiérarchie des
nœuds parents de la feuille ayant l'id "8", ayant pour bornes 13 et 14, en les
triant du parent le plus proche au parent le plus lointain !

...

...

...

...

Ah, c'est vrai, j'avais oublié le petit indice : utilisez les bornes des nœuds.
Si vous n'aviez pas deviné, ce n'est pas grave, voici ma solution, et pour
tenter de me faire pardonner, je l'ai commentée pour que vous la compreniez
mieux.

```sql
SELECT id, node_depth
    FROM tuto_ri
    WHERE node_left < 13
        AND node_right > 14
    ORDER BY node_depth DESC;
```

On cherche à récupérer l'ID, le nom, et la hiérarchisation des nœuds parents,
ce qu'on fait en sélectionnant les champs `id`, `name`, et `node_depth`.
Ensuite, dans la clause `WHERE`, on applique un des points principaux de la
représentation intervallaire : la borne gauche d'un nœud est *toujours plus
grande* que celle des nœuds parents. On sélectionne donc tous les nœuds ayant,
dans notre cas, une borne gauche plus petite que 7. On applique la même chose à
la borne droite, sauf que cette fois-ci, la borne droite de la feuille est
*toujours plus petite* que celle des nœuds parents.

On a donc les nœuds que l'on désire... Mais ce n'est pas fini, il y avait un
petit piège : on souhaite avoir les nœuds... Du plus proche au plus lointain
par rapport à la feuille. Il suffisait juste d'ajouter une petite clause
`ORDER BY`, en indiquant le nom de la colonne qui sert à trier (`node_depth`),
et par ordre **décroissant**. Je vous avais bien dit que la notion de
hiérarchisation pouvait s'avérer utile par moments... En voilà la preuve même !

`id` | `node_depth`
---- | ------------
7    | 1
1    | 0

**NB :** Notez que si je vous avais demandé, dans la liste des nœuds ressortie,
de ressortir également le nœud dans lequel on est, à la place des opérateurs
`<` et `>`, il faudrait alors utiliser `<=` et `>=`.

```sql
SELECT id, node_depth
    FROM tuto_ri
    WHERE node_left <= 13
        AND node_right >= 14
    ORDER BY node_depth DESC;
```

`id` | `node_depth`
---- | ------------
8    | 2
7    | 1
1    | 0

Dans les deux parties qui suivent, nous allons voir les différentes techniques
et opérations ~~chirurgicales~~ liées à la représentation intervallaire, telles
que...

- le comptage du nombre d'éléments dans un nœud (que ce soit le nombre
  d'enfants, de parents, d'éléments...) ;
- la modification du statut d'un élément, par l'ajout ou la suppression de
  nœuds ;
- la possibilité de mettre à jour les indices aux bornes en fonction du nombre
  d'éléments

Ces deux parties agiront donc par plusieurs séries de "mini-TP", qui demandent
par moments de la réflexion, et par moments des astuces... Bien évidemment, je
vous guiderai pas à pas pour la réalisation de ces TP.
