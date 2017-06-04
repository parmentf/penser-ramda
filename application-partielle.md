# Application partielle

Dans le [chapitre précédent](/combinaison-de-fonctions.md), nous avons parlé de combiner les fonctions de différentes manières, en finissant par les fonctions `compose` et `pipe` qui nous ont permis d'appliquer une série de fonctions en _pipeline_.

Dans ce chapitre, nous avons regardé des _pipelines_ simples de fonctions qui prenaient un seul paramètre. Mais si nous voulons utiliser des fonctions avec plus d'un paramètre?

Par exemple, disons que nous avons une collection de livres et que nous voulons trouver les titres de tous les livres publiés pendant une année particulière. Écrivons cela en utilisant seulement les fonctions d'itération de collection de Ramda:

```js
const publishedInYear = (book, year) => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(book => publishedInYear(book, year), books)

  return map(book => book.title, selected)
}
```

Ce serait bien de combiner `filter` et `map` dans un _pipeline_, mais nous ne savons pas encore comment, car `filter` et `map` prennent toutes les deux deux paramètres.

De même, on aimerait ne pas utiliser de fonction fléchée dans `filter`. Attaquons-nous à ce problème d'abord, parce qu'il va nous apprendre des choses que nous utiliserons pour créer le _pipeline_.

## Fonctions d'ordre supérieur

Dans le [premier chapitre](//Commencement.md), nous avons parlé des fonctions en tant que constructions de première classe \(NDT: _first-class constructs_\). Les fonctions de première classe peuvent être passées en paramètres à d'autres fonctions et retournées comme résultats d'autres fonctions. Nous avons déjà passé des fonctions en paramètres, mais pas encore retourné des fonctions.

Les fonctions qui prennent ou retournent d'autres fonctions sont connues comme des _fonctions d'ordre supérieur_.

Dans l'exemple ci-dessus, nous passons une fonction fléchée à `filter`: `book => publishedInYear(book, year)`, et nous voudrions essayer de nous débarrasser de la flèche. Pour ce faire, nous avons besoin d'une fonction qui prend un `book` et renvoie `true` si le livre a été publié dans une année donnée. Mais nous avons aussi besoin de lui faire connaître l'année, pour la rendre souple \(NDT: _flexible_\).

Il nous faut changer `publishedInYear` en une fonction qui renvoie une autre fonction. Je vais l'écrire avec la syntaxe de fonction entière \(NDT: _full function syntax_\) pour que vous puissiez voir ce qui se passe, puis je vous montrerai la version courte, en utilisant la syntaxe fléchée:

```js
// Full function version:
function publishedInYear(year) {
  return function(book) {
    return book.year === year
  }
}

// Arrow function version:
const publishedInYear = year => book => book.year === year
```

Avec cette nouvelle version de `publishedInYear`, nous pouvons réécrire l'appel à notre filtre, en éliminant la fonction fléchée:

```js
const publishedInYear = year => book => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(year), books)

  return map(book => book.title, selected)
}
```

Maintenant, quand nous appelons `filter`, `publishedInYear(year)` est évaluée immédiatement, retournant une fonction qui prend un `book`, ce qui est exactement ce dont `filter` a besoin.

## Application partielle de fonctions

Nous pouvons réécrire toute fonction multi-arguments de cette manière si nous le souhaitons, mais nous n'avons pas la main sur toutes les fonctions que nous voudrions utiliser. De plus, nous pourrions vouloir utiliser des fonctions multi-arguments de la manière habituelle.

Par exemple, si nous avions un autre programme qui voulait seulement vérifier qu'un livre a été publié une année donnée, nous aimerions écrire `publishedInYear(book, 2012)`, mais nous ne pouvons plus le faire. À la place, nous devons écrire `publishedInYear(2012)(book)`. C'est moins lisible et plus embêtant.

Heureusement, Ramda propose deux fonctions pour nous aider: `partial` et `partialRight`.

Ces deux fonctions permettent d'appeler une fonction quelconque avec moins d'arguments qu'elle n'en réclame. Elles renvoient toutes les deux une nouvelle fonction qui prend les paramètres manquants et appelle la fonction originale une fois que tous les arguments ont été fournis.

La différence entre `partial` et `partialRight` tient dans le fait que nous donnons les arguments en commençant par le paramètre le plus à gauche ou le plus à droite de la fonction originale.

Retournons à notre exemple originel et utilisons une de ces fonctions au lieu de réécrire `publishedInYear`. Comme nous ne voulons passer que l'année, et que c'est l'argument le plu sà droite, nous allons utiliser `partialRight`.

```js
const publishedInYear = (book, year) => book.year === year

const titlesForYear = (books, year) => {
  const selected = filter(partialRight(publishedInYear, [year]), books)

  return map(book => book.title, selected)
}
```

Si nous avions d'abord écrit une fonction `publishedInYear` prenant `(year, book)`au lieu de `(book, year)`, nous aurions pris `partial` à la place de `partialRight`.

Remarquez que les paramètres fournis à `partial` et `partialRight` doivent toujours se trouver dans un tableau, même quand il n'y en qu'un. Je ne saurais vous dire combien de fois je l'ai oublié, et obtenu un message d'erreur déroutant.

```
First argument to _arity must be a non-negative integer no greater than ten
```

## Curryfication

Devoir utiliser `partial` et `partialRight` partout rend la programmation verbeuse et fastidieuse. Mais devoir appeler des fonctions multi-arguments comme des séries de fonctions à un seul argument est lourd aussi.

Heureusement, Ramda nous offre la solution: `curry.`

La [curryfication](https://fr.wikipedia.org/wiki/Curryfication) est un autre concept de la programmation fonctionnelle. Techniquement, une fonction curryfiée est toujours une série de fonctions à un seul paramètre, ce qui est exactement ce dont je me plaignais. Dans les langages fonctionnels purs, la syntaxe rend cela transparent.

Mais parce que Ramda est une bibliothèque JavaScript, et que JavaScript n'a pas de syntaxe appropriée pour appeler une série de fonctions à un seul argument, les auteurs ont assoupli un peu la définition classique de la curryfication.

En Ramada, une fonction curryfiée peut être appelée avec une sous-partie de ses arguments, et retourne une nouvelle fonction qui accepte les arguments restant. Si vous appelez une fonction curryfiée avec tous ses arguments, elle ne fera qu'appeler la fonction originale \(NDT: ? _it will just call the function_\).

Vous pouvez considérer une fonction curryfiée comme le meilleur des deux mondes: vous pouvez l'appeler normalement avec tous ses arguments, et cela marchera tout simplement. Ou bien vous pouvez l'appeler avec un sous-ensemble de ses arguments, et elle agira comme si vous aviez utilisé `partial`.

Remarquez que cette souplesse apporte une petite baisse de performance, parce que `curry` doit déterminer comment la fonction a été appelée et ce qu'elle doit faire. En général, je ne `curry` des fonctions que quand j'ai besoin d'utiliser `partial` plus d'une fois.

Tirons avantage de `curry` pour notre fonction `publishedInYear`. Notez que `curry` marche toujours comme quand vous utilisiez `partial`: il n'y a pas de version `partialRight`. Nous développerons plus tard mais, pour l'instant, nous allons inverser les paramètres de `publishedInYear` pour que l'année soit le premier paramètre.

```js
const publishedInYear = curry((year, book) => book.year === year)

const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(year), books)

  return map(book => book.title, selected)
}
```

Nous pouvons de nouveau appeler `publishedInYear` avec `year` pour seul paramètre et recevoir une fonction qui prend un `book` et exécute notre fonction originale. Cependant, nous pouvons encore l'appeler normalement avec `publishedInYear(2012, book)` sans l'ennuyeuse syntaxe `)(`. Le meilleur des deux mondes!

## Ordre des paramètres

Remarquez que pour que `curry` marche pour nous, nous avons dû inverser l'ordre des paramètres. C'est très courant en programmation fonctionnelle, et c'est pourquoi toutes les fonctions Ramda sont écrites pour que les données soient passées en dernière position.

Les paramètres précédents peuvent être vus comme la configuration du traitement. Donc pour `publishedInYear`, le paramètre `year` est la configuration \(quelle année cherchons-nous?\) et le paramètre `book` est la donnée \(où cherchons-nous l'année?\).

Nous en avons déjà vu des exemples avec les fonctions d'itération de collection. Elles prennent toute la collection en dernier argument car cela rend ce style de programmation plus facile.

## Arguments dans le mauvais ordre

Que se passerait-il si nous avions laissé l'ordre original des arguments de `publishedInYear`? Comment pourrions-nous encore tirer parti de sa nature curryfiée?

Ramda propose quelques options.

### Flip

La première option est `flip`. `flip` prend une fonction à deux arguments plus et retourne une nouvelle fonction qui prend les mêmes arguments mais échange l'ordre des deux premiers arguments. C'est principalement utilisé pour les fonction à deux arguments, mais c'est plus général que ça.

En utilisant `flip`, on peut revenir à l'ordre original des arguments pour `publishedInYear`:

```js
const publishedInYear = curry((book, year) => book.year === year)
 
const titlesForYear = (books, year) => {
  const selected = filter(flip(publishedInYear)(year), books)
 
  return map(book => book.title, selected)
}
```

Dans la plupart des cas, je préfère utiliser l'ordre des arguments le plus pratique, mais si vous avez besoin d'utiliser une fonction que vous ne contrôlez pas, `flip` est une aide appréciable.

### Espace réservé

L'option la plus générale est l'argument _espace reservé_ (NDT: _placeholder_): `__`.

Et si nous avions une fonction curryfiée à trois arguments, que nous voulions lui fournir les premier et dernier arguments, laissant le dernier pour plus tard? Nous pouvons utiliser l'espace reservé (_placeholder_) à la place de l'argument du milieu:

```js
const threeArgs = curry((a, b, c) => { /* ... */ })

const middleArgumentLater = threeArgs('value for a', __, 'value for c')
```

Vous pouvez aussi utiliser l'espace reservé plus d'une fois lors d'un appel de fonction. Par exemple, si nous ne voulions fournir que l'argument du milieu.

```js
const threeArgs = curry((a, b, c) => { /* ... */ })
 
const middleArgumentOnly = threeArgs(__, 'value for b', __)
```

Nous pouvons utiliser l'espace réservé au lieu de `flip` si nous voulons:

```js
const publishedInYear = curry((book, year) => book.year === year)
 
const titlesForYear = (books, year) => {
  const selected = filter(publishedInYear(__, year), books)
 
  return map(book => book.title, selected)
}
```

Je trouve cette version plus lisible, mais si j'avais besoin de beaucoup utiliser la version `flip`pée de `publishedInYear`, je pourrais définir une fonction d'aide utilisant `flip`, et j'utiliserais cette fonction d'aide partout. Nous verrons quelques exemples dans les chapitres suivants.

Remarquez que `__` ne marche que pour les fonction curryfiées, alors que `partial`, `partialRight` et `flip` fonctionnent sur n'importe quelle fonction. Si vous avez besoin d'utiliser `__` sur une fonction normale, vous pouvez toujours l'encapsuler dans un appel à `curry`.
