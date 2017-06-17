# Style sans point

Dans le [chapitre précédent](programmation-declarative.md), nous avons parlé d'écrire du code déclarativement (en disant à l'ordinateur comment s'y prendre).

Vous avez peut-être remarqué que plusieurs des fonctions que nous avons écrites (`forever21`, `drivingAge` et `water`, par exemple) prennent toute un paramètre, construisent une nouvelle fonction puis l'appliquent à ce paramètre.

C'est un motif très commun en programmation fonctionnnelle, et une fois de plus Ramda nous fournit les outils pour rendre cela plus propre.

## Style sans point

Dans le chapitre [Application partielle](application-partielle.md), nous avons parlé principalement parlé de deux principes de Ramda:

- mettre les données à la fin,
- tout curryfier.

Ces deux principent mènent à un style que les programmeur fonctionnels appellent «sans point» (NDT: _"pointfree"_). Quand j'y pense, je me dis _«Des données? Quelles données? Il n'y a pas de données ici.»_

Il y a un très bon billet de blog en anglais, [Why Ramda?](http://fr.umio.us/why-ramda/), qui illustre vraiment bien le style sans point. Les titre de sections tels que _Where's the Data?_, _All Right, Already! May I See Some Data?_ y sont révélateurs.

Nous n'avons pas encore les outils pour rendre tous nos exemples complètement sans point, mais nous pouvons commencer.

Regardons `forever21`:

```js
const forever21 = age => ifElse(gte(__, 21), always(21), inc)(age)
```

Remarquez que `age` n'apparaît que deux fois: une fois dans la liste des arguments, et une fois à la toute fin de la fonction, quand nous lui appliquons la fonction renvoyée par `ifElse`.

En faisant attention quand nous travaillons avec Ramda, nous verrons souvent ce motif. Ça signifie presque toujours qu'il y a un moyen de convertir cette fonction en style sans point.

Voyons à quoi cela ressemblerait:

```js
const forever21 = ifElse(gte(__, 21), always(21), inc)
```

Et _pouf !_ Nous venons de faire disparaître `age`. Style sans point. Remarquez qu'il n'ya pas de différence de comportement entre ces deux versions. Nous retournons toujours une fonction qui prend un âge, mais maintenant nous n'explicitons plus le paramètre `age`.

Nous pouvons faire la même chose avec `alwaysDrivingAge` et `water`.

À la fin du chapitre, `alwaysDrivingAge` ressemblait à ça:

```js
const alwaysDrivingAge = age => ifElse(lt(__, 16), always(16), identity)(age)
```

Nous pouvons appliquer la même transformation pour la rendre sans point (NDT: la notion de simplification traduirait sans doute mieux ce que l'auteur a voulu exprimer).

```js
const alwaysDrivingAge = when(lt(__, 16), always(16))
```

Et voilà dans quel état nous avions laissé `water`:

```js
const water = temperature => cond([
  [equals(0),   always('water freezes at 0°C')],
  [equals(100), always('water boils at 100°C')],
  [T,           temp => `nothing special happens at ${temp}°C`]
])(temperature)
```

Et la voici, dans le style sans point:

```js
const water = cond([
  [equals(0),   always('water freezes at 0°C')],
  [equals(100), always('water boils at 100°C')],
  [T,           temp => `nothing special happens at ${temp}°C`]
])
```

## Fonctions multi-arguments

Et avec les fonctions qui prennent plus d'un argument? Voyons à nouveau l'exemple `titlesForYear` du chapitre [Application partielle](application-partielle.md).

```js
const titlesForYear = curry((year, books) =>
  pipe(
    filter(publishedInYear(year)),
    map(book => book.title)
  )(books)
)
```

Remarquez que seul `books` apparaît deux fois: une fois comme le dernier paramètre de la liste d'arguments (données à la fin!), et une fois à la toute fin de la fonction, au moment où nous lui appliquons notre pipeline. C'est semblable au motif que nous avons vu ci-dessus avec `age`, donc appliquons lui la même transformation:

```js
const titlesForYear = year =>
  pipe(
    filter(publishedInYear(year)),
    map(book => book.title)
  )
```

Ça marche! Nous avons maintenant une version sans point de `titlesForYear`.

Honnêtement, je n'aurais probablement pas envie du style sans point dans ce cas, car JavaScript n'est pas pratique pour appeler une série de fonctions à un seul argument, comme nous en avons déjà parlé dans les chapitres précédents.

Si nous voulons utiliser `titlesForYear` dans un pipeline, tout va bien. Nous pouvons appeler `titlesForYear(2012)` très facilement. Mais si nous voulons l'utiliser seule, nous devons revenir au motif `)(` que nous avons vu dans le chapitre précédent: `titlesForYear(2012)(books)`. Pour moi, cela ne vaut pas la peine.

Mais pratiquement à chaque fois que j'ai une fonction à un seul argument qui suit le motif ci-dessus (ou qui peut être refactoré pour le suivre), je la rendrai _sans point_.


## Refactorer sans point

Il y aura des moments où nos fonctions ne suivrons pas ce motif. Nous pourrions traiter les données de nombreuses fois dans la même fonction.

C'était le cas dans plusieurs exemples du chapitre [Combinaison de fonctions](combinaison-de-fonctions.md). Dans ces exemples, nous avons refactoré notre code pour combiner les fonctions en utilisant des choses comme `both`, `either`, `pipe` et `compose`. Quand ce serait fait, rendre nos fonctions _sans point_ (NDT: j'ai de plus en plus de mal avec cette terminologie: je ne vois pas de point) serait une transformation relativement facile.

Revoyons l'exemple `isEligibleToVote`. Voilà d'où nous sommes partis:

```js
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18
 
const isCitizen = person => wasBornInCountry(person) || wasNaturalized(person)
 
const isEligibleToVote = person => isOver18(person) && isCitizen(person)
```

Commençons par `isCitizen`. Elle prend une `person` et lui applique deux fonctions différentes, en combinant les résultats avec `||`. Comme nous l'avons appris dans le chapitre [Combinaison de fonctions](combinaison-de-fonctions.md), nous pouvons utiliser `either` à la place pour combiner les deux fonctions en une nouvelle fonction, puis appliquer la fonction combinée à `person`.

```js
const isCitizen = person => either(wasBornInCountry, wasNaturalized)(person)
```

On peut faire de même avec `isEligibleToVote` en utilisant `both`:

```js
const isEligibleToVote = person => both(isOver18, isCitizen)(person)
```

Maintenant que nous avons fait ces _refactorings_, nous voyons que les deux fonctions suivent le motif dont nous avons parlé plus haut: `person` est mentionné deux fois, une fois comme argument de la fonction, et une fois à la fin quand nous lui appliquons notre fonction combinée. Maintenant nous pouvons refactorer pour avoir le style _sans point_:

```js
const isCitizen = either(wasBornInCountry, wasNaturalized)
const isEligibleToVote = both(isOver18, isCitizen)
```

## Pourquoi?

S'habituer au style _sans point_ prend du temps. Il peut être difficile de s'adapter aux arguments manquant partout. Il est important aussi d'être familier des fonctions de Ramda pour savoir combien d'arguments elles réclament.

Mais une fois habitué, avoir un tas de petites fonctions _sans point_ combinées de manière intéressante devient très puissant.

Quel est l'avantage du style _sans point_ ? On pourrait arguer que ce n'est qu'un exercice académique conçu pour gagner une médaille du mérite pour la programmation fonctionnelle. Pourtant, je pense qu'il y a quelques avantages, même en dépit du travail que cela demande pour s'y habituer:

- cela rend les programmes plus simples et plus concis. Ce n'est pas toujours une bonne chose, mais ça peut l'être,
- cela rend les algorithmes plus clairs. En se concentrant seulement sur les fonctions à combiner, nous avons une meilleure compréhension de ce qui se passe, sans que les arguments nous bouchent la vue,
- cela nous force à penser davantage à la transformation à effectuer qu'aux données à transformer,
- cela nous aide à penser nos fonctions comme des blocs génériques, qui peuvent fonctionner avec différentes sortes de données, plutôt que d'y penser comme à des opérations sur un type particulier de donnée. En donnant un nom à une donnée, nous ancrons nos pensées à l'endroit où nous pouvons utiliser nos fonctions. Laisser l'argument _donnée_ à la porte nous permet d'être plus créatif.

## Conclusion

Le style _sans point_, aussi connu sous le nom de [programmation tacite](https://en.wikipedia.org/wiki/Tacit_programming), peut rendre notre code plus clair et plus compréhensible. En _refactorant_ notre code pour combiner toutes nos transformations en une seule fonction, nous obtenons de plus petits blocs de base, réutilisables à plus d'endroits (NDT: style pas très satisfaisant).

## Chapitre suivant

Dans nos exemples, nous n'avons pas été capables de _refactorer_ toutes les fonctions dans le style _sans point_. Nous avons encore du code écrit dans un style impératif. La plupart de ce code traite des objets et des tableaux.

Nous devons trouver une manière déclarative d'interagir avec les objets et les tableaux. Et l'immutabilité? Comment manipuler des objets et des tableaux de manière immutable?

Le chapitre suivant, [Objets et immutabilité](objets-et-immutabilite.md), montre comment travailler avec des objets de manière fonctionnelle et immutable. Le chapitre d'après, [Tableaux et immutabilité](tableaux-et-immutabilite.md) fera de même pour les tableaux.
