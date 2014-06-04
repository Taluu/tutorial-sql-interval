Une notion bien étrange que voilà...

Pour mieux expliquer, prenons (par exemple) des forums, ayant plusieurs "niveaux". Pour accéder aux "enfants" et aux "parents" d'un forum, vous pensez sûrement (et, je pense, vous les utiliserez) les héritages... C'est à dire la présence d'une colonne `parent_id`, méthode également connue sous le nom de représentation classique ou représentation par héritage, en renseignant récursivement la clause `WHERE` à l'aide de sous-requêtes, ou par des jointures. 

Mais, ceci peut **très** vite devenir lourd... et peu pratique, surtout à partir d'un certain niveau (et qui, au grand dam de tous, est trop vite atteint), comme nous le verrons par la suite.

Dans ce tuto, nous allons donc voir comment éviter cette lourdeur en employant une "ruse" mise au point par les développeurs : **la représentation par arbre d'intervalle**.

Accrochez-vous, on y va !
