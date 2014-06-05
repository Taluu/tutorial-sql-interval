Une notion bien étrange que voilà...

Mettons nous en situation, sur un système de forums, ayant plusieurs **niveaux**. Chaque forum peut posseder un 
**parent** et des **enfants**. Pour naviguer dans ce type d'arborescence, vous procéderez probablement par des héritages
dits *récursifs*, caractérisés par une colonne `parent_id`, méthode également connue sous le terme peu barbare 
*représentation classique* ou *représentation récursive*, en renseignant de manière récursive la clause `WHERE`, en
utilisant des sous-requêtes ou bien des jointures... Ou des concepts plus avancés que je n'aborderai pas ici.

Mais, ceci peut **très** vite devenir lourd... et peu pratique, surtout à partir d'un certain niveau, comme nous le
verrons par la suite.

Dans ce tuto, nous allons donc voir comment éviter cette lourdeur en employant une "ruse" mise au point par les
développeurs : **la représentation par arbre d'intervalle**.

Accrochez-vous, on y va !
