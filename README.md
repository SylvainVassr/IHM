# IHM

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
# IHM

Notre application appelé "Battement" nous permet d'avoir un visuel des monuments à visiter à proximitée d'une gare.

Pour cela, nous avons 2 API :
1. SNCF : qui nous retourne toutes les gares de France
2. Monuments : qui nous retourne les monuments historiques

Tout d'abord, nous avons la map, réalisé avec leaflet afin d'avoir un visuel. De base celle ci ne comporte aucune information. Elle sera changer lors de la validation de notre formulaire.

En effet à côté de cette dernière se trouve un formulaire assez simple demandant à l'utilisateur le nom de la gare ou il est supposé se rendre ainsi qu'un rayon.
Lors de l'envoi, nous traitons toutes les distances entre les monuments la gare selectionnée et ajoutons les monuments ayant un distance inferieur à celle renseignée par l'utilisateur.

Ces monuments sont donc affichés sur la map par l'intermediaire de "marker" sur lesquelles nous pouvons lire leur nom et la distance.
