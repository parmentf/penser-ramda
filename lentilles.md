# Lentilles

Dans les chapitres précédents [Objets et immutabilité](objets-et-immutabilite.md) et [Tableaux et immutabilité](tableaux-et-immutabilite.md), nous avons appris comment lire, mettre à jour et transformer les propriétés d'un objet et des éléments d'un tableau d'une façon déclarative et immutable.

Ramda offre un outil plus générique pour effectuer ces opérations: la lentille (NDT: _lens_ en anglais, la lentille d'une longue-vue).

# Qu'est-ce qu'une lentille?

Une lentille combine une fonction _getter_ et une fonction _setter_ en une seule unité. Ramda fournit un ensemble de fonctions pour travailler avec les lentilles.

On peut voir un lentille comme quelque chose qui fait le point sur une partie spécifique d'une structure de données plus grande.

