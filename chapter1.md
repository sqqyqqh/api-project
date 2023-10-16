
Installation, Introduction à GraphQL, et mise en place

Installation, Introduction à GraphQL et mise en place

## Partie 1: Introduction à GraphQL

### 1.1 Historique et contexte de GraphQL

**GraphQL** a été développé par Facebook en 2012 pour répondre à un besoin spécifique : améliorer les performances et la flexibilité des requêtes des applications mobiles. Avant GraphQL, les équipes de développement avaient principalement recours aux API REST, qui présentaient des limites en termes de performance et de flexibilité, en particulier pour les applications mobiles qui nécessitaient des données optimisées pour réduire la consommation de bande passante.

### 1.2 Comparaison entre GraphQL et REST

Bien que **REST** ait été la norme pour la création d'API pendant de nombreuses années, GraphQL offre une alternative flexible. Voici quelques différences clés :

- **Approche centrée sur les données vs. ressources** : Contrairement à REST qui est basé sur des ressources, GraphQL se centre sur les données. Cette approche permet de décrire précisément les données nécessaires, évitant le sur ou sous-fetching.
  
- **Un point de terminaison vs. multiples** : Avec REST, vous pourriez avoir de nombreux points de terminaison pour différentes ressources. Avec GraphQL, un unique point d'entrée sert à répondre à toutes les demandes.
  
- **Moins de requêtes réseau** : Grâce à sa capacité à agréger les données, GraphQL peut souvent réduire le nombre de requêtes nécessaires pour obtenir des données complexes.

### 1.3 Les principes fondamentaux de GraphQL

- **Requêtes**: Dans GraphQL, vous décrivez vos besoins en données via une requête. Cette requête est ensuite traitée par le serveur et renvoie précisément ce que vous avez demandé, ni plus ni moins.

- **Typage fort**: Chaque requête est vérifiée par rapport à un schéma, garantissant ainsi que vous obtenez exactement ce que vous attendez et que le serveur fournit des informations précises.

- **Résolveurs** : Ce sont des fonctions qui aident GraphQL à déterminer comment obtenir des données pour un type particulier. Si vous avez un type `Album` et que vous voulez savoir comment obtenir l'artiste pour cet album, le résolveur vous guidera.

### 1.4 Avantages et inconvénients de GraphQL

**Avantages** :
  
- **Performance améliorée** : En permettant aux clients de demander exactement ce qu'ils veulent, on peut souvent réduire la quantité de données transférées et traitées.
  
- **Evolutivité** : Ajoutez de nouveaux champs et types sans impacter les requêtes existantes. Les clients peuvent utiliser les nouveaux champs à leur rythme.

- **Réduit le besoin de versionner** : Contrairement aux API REST, où les changements peuvent nécessiter une nouvelle version de l'API, GraphQL permet d'évoluer en douceur.

**Inconvénients** :
  
- **Complexité accrue** : Pour de petits projets, GraphQL peut sembler trop complexe par rapport à un simple endpoint REST.
  
- **Cache côté client** : Avec REST, le cache HTTP est bien établi. Avec GraphQL, cela devient plus compliqué étant donné la nature dynamique des requêtes.

## Partie 2: Mise en place de l'environnement

### 2.1 Prérequis

Chaque étudiant devrait avoir une compréhension de base du **JavaScript**, car nous allons travailler principalement avec Node.js et Express. Avoir une connaissance préalable des bases de données serait également bénéfique.

### 2.2 Installation

1. **Installation de Node.js et npm**

   Si ce n'est pas déjà fait, installez Node.js à partir de [la page officielle](https://nodejs.org/). npm est inclus dans l'installation de Node.js.

2. **Initialisation du projet**

   Ouvrez un terminal et créez un nouveau dossier pour votre projet :

   ```bash
   mkdir mon-projet-graphql
   cd mon-projet-graphql
   ```

   Initialisez un nouveau projet npm :
   
   ```bash
   npm init -y
   ```

3. **Installation de dépendances**

   Vous aurez besoin d'express pour le serveur, de graphql et express-graphql pour l'intégration GraphQL :

   ```bash
   npm install express graphql express-graphql
   ```

4. **Sécurisation de votre API**

   Installer `helmet`, une collection de protections middleware pour sécuriser votre application :

   ```bash
   npm install helmet
   ```

   Dans votre `server.js` :

   ```javascript
   const helmet = require('helmet');
   app.use(helmet());
   ```

### 2.3 Configuration initiale du serveur

Avant de plonger dans GraphQL, nous allons d'abord mettre en place un serveur de base avec Express.

1. Créez un fichier `server.js` dans le dossier principal de votre projet.

2. Ajoutez le code de base pour démarrer un serveur Express :

   ```javascript
   const express = require('express');
   const { graphqlHTTP } = require('express-graphql');

   const app = express();

   app.use(express.json()); // Middleware pour parser le JSON

   app.listen(4000, () => {
      console.log('Listening on port 4000');
   });
   ```

3. Exécutez votre serveur avec :

   ```bash
   node server.js
   ```

### 2.4 Introduction à l'architecture d'une application GraphQL

Chaque application GraphQL a une structure de base :

- **Schéma** : Comme mentionné, le schéma est au cœur de toute application GraphQL. Il définit les types et les relations entre ces types.

- **Résolveurs** : Ce sont des fonctions qui résolvent les données pour chaque type dans le schéma. Si votre schéma a un type `Album`, vous aurez besoin d'un résolveur pour déterminer comment obtenir ces albums à partir de votre source de données.

- **Source de données** : Il s'agit de la source réelle de vos données, que ce soit une base de données, une API REST, un système de fichiers ou autre.

## Partie 3: Introduction au schéma GraphQL

### 3.1 Qu'est-ce qu'un schéma?

Comme

 mentionné précédemment, le schéma définit vos types de données et comment ces types sont liés les uns aux autres. Dans le contexte de notre application d'album musical, nous pourrions avoir des types tels que `Artiste`, `Album`, et `Chanson`.

1. Créez un nouveau dossier `graphql` dans votre dossier principal.

2. Dans ce dossier, créez un fichier `types.js`.

3. Ajoutez votre premier type :

   ```javascript
   const { GraphQLObjectType, GraphQLString } = require('graphql');

   const AlbumType = new GraphQLObjectType({
      name: 'Album',
      fields: () => ({
         id: { type: GraphQLString },
         titre: { type: GraphQLString },
         artiste: { type: GraphQLString }
      })
   });
   ```

Ce simple type définit un album avec un id, un titre et un artiste.

### 3.2 Relations entre types

Souvent, vous voudrez exprimer des relations entre différents types. Par exemple, un `Album` pourrait être lié à plusieurs `Chansons`.

```javascript
const ChansonType = new GraphQLObjectType({
   name: 'Chanson',
   fields: () => ({
      id: { type: GraphQLString },
      titre: { type: GraphQLString },
      album: {
         type: AlbumType,
         resolve(parent, args) {
            // Ici, utilisez la valeur parent pour obtenir l'album associé à cette chanson.
            return ...;
         }
      }
   })
});
```

Dans cet exemple, nous avons une relation entre un `Album` et une `Chanson`.