---
title: Sa première API en Next.js
date: 2021/3/18
description: Créer sa première API en Next.js
tag: next.js
author: You
---

import { RoughNotation } from "react-rough-notation"
import Image from 'next/image'
import mainPicture from '../../public/images/20210723_first_api_next/photo-1488229297570-58520851e868.webp'

<Image
  src={mainPicture}
  alt="Photo"
  priority
  placeholder="blur"
/> Crédit : [Joshua Sortino](https://unsplash.com/photos/LqKhnDzSF-8)

# Créer sa première API en Next.js

[Next.js](https://nextjs.org/) est un framework React, couplé à un serveur backend qui permet d'héberger des API.

Ce que nous allons voir aujourd'hui, c'est ajouter un endpoint d'une API, nous permettant de nous renvoyer certaines informations sur notre application.

Quelle version de notre outil tourne en ce moment sur la production ?

## Anatomie d'une API

Une API, dans son essence, c'est juste ceci :

/pages/api/user.js
```
function handler(req, res) {
  res.status(200).json({ name: 'John Doe' })
}

export default handler
```

Pour invoquer notre API, nous pouvons utilser notre navigateur et aller sur [http://localhost:3000/api/user](http://localhost:3000/api/user).

Une API, c'est donc une fonction qui prend 2 paramètres : une HTTP request (nommé par convention req), et une HTTP response (nommé par convention res).

Nous verrons plus loin à quoi sert la request et passer directement à la response.

Pour envoyer un résultat à notre consommateur, il faut passer par cet objet response.

Dans notre exemple, on associe à res le status 200, qui est le statut prévu par HTTP pour indiquer que tout va bien.

Et on ajoute une donnée au format JSON (on pourrait ajouter un simple text, mais JSON permet beaucoup plus de choses donc on l'utilisera la plupart du temps).

## Une API qui renvoie des informations techniques

Ce que nous allons voir maintenant, c'est une API qui affiche des informations sur l'application qu'on utilise et qui tourne.

Par exemple, nous allons afficher le n° de version de notre code.

Pour cela, nous allons utiliser les informations que nous avons renseignés dans le fichier `package.json`.

/pages/api/info.js
```
import config from '../../package.json';

async function handler(req, res) {
  const { name, version } = config;

  res.status(200).json({
    version,
    name
  });
};

export default handler
```

Pour voir le résultat, cliquer sur [http://localhost:3000/api/info.js](http://localhost:3000/api/info.js).

Cela commence à devenir intéressant.
Nous réutilisons les données que nous renseignons (la version et le nom de notre module), et nous pouvons les afficher.

Utile quand on a plusieurs onglets avec plusieurs versions de notre code sur différents environnents !

## Une API qui appelle une autre API

Passons un cran plus loin : notre API est donc un fournisseur de service, car c'est une fonction qui fait un traitement.

Et si notre API était elle-même auparavant le consommateur d'une autre API?

Supposons par exemple que notre code est hébergé sur GitHub.
Comment récupérer les méta informations sur notre repository, comme le nombre de like, le nombre de forts, etc. ?

Par chance, GitHub fournit une [API](https://docs.github.com/en/rest) très complète et surtout, très facile d'accès !

Pour cela, nous devons d'abord compléter notre fichier `package.json`.

```
{
  "name": "next-stats-api",
  "version": "0.1.0",
  "repository": {
    "type": "git",
    "url": "https://github.com/pom421/next-stats-api"
  },
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "11.0.1",
    "react": "17.0.2",
    "react-dom": "17.0.2"
  },
  "devDependencies": {
    "eslint": "7.31.0",
    "eslint-config-next": "11.0.1"
  }
}
```

```
import config from '../../package.json';

async function handler(req, res) {
  const { name, version, repository : { url } } = config;

  const apiUrl = url.replace("https://github.com", "https://api.github.com/repos")

  let githubInfos

  if (apiUrl) {
   githubInfos = await fetch(apiUrl).then(r => r.json())
  }

  const {stargazers_count, forks, open_issues  } = githubInfos

  res.status(200).json({
    version,
    name,
    stargazers_count,
    forks,
    open_issues
  });
};

export default handler
```

Et voila !
Nous avons créé une API qui est déjà très utile, lors d'un debug pour vérifier la version de son code.

Et nous avons maintenant la possibilité de donner des informations supplémentaires pertinentes.

Imaginer par exemple la possibilité de renvoyer des statistiques sur son site e-commerce, comme le nombre de commandes passées, le nombre d'utilisateurs en temps réel, etc. Next permet d'ajouter très rapidement des indicateurs techniques et métiers, très utiles pour suivre le bon déroulement des opérations.

L'utilisation du nom de répertoire et de fichier pour servir d'URL est très intuitif.
