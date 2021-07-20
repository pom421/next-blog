---
title: Upgrader un projet Next.js
date: 2021/3/18
description: Learn more about Next.js pages.
tag: next.js
author: You
---

import { RoughNotation } from "react-rough-notation"
import Image from 'next/image'

<Image
  src="/images/scripts_update_next/markus-winkler-cxoR55-bels-unsplash.jpeg"
  alt="Photo"
  width={1125}
  height={750}
  priority
  className="next-image"
/>_Credits: https://unsplash.com/@markuswinkler_

# Upgrader un projet Next.js

Next.js est un framework qui évolue constamment. Pour profiter des avancées pour le développeur, il est utile de savoir mettre à jour la librairie Next.

## Monter les versions non "breaking change"

Avant toute chose, il faut parler du concept de [semantic versioning](https://docs.npmjs.com/about-semantic-versioning).

C'est la notion de donner une version, qui a un sens sur l'importance du changement.

Pour prendre un exemple, en supposant qu'on utilisait jusque là une librairie en version 1.0.0.
- une version patch, serait du type 1.0.1. Elle indique qu'une évolution peu impactante a été apportée, souvent pour des raisons de sécurité.
- une version mineure, serait du type 1.1.0. Elle indique qu'une évolution moyennement impactante a été apportée.
- une version majeure, serait du type 2.0.0

Cette convention est pratique car l'évolution de la partie majeure nous informe qu'un changement important ("breaking change") a été apporté sur l'API de cette librairie. En revanche, une version mineure, et encore plus une version patch, ne doit pas avoir modifié fondamentalement l'utilisation de cette librairie.

Fort ce ces informations, voici comment je m'y prends en pratique :

```
yarn upgrade-interactive
```

Cette commande permet de visualiser toutes les librairies dont une version mineur/patch est plus récente se trouve sur le registre [npmjs](https://www.npmjs.com/).

Vous pouvez même utiliser cette commande peu importe que la version soient un breaking change, avec la syntaxe `yarn upgrade-interactive --latest`.

Mais faire un upgrade-interactive avec l'option latest est dangereuse. Elle peut aider pour des petits projets pour faire évoluer rapidement les choses. Mais pour les plus grands projets, il vous faut une couverture de tests suffisamment grande pour avoir confiance dans une montée de version sans erreur apparente et vérifier la documentation des librairies sur les breaking change qui sont apparus.

Par exemple pour Next.js, voici la documentation pour l'upgrade : [https://nextjs.org/docs/upgrading](https://nextjs.org/docs/upgrading)

## Construire des scripts

J'ajoute de façon systématique à mes projets 2 scripts dans `package.json`.

- fresh-install est un script qui permet de supprimer le cache npm et les artefacts next et de réinstaller toutes les librairies npm.

```
"scripts": {
  ...
  "fresh-install": "rm -rf node_modules/ .next/ && yarn",
}
```

- check-all permet de lancer en séquence les traitements suivants :
  - Yarn lint
  - Yarn tsc # seulement pour le TypeScript
  - Yarn test

```
"scripts": {
  ...
  "type-check": "tsc --pretty --noEmit",
  "test": "jest",
  "check-all": "yarn lint && yarn type-check && yarn test"
}
```

Si le check-all passe, on peut passer au build de l'application en mode production.

Pour cela, on lance les commandes habituelles :

```
yarn build
yarn start
```

Si le build et le start passe, alors on peut tester manuellement via le navigateur ou via des outils end 2 end, type Cypress.

## Conclusion

La migration technique de son projet Next.js est une activité fastidieuse, ne nous le cachons pas 🥱, et qui est amenée à se répéter régulièrement.
Autant en fait une activité la plus rapide possible en suivant une check list et en automatisant ce que l'on peut.

Une fois que ces actes de migration faits, on peut être raisonnablement confiant que la chaîne de CI/CD passe sans encombre
😅.



