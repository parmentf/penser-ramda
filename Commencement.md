# Commencement

J'utiliserai la bibliothèque [Ramda](http://ramdajs.com/ "la bibliothèque javascript Ramda") tout au long de ce livre, bien que la plupart des concepts exposés soient applicables à d'autres bibliothèques, comme [Underscore](http://underscorejs.org/ "lien vers la bibliothèque javascript Underscore") et [Lodash](https://lodash.com/ "lien vers la bibliothèque javascript Lodash"), ainsi qu'à d'autres langages de programmation.

Je m'en tiendrai aux sujets les plus légers, les moins académiques de la programmation fonctionnelle.Ceci principalement parce que je veux garder ce livre le plus accessible au plus grand nombre, mais aussi \(en partie\) parce que je ne suis pas encore très avancé sur la route de la programmation fonctionnelle.

## Ramda

Randy Coulman avait déjà parlé du sujet sur son blog avant de commencer la série _Thinking in Ramda_ :

* dans [Using Ramda With Redux](http://randycoulman.com/blog/2016/02/16/using-ramda-with-redux/), il a montré quelques exemples d'utilisation de Ramda dans l'écriture d'applications [Redux](http://redux.js.org/),
* dans [Using Redux-api-middleware With Rails](http://randycoulman.com/blog/2016/04/19/using-redux-api-middleware-with-rails/), il a utilisé Ramda pour simplifier la transformation de requêtes et de réponses \(NDT: _request and response payloads_\).

Je trouve que Ramda est une bibliothèque joliment conçue qui fournit beaucoup d'outils pour faire de la programmation fonctionnelle d'une manière claire et élégante.

Si vous voulez expérimenter Ramda en lisant ce livre, il y a un moyen très pratique de le faire depuis le navigateur, en utilisant la page [_Try Ramda_](http://ramdajs.com/repl/) sur le site même de Ramda.

## Fonctions

Comme son nom le suggère, la programmation fonctionnelle est une affaire de fonctions. Pour notre propos, nous définirons une fonction comme un morceau de programme appelée avec ou sans arguments et renvoyant un résultat.

Voici une fonction JavaScript simple:

```js
function double(x) {
  return x * 2
}
```

Avec les fonctions fléchées \(_arrow functions_ en anglais\), on peut écrire la même fonction avec beaucoup plus de concision. Je le mentionne dès à présent car nous allons beaucoup les utiliser.

```js
const double = x => x * 2
```

La plupart des langages de programmations permettent l'utilisation de fonctions.

Certains langages vont plus loin, et considèrent les fonctions comme la brique de base \(_first-class constructs_ en anglais\). Cela signifie qu'elles sont considérées comme d'autres valeurs. On peut:

* les référencer comme des variables ou des constantes,
* les passer en paramètres à d'autres fonctions,
* les renvoyer comme résultats d'autres fonctions.

JavaScript est un de ces langages, et nous allons tirer parti de cet aspect.

## Fonctions pures {#pure-functions}

En écrivant des programmes fonctionnels, il devient important de ne travailler qu'avec des fonctions soit-disant _pures_.

Les fonctions pures sont des fonctions qui n'ont pas d'effet de bord. Elle n'affectent aucune variable externe, elles ne lisent pas l'entrée standard, n'écrivent pas dans la sortie standard, elles ne lisent ni n'écrivent dans une base de données, elles ne modifient pas les paramètres qu'elles reçoivent, etc.

L'idée de base est que, si vous appelez une fonction avec les mêmes données encore et encore, vous obtiendrez toujours le même résultat.

Vous pouvez évidemment faire des choses avec des fonctions impures \(et vous devez le faire, si votre programme doit faire quelque chose d'intéressant\), mais pour l'essentiel, vous voudrez garder vos fonctions pures.

## Immutabilité {#immutability}

Un autre concept important dans la programmation fonctionnelle est celui d'_immutabilité_. Qu'est-ce que ça signifie? _Immutable_ veut dire _inchangeable_.

Quand je travaille de manière immutable, une fois que l'initialise une valeur ou un objet, je ne le/la modifie plus jamais. Cela signifie que je ne change pas les éléments d'un tableau ou les propriétés d'un objet.

Si j'ai besoin de changer quelque chose dans un tableau ou un objet, j'en renvoie une nouvelle copie avec la valeur modifiée. Nous en reparlerons en détails.

L'immutabilité travaille main dans la main avec les fonctions pures. Comme il est interdit aux fonctions pures d'entraîner des effets de bord, il leur est interdit de modifier des structures de données qui leur sont externes. Elles sont forcées de travailler les données de manière immutable.

## Par où commencer ? {#where-to-start}

La façon la plus simple de commencer à penser fonctionnellement est de remplacer les boucles par des fonctions _collection-iteration_ \(NDT: itération-collection?\)

Si vous venez d'un autre langage qui possède ces fonctions \(Ruby et Smalltalk en sont quelques exemples\), elles vous sont peut-être déjà familières.

Martin Fowler a une paire d'articles intéressants \(NDT: _great_, c'est sûrement mieux que ça\) à propos des _pipelines de collections_ qui montrent [comment utiliser ces fonctions](http://martinfowler.com/articles/collection-pipeline/), et comment [_refactorer_ du code existant en pipelines de collections](http://martinfowler.com/articles/refactoring-pipelines.html).

Remarquez que ces fonctions sont toutes \(à l'exception de `reject`\) disponibles sur `Array.prototype`, donc vous n'avez pas strictement besoin de Ramda pour les utiliser. Néanmoins, j'utiliserai les versions Ramda pour une question de cohérence avec la suite du livre.

### forEach {#foreach}

Essayons d'utiliser la fonction `foreach`plutôt que d'écrire une boucle explicite.

```js
// Remplacer ceci:
for (const value of myArray) {
  console.log(value)
}

// par:
forEach(value => console.log(value), myArray)
```

`forEach` prend une fonction et un tableau, puis appelle la fonction sur chaque élément du tableau.

Bien que `foreach` soit la plus facile d'approche de ces fonctions, c'est la moins utilisée d'entre elles quand on fait de la programmation fonctionnelle. Elle ne renvoie aucune valeur, alors elle n'est vraiment utilisée que quand on appelle des fonctions ayant des effets de bord.

### map {#map}

La fonction la plus importante à apprendre ensuite est `map`. Comme`forEach`,`map`applique une fonction à chaque élément d'un tableau. Toutefois, contrairement à `forEach`, `map` rassemble les résultats de l'application de la fonction dans un nouveau tableau et le renvoie.

Voici un exemple:

```js
map(x => x * 2, [1, 2, 3])  // --> [2, 4, 6]
```

Nous avons utilisé une fonction anonyme, mais nous pourrions aussi bien utiliser une fonction nommée:

```js
const double = x => x * 2

map(double, [1, 2, 3])
```

### filter/reject {#filterreject}

Ensuite, voyons `filter` et `reject`. Comme son nom l'indique, `filter` sélectionne les éléments d'un tableau sur la base d'une fonction. Par exemple:

```js
const isEven = x => x % 2 === 0
 
filter(isEven, [1, 2, 3, 4])  // --> [2, 4]
```

`filter`applies its function \(`isEven`in this case\) to each element of the array. Whenever the function returns a “truthy” value, the corresponding element is included in the result. Whenever the function returns a “falsy” value, the corresponding element is excluded \(filtered out\) from the array.

`reject`fait exactement l'inverse. Elle garde les éléments pour lesquels la fonction renvoie une valeur fausse_ \(_NDT: _falsy_ en anglais, qui signifie équivalent au booléen _faux_ du point de vue JavaScript\), et exclut les éléments pour lesquels elle retourne une valeur vraie \(NDT: _truthy_ en anglais, là encore, cela signfie _vrai_ du point de vue JavaScript\).

```js
reject(isEven, [1, 2, 3, 4]) // --> [1, 3]
```



