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

