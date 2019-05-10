# PowerfullP4
Une implémentation en Javascript du jeu puissance 4 avec une IA min-max
## Présentation
J'avais réalisé un puissance 4 en Javascript à l'époque où je m'entraînais à mettre en forme une page web, pour laquelle j'avais essayé de développer une intelligence artificielle avant d'abandonner par manque de temps.
J'ai donc décidé de terminer ce projet en implémentant un algorithme min-max avec coupe alpha-bêta.
## Jouer contre l'IA
Le projet à été déployé sur mon site internet, il est donc disponible au lien suivant : antoine.chaffin.fr/p4
## Utiliser le code
Tout le code du jeu ainsi que celui de l'intelligence artificielle se trouvent dans le fichier jeu.js. L'objet p4 représente le jeu en lui même.
## Explication du code source 
### Constantes & attributs
Les constantes suivantes permettent de contrôler les paramètres globales du jeu.

| Constantes |                 Définitions                 |
|:----------:|:-------------------------------------------:|
| n          | Représente le nombre de lignes du plateau   |
| m          | Représente le nombre de colonnes du plateau |
```javascript
  n: 6,
  // Nombre de colonnes
  m: 7,
```
Les attributs suivants sont utilisés pour modéliser le jeu.

|     Attributs    |                    Définitions                   |
|:-----------------:|:------------------------------------------------:|
| dom_plateau : []  | Représente le plateau sous la forme d'un tableau |
| coups : int       |   Représente le nombre de coups joués au total   |
| turn : int        |   Permet de savoir à quel joueur c'est le tour   |
| game_status : int |    Permet de savoir si une partie est terminée   |
```javascript
 // cet tableau contient des pointeurs directs vers les cases 
   // (noeuds <td> du DOM) du plateau dans la page html
  dom_plateau: [],
  // un entier: 1 ou 2 (le numéro du prochain joueur)
  turn: 1,
  /* un entier:
     0: la partie continue
     -1: la partie est nulle
     1: joueur 1 a gagné
     2: joueur 2 a gagné
   */
  game_status: 0,
  // Nombre de coups joués
  coups: 0,
```
### Méthodes
#### init(parent, lignes, colonnes)
Méthode permettant d'initialiser l'objet p4 et de créer le tableau HTML contenant un certains nombre de lignes et de colonnes et d'ajouter le handler gérant le click pour jouer.

| Paramètres |                    Définitions                    |
|:----------:|:-------------------------------------------------:|
| parents    | Le nom de l'élément HTML auquel ajouté l'objet p4 |
| lignes     |                Le nombre de lignes                |
| colonnes   |               Le nombre de colonnes               |
```Javascript
if (lignes) this.n = lignes;
    if (colonnes) this.m = colonnes;
	
    t = document.createElement('table');
    t.id = 'plateau';

    for (var i = this.n - 1; i >= 0; i--) {
      var tr = document.createElement('tr');
      this.dom_plateau[i] = [];
      for (var j = 0; j < this.m; j++) {
        var td = document.createElement('td');
        td.dataset.column = j;
		
        tr.appendChild(td);
        this.dom_plateau[i][j] = td;
      }
      t.appendChild(tr);
    }
    parent.innerHTML = '';
    parent.appendChild(t);

	  t.addEventListener('click', function(e) { p4.handler(e); });
	}
```
#### set(row, column, player)
Méthode permettant de remplir la case du tableau après un coup, incrémente le nombre de coup et change le tour actuel.

| Paramètres |              Définitions             |
|:----------:|:------------------------------------:|
| row        |         Ligne du coup à jouer        |
| column     |        Colonne du coup à jouer       |
| player     | Numéro du joueur qui à jouer le coup |
```javascript
    // On colore la case
	this.dom_plateau[row][column].className = 'joueur' + player;
    // On compte le coup
    this.coups++;
    // On passe le tour : 3 - 2 = 1, 3 - 1 = 2
    this.turn = 3 - this.turn;
```
