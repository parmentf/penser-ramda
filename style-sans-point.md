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
