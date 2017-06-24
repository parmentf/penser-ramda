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

