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

