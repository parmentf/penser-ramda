# Lentilles

Dans les chapitres précédents [Objets et immutabilité](objets-et-immutabilite.md) et [Tableaux et immutabilité](tableaux-et-immutabilite.md), nous avons appris comment lire, mettre à jour et transformer les propriétés d'un objet et des éléments d'un tableau d'une façon déclarative et immutable.

Ramda offre un outil plus générique pour effectuer ces opérations: la lentille (NDT: _lens_ en anglais, la lentille d'une longue-vue).

## Qu'est-ce qu'une lentille?

Une lentille combine une fonction _getter_ et une fonction _setter_ en une seule unité. Ramda fournit un ensemble de fonctions pour travailler avec les lentilles.

On peut voir un lentille comme quelque chose qui fait le point sur une partie spécifique d'une structure de données plus grande.

## Comment puis-je créer une lentille?

La façon la plus générique de créer une lentille en Ramda est d'utiliser la fonction `lens` (NDT: lentille, en anglais). `lens` prend une fonction _getter_ et une fonction _setter_, et renvoie la nouvelle lentille.

```js
const person = {
  name: 'Randy',
  socialMedia: {
    github: 'randycoulman',
    twitter: '@randycoulman'
  }
}
 
const nameLens = lens(prop('name'), assoc('name'))
const twitterLens = lens(
  path(['socialMedia', 'twitter']),
  assocPath(['socialMedia', 'twitter'])
)
```

Ici nous utilisons `prop` et `path` comme fonctions _getter_ et `assoc` et `assocPath` comme fonctions _setter_.

Remarquez que nous avons à chaque fois dû dupliquer les arguments de la fonction _getter_ dans la fonction _setter_. Heureusement, Ramda a de beaux raccourcis pour les usages les plus communs des lentilles: `lensProp`, `lensPath` et `lensIndex`.

- `lensProp` crée une lentille qui fait le point sur une propriété d'un objet,
- `lensPath` crée une lentille qui fait le point sur une propriété imbriquée d'un objet,
- `lensIndex` crée une lentille qui fait le point sur un élément d'un tableau.

Nous pourrions réécrire nos précédentes lentilles avec `lensProp` et `lensPath`:

```js
const nameLens = lensProp('name')
const twitterLens = lensPath(['socialMedia', 'twitter'])
```

C'est bien plus simple et supprime la duplication. Je n'éprouve pratiquement jamais le besoin d'utiliser la fonction `lens` générique.

# Que puis-je en faire?

Bon, super, nous avons créer des lentilles. Que peut-on en faire?

Ramda fournit trois fonctions pour travailler avec des lentilles.

- `view` lit la valeur d'une lentille,
- `set` met à jour la valeur d'une lentille,
- `over` applique une fonction de transformation à la lentille.

```js
view(nameLens, person) // => 'Randy'
 
set(twitterLens, '@randy', person)
// => {
//   name: 'Randy',
//   socialMedia: {
//     github: 'randycoulman',
//     twitter: '@randy'
//   }
// }
 
over(nameLens, toUpper, person)
// => {
//   name: 'RANDY',
//   socialMedia: {
//     github: 'randycoulman',
//     twitter: '@randycoulman'
//   }
// }
```

Notez que `set` et `over` renvoient l'objet entier avec la propriété visée par la lentille modifiée comme demandé.
