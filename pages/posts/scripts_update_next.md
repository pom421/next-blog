---
title: Upgrader un projet Next.js
date: 2021/3/18
description: Comment s'y prendre pour faire évoluer les versions des librairies d'un projet Next.js
tag: next.js
author: You
---

import { RoughNotation } from "react-rough-notation"
import Image from 'next/image'
import mainPicture from '../../public/images/scripts_update_next/markus-winkler-cxoR55-bels-unsplash.jpeg'
import yarnPicture from '../../public/images/scripts_update_next/yarn.png'

<Image
  src={mainPicture}
  alt="Photo"
  priority
  placeholder="blur"
/>_Credits: https://unsplash.com/@markuswinkler_

# Upgrader un projet Next.js

[Next.js](https://nextjs.org/) est un framework qui évolue rapidement.
La philosophie de Next est de mettre l'expérience développeur (DX) au même niveau d'exigence que l'expérience utilisateur (UX).

Pour profiter des avancées pour le développeur, il est utile de savoir mettre à jour Next et les autres librairies du projet.

## Monter les versions non "breaking change"

Avant toute chose, il faut parler du concept de [semantic versioning](https://docs.npmjs.com/about-semantic-versioning).

C'est une convention qui utilise le numéro de version, pour apporter du sens pour signifier si l'évolution de la librairie est superficielle ou importante.

Pour prendre un exemple, en supposant qu'on utilisait jusque là une librairie en version 1.0.0.
- une version patch, serait du type 1.0.1. Elle indique qu'une évolution peu impactante a été apportée, souvent pour des raisons de sécurité.
- une version mineure, serait du type 1.1.0. Elle indique qu'une évolution moyennement impactante a été apportée.
- une version majeure, serait du type 2.0.0. Dans ce cas, l'API de la librairie a été modifiée et des changements dans le code des développeurs est à prévoir.

Cette convention est utile car elle nous informe qu'un changement important ("breaking change") a été apporté sur cette librairie. En revanche, une version mineure, et encore plus une version patch, ne doit pas avoir modifié fondamentalement l'utilisation de cette librairie.

(À noter cependant que c'est une simple _convention_. Rien n'empêche malheureusement qu'un mainteneur ne respecte cette convention répandue et provoque des problèmes inattendus).

Fort ce ces informations, on peut utiliser une commande géniale ✨ 😍

```
yarn upgrade-interactive
```


Cette commande permet de visualiser toutes les librairies dont une version mineur/patch plus récente se trouve sur le registre [npmjs](https://www.npmjs.com/).

<Image
  src={yarnPicture}
  alt="Snapshot of yarn uprade-interactive command"
  placeholder="blur"
/>

C'est très intuitif : on navigue avec le clavier, on sélectionne avec `Espace`. Une fois qu'on a coché toutes les librairies à upgrader, on appuie sur `Entrée`.

De mon côté, je lance régulièrement cette commande. Donc j'ajoute toutes les dépendances affichées puisqu'elles ne sont pas censées cassée mon build. Nous verrons plus loin comment le vérifier.

Il est même possible d'utiliser cette commande en mode "J'ai de la chance", et lister aussi les librairires qui ont eu un breaking change, avec la syntaxe `yarn upgrade-interactive --latest`.

C'est cependant très risqué et pour ma part, je ne l'utilise que pour des petits projets qui ne sont pas encore en production. Pour un projet réel, c'est dangereux car même avec une batterie de tests significative, il y a toujours un risque que cette nouvelle version de librairie apporte un bug ou un effet de bord sur une autre partie du projet, qui ne sera pas détecté tout de suite.

### Mais alors quand change-t-on les versions majeures?

On a vu que la migration des dépendances de type mineur/patch, sont une opération qu'on peut faire sans trop réfléchir sur un grand ensemble de librairies.

En revanche, pour les versions majeures, chaque migration doit être traitée indépendamment. S'il s'avère que vous avez Next, Zod et React Hook Form qui évoluent tous les 3 avec des breaking changes, ce sont 3 tâches de développement à faire en séquence.

Pour chacune de ses tâches, il faut se reporter à la documentation des librairies sur les breaking change qui sont apparus.

Par exemple pour Next.js, voici la documentation pour l'upgrade : [https://nextjs.org/docs/upgrading](https://nextjs.org/docs/upgrading).

Certains projets, dont Next.js, propose des [codemod](https://nextjs.org/docs/advanced-features/codemods). Ce sont des scripts de développement qui transforment l'ensemble des fichiers de votre projet, pour le passer au nouveau format attendu par la librairie. Cela permet d'éviter des tâches répétitives et de traiter de grandes quantités de fichier automatiquement. Une review reste ensuite nécessaire pour vérifier que la transformation s'est bien passée.


## Construire des scripts

J'ajoute de façon systématique à mes projets 2 scripts dans `package.json`.

Cela me permet de tester en local que tout se passe bien, avant de commit et de lancer la chaîne d'intégration continue (CI).

- `fresh-install` est un script qui permet de supprimer le cache npm et le code généré par next et de réinstaller toutes les librairies npm.

```
"scripts": {
  ...
  "fresh-install": "rm -rf node_modules/ .next/ && yarn",
}
```

- `check-all` permet de lancer en séquence les traitements ESLint, compilateur TypeScript et tests Jest.

```
"scripts": {
  ...
  "type-check": "tsc --pretty --noEmit",
  "test": "jest",
  "check-all": "yarn lint && yarn type-check && yarn test"
}
```

Si `check-all` passe, on peut passer au build de l'application en mode production.

Pour cela, on lance les commandes habituelles :

```
yarn build
yarn start
```

Si le build et le start passe, alors on peut tester manuellement via le navigateur ou via des outils End-to-End, type [Cypress](https://www.cypress.io/).

## Conclusion

La migration technique de son projet Next.js est une tâche récurrente et fastidieuse 🥱.

Autant en fait une activité la plus rapide possible en suivant une check list et en automatisant ce que l'on peut.

Une fois ces actes de migration effectués, on commit son code et push sur le repository GitHub.

Si vous avez suivi ces conseils, on peut être confiant pour que la chaîne de CI/CD passe sans encombre 😅.

J'espère que ces informations vous seront utiles ! Bonne chance ! 🤞



