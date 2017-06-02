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

## Application partielle de fonctions {#partially-applying-functions}

Nous pouvons réécrire toute fonction multi-arguments de cette manière si nous le souhaitons, mais nous n'avons pas la main sur toutes les fonctions que nous voudrions utiliser. De plus, nous pourrions vouloir utiliser des fonctions multi-arguments de la manière habituelle.

Par exemple, si nous avions un autre programme qui voulait seulement vérifier qu'un livre a été publié une année donnée, nous aimerions écrire `publishedInYear(book, 2012)`, mais nous ne pouvons plus le faire. À la place, nous devons écrire `publishedInYear(2012)(book)`. C'est moins lisible et plus embêtant.

Heureusement, Ramda propose deux fonctions pour nous aider: `partial`et `partialRight`.

Ces deux fonctions permettent d'appeler une fonction quelconque avec moins d'arguments qu'elle n'en réclame. Elles renvoient toutes les deux une nouvelle fonction qui prend les paramètres manquants et appelle la fonction originale une fois que tous les arguments ont été fournis.

La différence entre `partial` et `partialRight` tiens dans le fait que nous donnons les arguments en commençant par le paramètre le plus à gauche ou le plus à droite de la fonction originale.

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



