# Combinaison de fonctions

Dans le premier chapitre, j'ai présenté Ramda et quelques concepts basiques de programmation fonctionnelle, comme les fonctions, les fonctions pures, et l'immutabilité. J'ai alors suggérer qu'une bonne manière de commencer était d'utiliser les fonction _itération-collection_ comme `forEach`, `map`, `select`, et consorts.

## Combinaisons simples

Une fois que vous vous êtes fait à l'idée de passer des fonctions à d'autres fonctions, vous pourriez tomber sur des situtations où vous voudriez combiner plusieurs fonctions.

Ramda fournit plusieurs fonctions pour les combinaisons simples. Voyons-en quelques unes.

### Complement {#complement}

Dans le précédent chapitre, nous avons utilisé `find` pour trouver le premier nombre pair d'une liste:

```js
const isEven = x => x % 2 === 0
 
find(isEven, [1, 2, 3, 4]) // --> 2
```

Et si nous voulions trouver le premier nombre impair à la place? Nous pouvons toujours écrire une fonction `isOdd `et l'utiliser, mais nous savons qu'un nombre qui n'est pas pair est impair. Réutilisons notre fonction `isEven`.

Ramda fournit une fonction d'ordre supérieur \(NDT: _higher-order function_\), `complement`, qui prend une autre fonction et retourne une nouvelle fonction `true` quand la fonction original renvoie une valeur fausse \(NDT: _falsy value_\), et `false` quand la fonction originale renvoie une valeur vraie \(NDT: _truthy value_\).

```js
const isEven = x => x % 2 === 0
 
find(complement(isEven), [1, 2, 3, 4]) // --> 1
```

C'est encore mieux de donner à la fonction `complement`ée son propre nom, pour qu'on puisse la réutiliser:

```js
const isEven = x => x % 2 === 0
const isOdd = complement(isEven)
 
find(isOdd, [1, 2, 3, 4]) // --> 1
```

Remarquez que `complement` implémente la même idée pour les fonctions que l'opérateur `!` \(non\) implémente pour les valeurs.

