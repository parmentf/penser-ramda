# Objets et immutabilité

Dans le [chapitre précédent](style-sans-point.md), nous avons parlé d'écrire nos fonctions en utilisant le style _sans point_ ou _tacite_, où l'argument principal de notre fonction, la donnée (NDT: _the main data argument to our function_, où _data_ est invariable), n'est pas montrée explicitement.

Nous étions alors incapables de convertir toutes nos fonctions dans ce style parce qu'il nous manquait des outils. Il est temps de voir ces outils.

## Lire les propriétés d'un objet

Rappelons-nous l'exemple du droit de vote que nous avons revisité dans le dernier chapitre:

```js
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18
 
const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

Comme vous pouvez le voir, nous avons rendu `isCitizen` et `isEligibleToVote` _sans point_, mais nous n'avons pas pu le faire pour les trois premières fonctions.

Nous avons appris dans le chapitre [Programmation déclarative](programmation-declarative.md) que nous pouvions rendre les fonctions plus « déclaratives » en utilisant `equals` et `gte`. Commençons par là.

```js
const wasBornInCountry = person => equals(person.birthCountry, OUR_COUNTRY)
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => gte(person.age, 18)
```

Pour rendre ces fonctions _sans point_, nous avons besoin d'un moyen de créer une fonction finalement applicable à `person`. Le problème est que nous devons accéder aux propriétés de `person`, et que la seule manière que nous connaissons est l'impérative.


### prop

Heureusement, Ramda peut nous aider. Elle fournit la fonction `prop` pour accéder aux propriétés d'un objet.

Avec `prop`, nous pouvons transformer `person.birthCountry` en `prop('birthCountry', person)`. Commençons par ça.

```js
const wasBornInCountry = person => equals(prop('birthCountry', person), OUR_COUNTRY)
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(prop('age', person), 18)
```

Ouahou! Ça a l'air encore pire, maintenant. Mais continuons ce _refactoring_. D'abord, inversons l'ordre des arguments que nous passons à `equals`, pour que `prop` soit à la fin. `equals` rend le même résultat, quel que soit l'ordre des argument, nous ne prenons donc pas de risque.

```js
const wasBornInCountry = person => equals(OUR_COUNTRY, prop('birthCountry', person))
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(prop('age', person), 18)
```

Ensuite, grâce à la nature _curryfiéeè d'`equals` et `gte`, nous pouvons créer de nouvelles fonctions à appliquer au résultat de l'appel à `prop`.

```js
const wasBornInCountry = person => equals(OUR_COUNTRY)(prop('birthCountry', person))
const wasNaturalized = person => Boolean(prop('naturalizationDate', person))
const isOver18 = person => gte(__, 18)(prop('age', person))
```

C'est encore un peu moins bien, mais allons plus loin. Tirons avantage de la _curryfication_ une fois de plus avec tous les appels à `prop`.

```js
const wasBornInCountry = person => equals(OUR_COUNTRY)(prop('birthCountry')(person))
const wasNaturalized = person => Boolean(prop('naturalizationDate')(person))
const isOver18 = person => gte(__, 18)(prop('age')(person))
```

Encore pire. Mais nous retrouvons un motif familier. Nos trois fonctions ont la même forme que `f(g(person))` et nous savons depuis le chapitre [Combinaison de fonctions](combinaison-de-fonctions.md) que c'est l'équivalent de `compose(f,g)(person)`.

Tirons parti de cela. Utilisons `compose`.

```js
const wasBornInCountry = person => compose(equals(OUR_COUNTRY), prop('birthCountry'))(person)
const wasNaturalized = person => compose(Boolean, prop('naturalizationDate'))(person)
const isOver18 = person => compose(gte(__, 18), prop('age'))(person)
```

Maintenant nous arrivons quelque part. Les trois fonctions ressemblent à `person => f(person)`. Nous savons depuis le chapitre [Style sans point](style-sans-point.md) que nous pouvons rendre ces fonctions _tacites_.

```js
const wasBornInCountry = compose(equals(OUR_COUNTRY), prop('birthCountry'))
const wasNaturalized = compose(Boolean, prop('naturalizationDate'))
const isOver18 = compose(gte(__, 18), prop('age'))
```

Il n'était pas évident quand nous avons commencé que nos méthodes faisaient deux choses différentes. Elles accédaient toutes les deux à une propriété d'un objet et appliquaient une opération sur la valeur de cette propriété. Ce _refactoring_ en style _tacite_ (NDT: ou _sans point_) a rendu cela très explicite.

Voyons d'autres outils que Ramda propose pour travailler sur les objets.
