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

Et si nous voulions trouver le premier nombre impair à la place? Nous pouvons toujours écrire une fonction `isOdd`et l'utiliser, mais nous savons qu'un nombre qui n'est pas pair est impair. Réutilisons notre fonction `isEven`.

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

Remarquez que `complement` fait la même chose sur les fonctions que ce que l'opérateur `!` \(non\) fait sur les valeurs.

### Both/Either {#botheither}

Disons que nous travaillons sur un système de vote. Étant donnée une personne, nous voudrions être capable de déterminer si cette personne a le droit de vote. Selon nos connaissances actuelles, une personne doit avoir au moins 18 ans et être citoyen pour avoir le droit de vote. Quelqu'un est un citoyen s'il est né dans le pays ou s'il est devenu plus tard en se faisant naturaliser.

```js
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18
 
const isCitizen = person => wasBornInCountry(person) || wasNaturalized(person)
 
const isEligibleToVote = person => isOver18(person) && isCitizen(person)
```

Ce que nous avons écrit ci-dessus fonctionne, mais Ramda fournit quelques fonctions pratiques pour nous aider à rendre ce code un peu plus propre.

`both`prend deux autres fonctions et renvoie une nouvelle fonction qui retourne `true `si les deux fonctions renvoient une valeur vraie quand elles sont appliquées à leurs arguments, et `false` sinon.

`either`prend deux autres fonctions et renvoie une nouvelle fonction qui retourne `true `si l'une ou l'autre des fonctions renvoie une valeur vrai quand elles sont appliquées à leurs arguments, et `false` sinon.

En utilisant ces deux fonctions, nous pouvons simplifier `isCitizen` et `isEligibleToVote`:

```js
const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

Remarquez que `both` fait la même chose avec des fonctions que ce que l'opérateur `&&` fait pour des valeurs, et que `either` fait la même chose avec des fonctions que ce que l'opérateur `||` fait avec des valeurs.

Ramda fournit aussi `allPass` et `anyPass`, qui prennent un tableau de fonctions de taille quelconque. Comme leurs noms le suggèrent, `allPass` fonctionne comme `both`, et `anyPass` comme `either`.



