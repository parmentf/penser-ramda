# Récapitulatif

Tout le long des huit chapitres principaux, nous avons parlé de la bibliothèque JavaScript Ramda, qui propose des fonctions pour travailler en JavaScript d'une manière fonctionnelle, déclarative et immutable.

Dans ce livre, nous avons appris que l'API de Ramda suit les principes suivant:

- **données à la fin**: presque toutes les fonctions prennent les données comme dernier paramètre,

- **curryfication**: presque toutes les fonctions de Ramda sont _curryfiées_. C'est-à-dire que vous pouvez appeler une fonction avec un partie de ses arguments obligatoires, et qu'elle retournera alors une nouvelle fonction qui prendra les arguments restant. Une fois que tous les arguments ont été fournis, la fonction originale est invoquée.

Ces deux principes nous permettent d'écrire du code fonctionnel très clair qui combine des blocs de base en opérations plus puissantes (NDT: _powerful_).
