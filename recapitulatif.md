# Récapitulatif

Tout le long des huit chapitres principaux, nous avons parlé de la bibliothèque JavaScript Ramda, qui propose des fonctions pour travailler en JavaScript d'une manière fonctionnelle, déclarative et immutable.

Dans ce livre, nous avons appris que l'API de Ramda suit les principes suivant:

- **données à la fin**: presque toutes les fonctions prennent les données comme dernier paramètre,

- **curryfication**: presque toutes les fonctions de Ramda sont _curryfiées_. C'est-à-dire que vous pouvez appeler une fonction avec un partie de ses arguments obligatoires, et qu'elle retournera alors une nouvelle fonction qui prendra les arguments restant. Une fois que tous les arguments ont été fournis, la fonction originale est invoquée.

Ces deux principes nous permettent d'écrire du code fonctionnel très clair qui combine des blocs de base en opérations plus puissantes (NDT: _powerful_).

## Résumé

Pour référence, voici un bref résumé du livre.

1. [Démarrage](commencement.md) nous amène aux concepts de fonctions, fonctions pures et immutabilité. Il démarre ensuite en nous montrant les fonctions d'itération de collection comme `map`, `filter` et `reduce`,
2. [Combinaison de fonctions](combinaison-de-fonctions.md) nous montre que nous pouvons combiner des fonctions de différentes manières en utilisant des outils comme `both`, `either`, `pipe` et `compose`,
3. [Application partielle](application-partielle.md) nous aide à découvrir qu'il peut être utile de ne fournir qu'une partie des arguments d'une fonction, permettant de ne passer le reste des arguments que plus tard, à une autre fonction. Nous utilisons `partial` et `curry` pour nous y aider et apprenons `flip` et l'espace réservé (`__`),
4. [Programmation déclarative](programmation-declarative.md) nous apprend la différence entre les programmation impérative et déclarative. Nous apprenons comment utiliser les remplaçants déclaratifs de Ramda pour l'arithmétique, les comparaisons, la logique, les conditionnelles,
5. [Style tacite](style-sans-point.md) introduit la notion de style tacite. En style tacite, on ne voit pas réellement le paramètre portant les données sur les lesquelles on travaille; c'est implicite. Nos programmes sont faits de simple et petits blocs qui sont combinés pour répondre aux besoins. Ce n'est qu'à la fin qu'on applique nos fonctions composées aux données réelles,
6. [Objets et immutabilité](objets-et-immutabilite.md) nous renvoie à l'idée de travail déclaratif, cette fois en nous donnant les outils pour lire, mettre à jour, supprimer et transformer les propriétés des objets,
7. [Tableaux et immutabilité](tableaux-et-immutabilite.md) continue sur le même thème et nous montre comment faire de même sur les tableaux,
8. [Lentilles](lentilles.md) conclut en introduisant le concept de lentille, un moyen de se concentrer sur une petite partie d'une structure de données plus large. En utilisant les fonctions `view`, `set` et `over`, on peut lire, mettre à jour et transformer la valeur visée dans le contexte d'une structure plus grande.

## Et ensuite?

Nous n'avons pas couvert l'ensemble de Ramda dans ce livre. En particulier, nous n'avons pas parlé des fonctions travaillant sur les chaînes de caractères, ni des concepts plus avancés comme les transducteurs.

Pour en savoir plus sur les possibilités de Ramda, je recommande la lecture attentive de sa [documentation](http://ramdajs.com/docs/). L'information qu'on y trouve est très riche. Toutes les fonctions sont regroupées par le type de données qu'elles traitent, même s'il y a quelques recouvrements. Par exemple, plusieurs des fonctions tableau fonctionnent aussi sur les chaînes, et `map` s’accommode aussi bien des tableaux que des objets.

Si vous êtes intéressé par les sujets les plus avancées, voici quelques endroits où aller (NDT: mais il vous faudra faire avec la langue anglaise):

- Transducteurs: voilà un bon [article d'introduction](http://simplectic.com/blog/2015/ramda-transducers-logs/) sur l'analyse de logs avec des transducteurs,
- Types de données algébriques: si vous avez beaucoup lu sur la programmation fonctionnelle, vous aurez entendu parler de types et de termes algébriques tels que _Foncteur_, _Applicatif_ (NDT: _Applicative_, je ne sais pas bien traduire ça) et _Monade_.  Si vous voulez explorer ces idées dans le contexte de Ramda, consultez le projet [ramda-fantasy](https://github.com/ramda/ramda-fantasy), qui implémente plusieurs types de données conformes à la [spécification du Monde Imaginaire](https://github.com/fantasyland/fantasy-land) (NDT: _Fantasy Land_) (aussi connue comme la Spécification Algébrique de JavaScript).
