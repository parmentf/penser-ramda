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

### pick

Là où  `prop` lit une seule propriété d'un objet et renvoie sa valeur, `pick` lit plusieurs propriétés d'un objet et renvoie un nouvel objet contenant seulement ces propriétés.

Par exemple, si on veut seulement les nom et âge d'une `person`, on peut utiliser `pick(['name', 'age'], person)`.

### has

Si on veut seulement savoir si un objet possède une propriété sans lire sa vleur, on peut utiliser `has` pour vérifier ses propriétés, et `hasIn` pour remonter la chaîne des prototypes: `has('name', person)`.

### path

Là où `propr` lit une propriété d'un objet, `path` plonge à l'intérieur d'objets imbriqués. Par exemple, pour accéder au code postal d'une structure plus profonde, on peut faire `path(['address', 'zipCode'], person)`.

Notez que `path` est plus tolérant que `prop`. `path` renverra `undefined` dès qu'un élément le long du chemin (l'argument original inclus) est `null` ou `undefined`, alors que `prop` lèvera une erreur (NDT: _error_ en anglais, mais ce pourrait être _exception_?).

### propOr / pathOr

`propOr` et `pathOr` ressemblent à `prop` et `path` combinées à `defaultTo`. Elles permettent de fournir une valeur par défaut à utiliser quand la propriété ou le chemin (NDT: _path_) ne sont pas trouvées dans l'objet ciblé.

Par exemple, on peut fournir une valeur par défaut quand nous ne connaissons par le nom d'une personne: `propOr('<Unnamed>', 'name', person)`. Remarquez que contrairement à `prop`, `propOr` ne lèvera pas d'erreur si `person` est `null` ou `undefined`; à la place, elle retournera la valeur par défaut.

### keys / values

`keys` renvoie un tableau contenant les noms de toutes les propriétés propres à un objet. `values` renvoie les valeurs de ces propriétés. Ces fonctions sont utiles combinées à des fonctions d'itération de collection comme celles que nous avons vues au premier chapitre.

## Ajouter, mettre à jour et supprimer des propriétés

Nous avons maintenant de nombreux outils pour lire les objets déclarativement, mais qu'en est-il pour faire des changements?

Comme l'immutabilité est importante, nous ne voulons pas changer les objets directement. Au lieu de cela, nous voulons retourner de nouveaux objets qui ont été modifiés de la manière que nous voulons.

Une fois de plus, Ramda est d'une grande aide.

### assoc / assocPath

En programmation impérative, nous pouvons initialiser (NDT: _set_) ou modifier le nom d'une personne avec l'opérateur d'affectation: `person.name = 'New name'`.

Dans notre monde fonctionnel et immutable, il nous faut utiliser `assoc` à sa place: `const updatedPerson = assoc('name', 'New name', person)`.

`assoc` renvoie un nouvel objet avec la valeur de propriété ajoutée ou modifiée, laissant l'objet original inchangé. 

Il y a aussi `assocPath` pour mettre à jour une propriété imbriquée: `const updatedPerson = assocPath(['address', 'zipcode'], '97504', person)`.

### dissoc / dissocPath / omit

Et pour supprimer des propriétés? En programmation impérative, nous pourrions vouloir dire `delete person.age`. En Ramda, nous utiliserions `dissoc`: `const updatedPerson = dissoc('age', person)`.

`dissocPath` est semblable, mais va plus loin à l'intérieur de la structure de l'objet: `dissocPath(['address', 'zipCode'], person)`.

Il y a aussi `omit`, qui peut retrancher plusieurs propriétés à la fois. `const updatedPerson = omit(['age', 'birthCountry'], person)`.

Remarquez que `pick` et `omit` sont très similaires et complémentaires l'un de l'autre. Ils sont pratiques pour le _white-listing_ (NDT: de _white list_, liste blanche, garder uniquement cet ensemble de propriétés en utilisant `pick`), ou le _black-listing_ (NDT: de _black list_, liste noire, enlever cet ensemble de propriétés en utilisant `omit`).

## Transformer des propriétés

Nous en savons assez pour travailler avec des objets d'une manière déclarative et immutable. Écrivons une fonction, `celebrateBirthday`, qui met à jour l'âge d'une personne à son anniversaire.

```js
const nextAge = compose(inc, prop('age'))
const celebrateBirthday = person => assoc('age', nextAge(person), person)
```

C'est un motif assez habituel. Plutôt que de mettre à jour une propriété avec une nouvelle valeur connue, nous voulons vraiment transformer la valeur en appliquant une fonction à l'ancienne valeur, comme nous l'avons fait ici.

Je ne connais pas de bonne manière d'écrire ceci avec moins de répétition, tout en restant dans le style _tacite_, avec les seulement les outils que nous connaissons.

Ramda vient à notre secours avec sa fonction `evolve`. 

`evolve` prend un objet qui spécifie une fonction de transformation pour chaque propriété à transformer. Réécrivons `celebrateBirthday` en utilisant `evolve`:

```js
const celebrateBirthday = evolve({ age: inc })
```

Ce code demande de faire évoluer (NDT: _evolve_) l'objet cible (non montré ici à cause du style _tacite_) en faisant un nouvel objet avec les mêmes propriétés et valeurs, mais dont l'`age` est obtenu en appliquant `inc` à la valeur originale d'`age`.

`evolve` peut transformer plusieurs propriétés d'un coup et à plusieurs niveaux d'imbrication. L'objet de transformation peut avoir la même forme que l'objet à transformer, et `evolve` traversera alors récursivement les deux structures, appliquant les fonctions de transformation au passage.

Notez qu'`evolve` n'ajoutera pas de nouvelle propriété; si vous spécifiez une transformation pour une propriété qui n'est pas présente dans l'objet cible, `evolve` l'ignorera tout simplement.

Je me suis aperçu qu'`evolve` est rapidement devenu un cheval de bataille (NDT: _workhorse_) dans mes applications.
