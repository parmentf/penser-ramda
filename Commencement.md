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

