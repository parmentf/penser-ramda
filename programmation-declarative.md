# Programmation déclarative

Ce chapitre est le quatrième de ce livre sur la programmation fonctionnel appelé _Penser Ramda_.

Dans le troisième chapitre, nous avons parlé de la combinaison de fonctions qui prennent plus d'un argument en utilisant  les techniques de l'application partielle et de la curryfication.

En commençant à écrire de petits blocs fonctionnels et à les combiner, nous nous sommes retrouvés à écrire beaucoup de fonctions qui encapsulent les opérateurs de JavaScript, comme les opérateurs arithmétiques, ceux de comparaison, de logique, et de contrôle du flux. Cela peut être fastidieux, heureusement Ramda peut nous aider.

Mais d'abord, un peu de contexte.

## Impératif contre déclaratif

Il existe de nombreuses façons de diviser le paysage des styles de programmation. Il y a le typage statique contre le typage dynamqiue, les langages interprétés contre les langages compilés, les langages de bas niveau contre ceux de haut niveau, _etc._

Une autre division de cet ordre est l'opposition de la programmation impérative à la programmation déclarative.

Sans aller trop loin, on peut dire que la programmation impérative est un style de programmation dans lequel les programmeurs disent à l'ordinateur quoi faire en lui disant comment il doit le faire. La programmation impérative a donné lieu à beaucoup de constructions que nous utilisons tous les jours: le contrôle du flux (les instructions `if-then-else` et les boucles), les opérateurs arithmétiques (`+`, `-`, `*`, `/`), les opérateurs de comparaison (`===`, `>`, `<`, _etc._) et les opérateurs logiques (`&&`, `||`, `!`).

La programmation déclarative est un style de programmation dans lequel les programmeurs disent à l'ordinateur quoi faire en lui disant ce qu'ils veulent. L'ordinateur doit trouver comment produire le résultat.

Prolog est un des langages classiques déclaratifs. En Prolog, un programme consiste en un ensemble de faits et un ensemble de règles d'inférence. Vous démarrez le programme en posant une question, et le moteur d'inférence de Prolog utilise les faits et les règles pour répondre à la question.

La programmation fonctionnelle est considérée comme un sous-ensemble de la programmation déclarative. Dans un programme fonctionnel, on définit des fonctions et on dit à l'ordinateur quoi faire en combinant ces fonctions.

Même dans des programmes déclaratifs, il est nécessaire d'effectuer des tâches similaires à celles que nous faisons dans les programmes impératifs. Le contrôle du flux, l'arithmétique, la comparaison et la logique sont encore les blocs de base avec lesquels nous travaillons. Mais nous devons trouver un moyen d'exprimer ces blocs d'une façon déclarative.

## Remplaçants déclaratifs

Comme nous programmons en JavaScript, un langage impératif, il est normal d'utiliser les constructions impératives standard quand on écrit du code JavaScript «normal».

Mais quand nous écrivons des transformations fonctionnelles en utilisant des pipelines et autres choses similaires, les constructions impératives ne sont pas adaptées.

Regardons certains de ces blocs de base que Ramda propose pour nous aider à nous sortir de ce guêpier.

## Arithmétique

Dans le deuxième chapitre, nous avons implémenté une série de transformations arithmétiques pour montrer ce qu'est un pipeline:

```js
const multiply = (a, b) => a * b
const addOne = x => x + 1
const square = x => x * x
 
const operate = pipe(
  multiply,
  addOne,
  square
)
 
operate(3, 4) // => ((3 * 4) + 1)^2 => (12 + 1)^2 => 13^2 => 169
```

Remarquez comme nous avons dû écrire des fonctions pour tous les blocs de base que nous avons voulu utiliser.

Ramda fournit les fonctions `add`, `substract`, `multiply` et `divide` pour remplacer les opérateurs arithmétiques standard. Ainsi nous pouvons utiliser `multiply` à la place de la fonction que nous avons écrite nous-mêmes, nous pouvons tirer parti de la fonction curryfiée `add` de Ramda pour remplacer notre `addOne`, et nous pouvons écrire `square` en termes de `multiply` de la même manière:

```js
const square = x => multiply(x, x)
 
const operate = pipe(
  multiply,
  add(1),
  square
)
```

`add(1)` est très similaire à l'opérateur d'incrémentation (`++`), mais l'opérateur d'incrémentation modifie la variable incrémentée, c'est donc une mutation. Comme nous l'avons appris dans le premier chapitre, l'immutabilité est un principe fondamental de la programmation fonctionnelle, donc nous ne voulons pas utiliser `++` ni son cousin `--`.

Nous pouvons utiliser `add(1)` et `substract(1)` pour incrémenter et décrémenter, mais parce que ces deux opérations sont si communes, Ramda fournit `inc` et `dec` à la place.

Alors nous pouvons simplifier encore notre pipeline:

```js
const square = x => multiply(x, x)
 
const operate = pipe(
  multiply,
  inc,
  square
)
```

`substract` est le remplaçant de l'opérateur binaire `-`, mais l'opérateur unaire `-` existe aussi pour rendre la valeur opposée. Nous pourrions utiliser `multiply(-1)`, mais Ramda fournit la fonction `negate` pour accomplir cette tâche.

## Comparaison

Dans le chapitre 2, nous avons écrit quelques fonctions pour déterminer si une personne avait le droit de vote. La version finale de ce code ressemblait à ça:

```js
const wasBornInCountry = person => person.birthCountry === OUR_COUNTRY
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => person.age >= 18
 
const isCitizen = either(wasBornInCountry, wasNaturalized)
 
const isEligibleToVote = both(isOver18, isCitizen)
```

Remarquez que certaines de nos fonctions utilisent les opérateurs de comparaison classiques (`===` et `>=` dans ce cas). Comme vous devriez maintenant le devinez, Ramda donne aussi des remplaçants pour ceux-ci.

Modifions notre code pour utiliser `equals` à la place de `===` et `gte` (NDT: _greater than or equal_) au lieu de `>=`.

```js
const wasBornInCountry = person => equals(person.birthCountry, OUR_COUNTRY)
const wasNaturalized = person => Boolean(person.naturalizationDate)
const isOver18 = person => gte(person.age, 18)
 
const isCitizen = either(wasBornInCountry, wasNaturalized)
 
const isEligibleToVote = both(isOver18, isCitizen)
```

Ramda fournit aussi `gt` pour `>`, `lt` pour `<` et `lte` pour `<=`.

Notez que ces fonctions prennent leurs arguments dans ce qui semble être l'ordre normal (le premier argument est-il plus grand que le second?). Cela a du sens utilisé isolément, mais peut être déroutant en combinant des fonctions. Ces fonctions semble violer le principe «données à la fin» de Ramda, donc nous allons devoir faire preuve de prudence quand nous les utiliserons dans des pipelines et des situations semblables. C'est là qu'opportunément interviennent `flip` et l'espace réservé (`__`).

En plus d'`equals`, il y a `identical` pour déterminer si deux valeurs référencent le même emplacement mémoire.

Il y a plusieurs usages communs de `===`: vérifier si une chaîne ou un tableau sont vides (`str === ''` ou `arr.length === 0`), et vérifier si une variable est `null` ou `undefined`. Ramda fournit des fonctions adaptées à ces deux cas: `isEmpty` et `isNil`.

## Logique

Dans le chapitre 2 (et juste au-dessus), nous avons utilisé les fonctions `both` et `either` à la place des opérations `&&` et `||`. Nous avons aussi parlé de `complement` à la place de `!`.

Ces fonctions combinées marchent très bien quand les fonctions que nous combinons agissent toutes deux sur la même valeur. Ci-dessus, `wasBornInCountry`, `wasNaturalized` et `isOver18` s'appliquent toutes à une personne.

Mais quelquefois nous devons appliquer `&&`, `||` et `!` à des valeurs disparates. Pour ces cas, Ramda nous donne les fonctions `and`, `or`, et `not`. Je les conçois de cette manière: `and`, `or`, et `not` s'appliquent à des valeurs alors que `both`, `either` et `complement` s'appliquent à des fonctions.

Un usage habituel de `||` est de fournir des valeurs par défaut. Par exemple, nous pourrions écrire quelque chose comme ça:

```js
const lineWidth = settings.lineWidth || 80
```

C'est un idiotisme courant, qui marche la plupart du temps, mais qui repose sur la définition JavaScript de _falsy_. Et si `0` était un réglage valide? Comme `0` est _falsy_, nous finirions avec une ligne de largeur 80.

Nous pourrions utiliser la fonction `isNil` que nous venons juste d'apprendre, mais une fois de plus Ramda a une meilleure option pour nous: `defaultTo`.

```js
const lineWidth = defaultTo(80, settings.lineWidth)
```

`defaultTo` vérifie si le second argument `isNil`. S'il ne l'est pas, il renvoie cette valeur, sinon il renvoie la première valeur.

## Conditionnelles

Le contrôle de flux est moins indispensable dans les programmes fonctionnels, mais il reste occasionnellement utile. Les fonctions d'itération de collection dont nous avons parlé dans le chapitre 1 traitent la plupart des boucles, mais les conditionnelles sont encore très importantes.

### ifElse

Écrivons une fonction, `forever21`, qui prend un âge et renvoie l'âge de l'année suivante. Mais, comme son nom l'indique, une fois que l'âge atteint 21, il n'évolue plus.

```js
const forever21 = age => age >= 21 ? 21 : age + 1
```

Remarquez que notre conditionnelle (`age >= 21`) et la seconde branche (`age + 1`) peuvent toutes deux être écrites comme des fonctions de l'`age`. Nous pouvons réécrire la première branche (`21`) en une fonction constante (`() => 21`). Maintenant nous auvons trois fonctions qui prennent (ou ignorent) l'`age`.

Nous sommes maintenant en position d'utiliser la fonction `ifElse` de Ramda, qui est la fonction équivalente à la structure `if...then...else` ou à son cousin plus court, l'opérateur ternaire (`?:`).

```js
const forever21 = age => ifElse(gte(__, 21), () => 21, inc)(age)
```

Comme nous l'avons dit plus haut les fonctions de comparaison ne fonctionnent pas comme aimerions quand nous les combinons, alors nous sommes forcés d'introduire l'espace réservé (`__`). Nous pouvons aussi passer à `lte`:

```js
const forever21 = age => ifElse(lte(21), () => 21, inc)(age)
```

Dans ce cas, nous devons lire ceci comme _21 est plus petit que ou égal à age_. Je vais continuer avec la version utilisant l'espace reservé parce que je la trouve plus lisible et moins déroutante.

### Constantes

Les fonctions constantes sont très utiles dans des situations telles que celle-là. Comme vous pouvez l'imaginer, Ramda nous fournit un raccourci. Dans ce cas, le raccourci se nomme `always`.

```js
const forever21 = age => ifElse(gte(__, 21), always(21), inc)(age)
```

Ramda donne aussi `T` et `F` comme des raccourcis pour `always(true)` et `always(false)`.

### Identity

Essayons une autre fonction, `alwaysDrivingAge`. Cette fonction prend un âge, et le renvoie s'il est `gte` 16. Mais s'il est plus petit que 16, elle renvoie 16. Ça autorise n'importe qui à prétendre qu'il a l'âge de conduire, même si ce n'est pas le cas.

```js
const alwaysDrivingAge = age => ifElse(lt(__, 16), always(16), a => a)(age)
```

La seconde branche de la conditionnelle (`a => a`) est un autre motif habituel en programmation fonctionnelle. Il est connu comme la fonction identité. C'est-à-dire une fonction qui renvoie l'argument qu'elle reçoit, quel qu'il soit.

Comme vous vous en doutez, Ramda nous fournit une fonction `identity`.

```js
const alwaysDrivingAge = age => ifElse(lt(__, 16), always(16), identity)(age)
```

`identity` peut prendre plus d'un argument, mais elle retourne toujours le premier argument. Si nous voulons renvoyer quelque chose d'autre que le premier argument, il y a la fonction plus générale `nthArg` (NDT: _nième arg_). Elle est moins utilisée que `identity`.

### when et unless

Avoir une instruction `ifElse` avec `identity` dans une des branches de la conditionnelle est courant, aussi Ramda fournit encore des raccourcis.

Si, comme dans notre cas, la seconde branche est `identity`, nous pouvons utiliser `when` au lieu de `ifElse`:

```js
const alwaysDrivingAge = age => when(lt(__, 16), always(16))(age)
```
Si la première branche de la conditionnelle est `identity`, nous pouvons utiliser `unless`. Si nous inversons notre condition pour utiliser `gte(__, 16)`, nous pouvons utiliser `unless`.

```js
const alwaysDrivingAge = age => unless(gte(__, 16), always(16))(age)
```

### cond

Ramda donne aussi la fonction `cond` qui peut remplacer une instruction `switch` ou une chaîne d'instructions `if...then...else`.

Je vais répéter l'exemple de la documentation de Ramda pour montrer comment ça s'utilise:

```js
const water = temperature => cond([
  [equals(0),   always('water freezes at 0°C')],
  [equals(100), always('water boils at 100°C')],
  [T,           temp => `nothing special happens at ${temp}°C`]
])(temperature)
```

Je n'ai encore jamais eu besoin de `cond` dans mon code Ramda, mais il y a de nombreuses années j'avais l'habitude d'écrire en Common Lisp, alors  `cond` est comme une vieille amie.
