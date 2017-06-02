# Combinaison de fonctions

Dans le premier chapitre, j'ai présenté Ramda et quelques concepts basiques de programmation fonctionnelle, comme les fonctions, les fonctions pures, et l'immutabilité. J'ai alors suggéré qu'une bonne manière de commencer était d'utiliser les fonction _itération-collection_ comme `forEach`, `map`, `select`, et consorts.

## Combinaisons simples

Une fois que vous vous êtes fait à l'idée de passer des fonctions à d'autres fonctions, vous pourriez tomber sur des situations où vous voudriez combiner plusieurs fonctions.

Ramda fournit plusieurs fonctions pour les combinaisons simples. Voyons-en quelques unes.

### Complement

Dans le précédent chapitre, nous avons utilisé `find` pour trouver le premier nombre pair d'une liste:

```js
const isEven = x => x % 2 === 0

find(isEven, [1, 2, 3, 4]) // --> 2
```

Et si nous voulions trouver le premier nombre impair à la place? Nous pouvons toujours écrire une fonction `isOdd` et l'utiliser, mais nous savons qu'un nombre qui n'est pas pair est impair. Réutilisons notre fonction `isEven`.

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

### Both/Either

Disons que nous travaillons sur un système de vote. Étant donnée une personne, nous voudrions être capable de déterminer si cette personne a le droit de vote. Selon nos connaissances actuelles, une personne doit avoir au moins 18 ans et être citoyen pour avoir le droit de vote. Quelqu'un est un citoyen s'il est né dans le pays ou s'il l'est devenu plus tard en se faisant naturaliser.

```js
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18

const isCitizen = person => wasBornInCountry(person) || wasNaturalized(person)

const isEligibleToVote = person => isOver18(person) && isCitizen(person)
```

Ce que nous avons écrit ci-dessus fonctionne, mais Ramda fournit quelques fonctions pratiques pour nous aider à rendre ce code un peu plus propre.

`both` prend deux autres fonctions et renvoie une nouvelle fonction qui retourne `true` si les deux fonctions renvoient une valeur vraie quand elles sont appliquées à leurs arguments, et `false` sinon.

`either` prend deux autres fonctions et renvoie une nouvelle fonction qui retourne `true` si l'une ou l'autre des fonctions renvoie une valeur vrai quand elles sont appliquées à leurs arguments, et `false` sinon.

En utilisant ces deux fonctions, nous pouvons simplifier `isCitizen` et `isEligibleToVote`:

```js
const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

Remarquez que `both` fait la même chose avec des fonctions que ce que l'opérateur `&&` fait pour des valeurs, et que `either` fait la même chose avec des fonctions que ce que l'opérateur `||` fait avec des valeurs.

Ramda fournit aussi `allPass` et `anyPass`, qui prennent un tableau de fonctions de taille quelconque. Comme leurs noms le suggèrent, `allPass` fonctionne comme `both`, et `anyPass` comme `either`.

## Pipelines

Quelquefois nous voulons appliquer plusieurs fonctions à des données, comme à travers un _pipeline_. Par exemple, nous pourrions vouloir prendre deux nombres, multiplier l'un par l'autre, ajouter un et mettre le résultat au carré. Nous pourrions l'écrire comme ceci:

```js
const multiply = (a, b) => a * b
const addOne = x => x + 1
const square = x => x * x

const operate = (x, y) => {
  const product = multiply(x, y)
  const incremented = addOne(product)
  const squared = square(incremented)

  return squared
}

operate(3, 4) // => ((3 * 4) + 1)^2 => (12 + 1)^2 => 13^2 => 169
```

Notez que chaque opération est appliquée au résultat de la précédente.

### pipe

Ramda fournit la fonction `pipe`, qui prend une liste d'une ou plusieurs fonctions, et renvoie une nouvelle fonction.

Cette nouvelle fonction prendre le même nombre d'arguments que la première fonction qu'on a donné. Elle fait alors passer ces arguments à travers un "tuyau" \(NDT: _pipes_\) composé de toutes les fonctions de la liste. Elle applique la première fonction aux arguments, passe son résultat à la deuxième fonction, etc. Le résultat de la dernière fonction est le résultat de l'appel à `pipe`.

Remarquez que toutes les fonctions après la première ne doivent prendre qu'un seul argument.

Sachant cela, nous pouvons utiliser `pipe` pour simplifier notre fonction `operate`:

```js
const operate = pipe(
  multiply,
  addOne,
  square
)
```

Quand nous appelons `operate(3,4)`, `pipe` passe `3` et `4` à la fonction `multiply`, dont le résultat est `12`. Elle passe ce `12` à `addOne`, qui renvoie `13`. Elle passe alors ce `13` à `square`, qui retourne `169`, et cela devient le résultat final d'`operate`.

### compose

Une autre façon pour écrire notre fonction `operate` originale aurait été d'intégrer \(NDT: _to inline_\) toutes les variables temporaires:

```js
const operate = (x, y) => square(addOne(multiply(x, y)))
```

C'est bien plus compact, mais un peu plus dur à lire. Dans cette forme pourtant, on a envie de le réécrire en utilisant la fonction `compose` de Ramda.

`compose` fonctionne exactement de la même manière que `pipe`, excepté qu'elle applique les fonctions de droite à gauche, et non de gauche à droite. Écrivons `operate` avec `compose`:

```js
const operate = compose(
  square,
  addOne,
  multiply
)
```

C'est la même chose que le `pipe` précédent, mais avec les fonctions dans l'ordre inverse. En fait, la fonction `compose` de Ramda est écrite avec `pipe`.

Je pense toujours `compose` de cette manière: `compose(f, g)(value)` est équivalent à `f(g(value))`.

Comme avec `pipe`, remarquez que toutes les fonctions sauf la dernière ne doivent prendre qu'un seul paramètre.

### compose ou pipe ?

Je crois que `pipe` est plus simple à comprendre quand on a l'habitude de la programmation impérative, parce qu'on lit les fonctions de gauche à droite. Par contre, `compose` est un peu plus facile à traduire en fonction imbriquée \(NDT: ?\) comme je l'ai montré plus haut.

Je n'ai pas encore trouvé de bonne règle pour décider quand il faut préférer `compose`et quand il faut préférer `pipe`. Comme ils sont équivalents en Ramda, choisir l'une ou l'autre n'a probablement pas grande importance. Choisissez simplement celle qui ce lit le mieux dans le contexte.

## Conclusion

En combinant plusieurs fonctions d'une certaine manière, on peut commencer à écrire des fonctions plus puissantes \(NDT: _powerful_\).

## Chapitre suivant

Vous avez peut-être remarqué que nous avons la plupart du temps ignoré les arguments des fonctions, en les combinant. Nous ne fournissons les arguments qu'au moment où nous appelons la fonction combinée \(NDT: _combined function_, peut-être «fonction combinaison» serait-il meilleur, mais ça ne sonne pas très bien\).

C'est habituel en programmation fonctionnelle, et nous allons en parler beaucoup plus dans le prochain chapitre, [Application partielle](/application-partielle.md). Nous y parlons aussi de la manière de combiner des fonctions qui prennent plus qu'un argument.

