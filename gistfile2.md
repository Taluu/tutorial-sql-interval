Dans cette première (courte) série de TP, et afin de se mettre en jambe pour la seconde série, nous allons voir comment compter le nombre d'éléments d'un nœud, le nombre de feuilles d'un nœud, le nombre de nœuds "complexes" dans un même nœud, ... bref, on va compter.

Compter le nombre d'éléments d'un nœud
======================================
Pour compter le nombre d'éléments d'un nœud, ça va être relativement simple, et vite expédié. Vous vous souvenez, à la fin de la première partie, on a vu comment récupérer tous les parents (et leur caractéristiques) d'un nœud, en incluant ou non l'élément... Ici, ça va plus ou moins être le même topo.

Ce qui change ici, c'est que vous aurez besoin, comme je l'ai dit, non pas de sélectionner les parents de l'élément, mais ses enfants ! Donc, au lieu de rechercher tous les éléments qui ont une borne gauche inférieure à celle de l'élément recherché, et une borne droite supérieure à celle de l'élément, on va faire l'inverse.

On va essayer de compter le nombre d'enfants pour le nœud ayant l'id n°1 (le nœud racine) :

```sql
SELECT COUNT(*)
    FROM tuto_ri
    WHERE node_left > 1 
        AND node_right < 18;
```

Vous avez vu, ce n'était pas si sorcier, il suffisait donc juste d'inverser les signes pour les deux bornes pour obtenir les enfants et non pas les parents du nœud, et d'utiliser la fonction d'agrégat `COUNT(*)`. On trouve alors 8 éléments ; dans le sens inverse (en comptant donc les *parents* du nœud portant l'id n°2), on ne devrait trouver aucun parent.

Compter le nombre de feuilles d'un nœud
=======================================
> Mais... Mais... N'est-ce pas la même chose que ce qu'on a fait à l'instant même ?

Pas tout à fait ; ici, on ne cherchera pas à compter toute la filiation d'un nœud, mais en prenant également en compte le type de nœud (le nombre de feuilles ou de nœuds donc) d'un nœud. 

Donc, on va à peu près procéder de la même manière qu'auparavant, mais, tâchez juste de vous rappeler d'un des points à retenir de la représentation intervallaire. Vous y êtes ? Non ? Vous savez ce qu'il vous reste à faire...

```sql
SELECT COUNT(*)
    FROM tuto_ri
    WHERE node_left > 1 
        AND node_right < 18 
        AND (node_right - node_left) = 1;
```

| `COUNT(*)` |
| ---------- |
| 6          |


Ce TP était également plutôt facile ; il suffisait de se souvenir que pour toutes les feuilles d'un arbre, la différence entre les deux bornes est toujours égale à 1. De même, pour compter le nombre de nœuds fils, vous pouvez, je pense, aisément le deviner : il faut chercher non pas les fils ayant une différence entre leurs bornes égale à 1, mais **supérieure** à 1.

```sql
SELECT COUNT(*)
    FROM tuto_ri
    WHERE node_left > 3 
        AND node_right < 24 
        AND (node_right - node_left) > 1;
```

| `COUNT(*)` |
| ---------- |
| 2          |


**NB :** Vous vous en doutez, un nœud ne peut avoir de feuilles comme parentes, ou c'est qu'il y alors un problème dans vos branches... Tout comme le calcul du nombre d'éléments parents donnera le même résultat que celui du calcul du nombre des nœuds parents. De plus, si vous additionnez le résultat du décompte des feuilles enfantes et des nœuds enfants du nœud recherché, on trouve alors le même résultat que celui du premier exercice.

Nous avons fini de tout compter, nous allons passer maintenant à quelque chose de bien plus intéressant : la modification d'un arbre. Que ce soit pour ajouter ou supprimer un nœud, ou même le déplacer, effectuer ces tâches n'est pas une mince affaire : on verra un poil d'algorithmie avant d'attaquer...
