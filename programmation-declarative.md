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

