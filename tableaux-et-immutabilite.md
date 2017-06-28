# Tableaux et immutabilité

Dans le [chapitre précédent](objets-et-immutabilite.md), nous avons vu comment travailler avec les objets Javascript d'une façon fonctionnelle et immutable.

Dans ce chapitre, nous ferons de même pour les tableaux.

## Lire les éléments d'un tableau

Dans le chapitre [Objets et immutabilité](objets-et-immutabilite.md), nous avons parlé de fonctions Ramda variées pour lire les propriétés d'un objet, dont `prop`, `pick` et `has`. Ramda a encore plus de méthodes pour lire les éléments d'un tableau.

L'équivalent de `prop` pour les tableaux est `nth`; l'équivalent de `pick` est `slice` et l'équivalent de `has` est `contains`. Jetons leur un œil.

```js
const numbers = [10, 20, 30, 40, 50, 60]
 
nth(3, numbers) // => 40  (0-based indexing)
 
nth(-2, numbers) // => 50 (negative numbers start from the right)
 
slice(2, 5, numbers) // => [30, 40, 50] (see below)
 
contains(20, numbers) // => true
```

`slice` prend deux indices et renvoie le sous-tableau commençant au premier indice (qui démarre à 0) et incluant tous les éléments jusqu'au second indice (exclus).

Accéder au premier (`nth(0)`) et au dernier (`nth(-1)`) élément est courant, donc Ramda fournit des raccourcis pour ces cas particuliers, `head` et `last`. Il donne aussi des fonctions pour accéder à tous les éléments sauf le premier (`tail`), tous les éléments sauf le dernier (`init`), les `N` premiers éléments (`take(N)`) et les `N` derniers éléments (`takeLast(N)`). Regardons-les en action.

```js
const numbers = [10, 20, 30, 40, 50, 60]
 
head(numbers) // => 10
tail(numbers) // => [20, 30, 40, 50, 60]
 
last(numbers) // => 60
init(numbers) // => [10, 20, 30, 40, 50]
 
take(3, numbers) // => [10, 20, 30]
takeLast(3, numbers) // => [40, 50, 60]
```

## Ajouter, mettre à jour et supprimer des éléments d'un tableau

Pour les objets, nous avons appris `assoc`, `dissoc` et `omit` pour ajouter, mettre à jour et supprimer des propriétés (NDT: `dissoc` pour mettre à jour?!?).

Parce que les tableaux sont une structure de données ordonnée, il y a plusieurs méthodes qui font le même travail qu'`assoc`. Les plus générales sont `insert` et `update`, mais Ramda fournit aussi `append` et `prepend` pour le cas courant d'ajout d'éléments au début ou à la fin du tableau. `insert`, `append` et `prepend` ajoutent de nouveaux éléments au tableau; `update` «remplace» un élément avec une nouvelle valeur.

Comme on peut l'attendre d'une bibliothèque fonctionnelle, toutes ces fonctions renvoient un nouveau tableau avec les changements voulus; le tableau original n'est jamais modifié.

```js
const numbers = [10, 20, 30, 40, 50, 60]
 
insert(3, 35, numbers) // => [10, 20, 30, 35, 40, 50, 60]
 
append(70, numbers) // => [10, 20, 30, 40, 50, 60, 70]
 
prepend(0, numbers) // => [0, 10, 20, 30, 40, 50, 60]
 
update(1, 15, numbers) // => [10, 15, 30, 40, 50, 60]
```

Pour combiner deux objets en un seul, nous avons appris `merge`. Ramda offre `concat` pour faire de même avec des tableaux.

```js
const numbers = [10, 20, 30, 40, 50, 60]
 
concat(numbers, [70, 80, 90]) // => [10, 20, 30, 40, 50, 60, 70, 80, 90]
```

Remarquez que c'est le deuxième tableau qui est ajouté à la fin du premier. Ça a l'air normal quand on l'utilise seule, mais, comme avec `merge`, ça pourrait ne pas se comporter comme vous l'attendez dans un pipeline. Je trouve utile de définir une fonction d'aide, `concatAfter`: `const concatAfter = flip(concat)` à utiliser dans les pipelines.

Ramda fournit aussi plusieurs options pour supprimer des éléments. `remove` supprime des éléments par indice, alors que `without` les enlève par valeur. Il y a aussi `drop` et `dropLast` pour le cas habituel de suppression des éléments du début ou de la fin du tableau.

```js
const numbers = [10, 20, 30, 40, 50, 60]
 
remove(2, 3, numbers) // => [10, 20, 60]
 
without([30, 40, 50], numbers) // => [10, 20, 60]
 
drop(3, numbers) // => [40, 50, 60]
 
dropLast(3, numbers) // => [10, 20, 30]
```

Prenez note que `remove` prend un indice et un compte (le nombre d'éléments à supprimer) alors que `slice` prend deux indices. Cette incohérence peut être déroutante si vous n'en êtes pas conscient.

## Transformer des éléments

Comme pour les objets, nous pourrions vouloir mettre à jour un élément de tableau en appliquant une fonction à la valeur originale.

```js
const numbers = [10, 20, 30, 40, 50, 60]
 
update(2, multiply(10, nth(2, numbers)), numbers) // => [10, 20, 300, 40, 50, 60]
```

Pour simplifier ce cas d'usage commun, Ramda nous donne `adjust` qui marche comme `evolve` pour les objets. Contrairement à `evolve`, `adjust` ne marche que pour un seul élément de tableau.

```js
const numbers = [10, 20, 30, 40, 50, 60]
 
adjust(multiply(10), 2, numbers)
```

Remarquez que les deux premiers arguments d'`adjust` sont inversés par rapport à `update`. Ça peut être source de confusion, mais prend tout son sens quand vous considérez l'application partielle. Vous pourriez vouloir fournir une fonction d'ajustement avec `adjust(multiply(10))` et décider seulement plus tard à quel indice et à quel tableau appliquer cet ajustement.
