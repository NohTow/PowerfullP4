# PowerfullP4
Une implémentation en Javascript du jeu puissance 4 avec une IA min-max
## Présentation
J'avais réalisé un puissance 4 en Javascript à l'époque où je m'entraînais à mettre en forme une page web, pour laquelle j'avais essayé de développer une intelligence artificielle avant d'abandonner par manque de temps.
J'ai donc décidé de terminer ce projet en implémentant un algorithme min-max avec coupe alpha-bêta.
## Jouer contre l'IA
Le projet à été déployé sur mon site internet, il est donc disponible au lien suivant : www.antoine.chaffin.fr/p4
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
#### play(column)
Méthode permettant de jouer un coup à partir de la colonne choisie (récupérée lors du clic), afin de pouvoir cliquer n'importe où sur la colonne et non pas sur un point précis puis vérifie si la partie est finie et affiche les résultats si c'est le cas.

| Paramètres |               Définitions              |
|:----------:|:--------------------------------------:|
| column     | La colonne dans laquelle jouer le coup |

```javascript
    // Vérifier si la partie est encore en cours
    if (this.game_status != 0) {
  		/*if (window.confirm("La partie est finie!\n\nSouhaitez-vous recommencer?")) {
  			this.reset();
			}*/
			return;
    }

    // Trouver la première case libre dans la colonne
    var row;
    for (var i = 0; i < this.n; i++) {
      if (!this.dom_plateau[i][column].className) {
        row = i;
        break;
      }
    }
    if (row === undefined) {
      window.alert("La colonne est pleine!");
      return;
    }

    // Effectuer le coup
    this.set(row, column, this.turn);

    // Vérifier s'il y a un gagnant, ou si la partie est finie
    if (this.win(row, column, 'joueur' + (3 - this.turn))) {
      this.game_status = 3 - this.turn;
    } else if (this.coups >= this.n * this.m) {
      this.game_status = -1;
    }

    //Au cours de l'affichage, pensez eventuellement, à afficher un 
    //message si la partie est finie...
    switch (this.game_status) {
      case -1: 
       // window.alert("Partie Nulle!!"); 
	   var txtElem = document.createElement('h2');
	   txtElem.id='resultat';
	   var txt = document.createTextNode("Partie nulle !");
	   txtElem.appendChild(txt);
	   document.getElementById("banniere").appendChild(txtElem);
	   var buttonElem= document.createElement('button');
	   buttonElem.id='bouton';
	   var txtButton = document.createTextNode("Rejouer !");
	   buttonElem.appendChild(txtButton);
	   buttonElem.onclick = p4.resetButton;
	   document.getElementById("wrapper").appendChild(buttonElem);
        break;
      case 1:
        //window.alert("Victoire du joueur 1"); 
		var txtElem = document.createElement('h2');
	   txtElem.id='resultat';
	   var txt = document.createTextNode("Victoire du joueur !");
	   txtElem.appendChild(txt);
	   document.getElementById("banniere").appendChild(txtElem);
	   var buttonElem= document.createElement('button');
	   buttonElem.id='bouton';
	   var txtButton = document.createTextNode("Rejouer !");
	   buttonElem.appendChild(txtButton);
	   buttonElem.onclick = p4.resetButton;
	   document.getElementById("wrapper").appendChild(buttonElem);
        break;
      case 2:
       // window.alert("Victoire du joueur 2"); 
	   var txtElem = document.createElement('h2');
	   txtElem.id='resultat';
	   var txt = document.createTextNode("Victoire de l'IA !");
	   txtElem.appendChild(txt);
	   document.getElementById("banniere").appendChild(txtElem);
	   var buttonElem= document.createElement('button');
	   buttonElem.id='bouton';
	   var txtButton = document.createTextNode("Rejouer !");
	   buttonElem.appendChild(txtButton);
	   buttonElem.onclick = p4.resetButton;
	   document.getElementById("wrapper").appendChild(buttonElem);
        break;
    }
```
#### handler(event)
Méthode permettant d'appeler la fonction play lors du clic de l'utilisateur ainsi que d'appeller la fonction permettant à l'IA de jouer après le coup du joueur.

| Paramètres |                        Définitions                        |
|:----------:|:---------------------------------------------------------:|
| event      | Le clic sur la colonne dans laquelle le joueur veut jouer |

```javascript
var column = event.target.dataset.column;
    
  	//attention, les variables dans les datasets sont TOUJOURS 
  	//des chaînes de caractère. Si on veut être sûr de ne pas faire de bêtise,
  	//il vaut mieux la convertir en entier avec parseInt
  	if (column){
    	this.play(parseInt(column));
	}
	if(this.getTurn()==2){
    bestMoove = this.minmax(this.getTab(), 7, Number.NEGATIVE_INFINITY,+Number.POSITIVE_INFINITY,true);
    console.log(bestMoove);
    this.play(parseInt(bestMoove["Moove"]));
```

#### geTab()
Méthode permettant de récuperer le plateau de jeu sous forme de tableau javascript

| Paramètres |                     Définitions                    |
|:----------:|:--------------------------------------------------:|
| RETURN     | Le plateau de jeu sous forme de tableau javascript |

```javascript
var resTab = []
		for(var i = 0 ; i < this.n ; i++){
			resTab.push([]);
		}
		for (var i = this.n - 1; i >= 0; i--){
			var string = "";
			for (var j = 0 ; j < this.m ; j++){
				if(this.dom_plateau[i][j].className == "joueur1"){
					string+="1 ";
					resTab[i].push(1);
				}else if(this.dom_plateau[i][j].className == "joueur2") {
					string+="2 ";
					resTab[i].push(2);
				}else{
					string+="0 ";
					resTab[i].push(0);
				}	
			}
		}
		return resTab;
``` 
#### is_won(board, player)
Méthode permettant de savoir si le joueur à gagné.

| Paramètres |              Définitions             |
|:----------:|:------------------------------------:|
| board      |      Le plateau de jeu à tester      |
| player     |     Le numéro du joueur à tester     |
| RETURN     | True si player à gagner, false sinon |

```javascript
// On vérifie les ligne
      for(var i = 0 ; i<this.m-3 ; i++){
        for(var j = 0 ; j<this.n ; j++){
          if(board[j][i]==player && board[j][i+1]==player && board[j][i+2]==player && board[j][i+3]==player){
            return true;
          }
        }
      }
      //On vérifie les colonnes
      for(var i = 0 ; i<this.m ; i++){
        for(var j = 0 ; j<this.n-3 ; j++){
          if(board[j][i]==player && board[j+1][i] == player && board[j+2][i]==player && board[j+3][i]==player){
            return true;
          }
        }
      }
      //on vérifie les diagonales qui montent
      for(var i = 0 ; i<this.m-3 ; i++){
        for(var j = 0 ; j<this.n-3 ; j++){
          if(board[j][i]==player && board[j+1][i+1]==player && board[j+2][i+2]==player && board[j+3][i+3]==player){
            return true;
          }
        }
      }
      //on vérifie les diagonales qui descendent
      for(var i = 0 ; i<this.m-3 ; i++){
        for(var j = 3 ; j<this.n ; j++){
          if(board[j][i]==player && board[j-1][i+1]==player && board[j-2][i+2]==player && board[j-3][i+3]==player){
            return true;
          }
        }
      }
    return false;  
```
