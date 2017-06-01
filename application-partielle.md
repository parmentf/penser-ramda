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

