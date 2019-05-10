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

#### getTab()
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
#### getMovePlayable(board)
Méthode permettant de récupérer les numéros des colonnes où l'on peut jouer sous forme de tableau javascript.

| Paramètres |                                 Définitions                                 |
|:----------:|:---------------------------------------------------------------------------:|
| board      |                          Le plateau de jeu à tester                         |
| RETURN     | Un tableau javascript contenant les numéros des colonnes où l'on peut jouer |

```javascript
	var resTab = [];
	for (var j = 0 ; j < this.m ; j++){
        	if(board[this.n-1][j] == 0) resTab.push(j)
      	}
	return resTab;
```

#### is_terminal_node(board)
Méthode permettant de savoir si la partie est finie

| Paramètres |                               Définitions                               |
|:----------:|:-----------------------------------------------------------------------:|
| board      |                        Le plateau de jeu à tester                       |
| RETURN     | True si un joueur à gagner ou qu'aucun coup n'est possible, false sinon |
```javascript
return (is_won(board,1) || is_won(board,2) || getMovePlayable(board).length == 0)
```

#### evaluate_window(window, player)
Méthode permettant de retourner une valeur représentant les possibilités offertes au joueur dans une "fenêtre" de 4 pions.

| Paramètres |                               Définitions                               |
|:----------:|:-----------------------------------------------------------------------:|
| window     |                 Tableau représentant 4 pions du plateau                 |
| RETURN     | Le score représentant les possibilités offerte par la fenêtre au joueur |

```javascript
countOwn = 0;
      countAdv = 0;
      for(var i = 0 ; i<4 ; i++){
        if(window[i] == player) countOwn++;
        else if(window[i] !=0) countAdv++;
      }
      //console.log("countOwn : "+countOwn);
      //console.log("Count adv : "+countAdv)
      if(countOwn==4) return 100;
      else if(countOwn == 3 && countAdv == 0) return 5;
      else if(countOwn == 2 && countAdv == 0) return 2;
      else if(countAdv == 3 && countOwn == 0) return -4;
      return 0;
    },
    score_position: function(board, player){
      var score = 0;
      //on test toutes les lignes possibles à réaliser
      for(i = 0 ; i< this.n; i++){
        for(j = 0 ; j<this.m-3 ; j++){
          window_test = [];
          window_test.push(board[i][j]);
          window_test.push(board[i][j+1]);
          window_test.push(board[i][j+2]);
          window_test.push(board[i][j+3]);
          score+= this.evaluate_window(window_test, player);
          //console.log(window_test);
        }
      }
      for(i = 0 ; i < this.m ; i++){
        for(j = 0; j< this.n-3 ; j++){                                                      
          window_test = [];
          window_test.push(board[j][i]);
          window_test.push(board[j+1][i]);
          window_test.push(board[j+2][i]);
          window_test.push(board[j+3][i])
          score+= this.evaluate_window(window_test, player);
          //console.log(window_test);
          //console.log(score)
        }
      }
      //diagonale qui monte
      for(i = 0 ; i< this.n - 3 ; i++){
        for(j = 0 ; j < this.m - 3 ; j++){
          window_test = []
          window_test.push(board[i][j]);
          window_test.push(board[i+1][j+1]);
          window_test.push(board[i+2][j+2]);
          window_test.push(board[i+3][j+3]);
          score+= this.evaluate_window(window_test, player);
          //console.log(window_test);
          //console.log(score);
        }
      }

      for(i=0 ; i<this.n-3 ; i++){
        for(j = 0 ; j< this.m - 3 ; j++){
          window_test = [];
          window_test.push(board[i+3][j]);
          window_test.push(board[i+3-1][j+1]); //ahah
          window_test.push(board[i+3-2][j+2]);
          window_test.push(board[i][j+3]);
          //console.log(window_test);
          //console.log(score);
        }
      }
      return score;
```

#### getRandomInt(max)
Méthode permettant de choisir un entier au hasard compris entre 0 et max-1

| Paramètres |                  Définitions                 |
|:----------:|:--------------------------------------------:|
| max        |    La borne supérieur des choix possibles    |
| RETURN     | Un entier aléatoire compris entre 0 et max-1 |

```javascript
return Math.floor(Math.random() * Math.floor(max));
```

#### minmax(board, depth, alpha, beta, maximizingPlayer)
Méthode récursive de l'algorithme minmax

|    Paramètres    |                                       Définitions                                       |
|:----------------:|:---------------------------------------------------------------------------------------:|
| board            |                         Plateau sur lequel on applique le minmax                        |
| depth            |         Entier permettant de limiter la profondeur de la recherche dans l'arbre         |
| alpha            |                    Valeur du paramètre alpha pour la coupe alpha-bêta                   |
| beta             |                    Valeur du paramètre beta pour la coupe alpha-bêta                    |
| maximizingPlayer |   Boolean permettant de savoir si on est le joueur qui maximise ou celui qui minimise   |
| RETURN           | Un couple (Valeur, Moove) représentant le score et la colonne du meilleur coup possible |
```javascript
      var valid_moove =  this.getMovePlayable(board);
      var res = new Object();
      //var res = []
      is_terminal_node = this.is_won(board, 1) || this.is_won(board, 2);
      if(depth == 0 || is_terminal_node){
        if(is_terminal_node){
          if(this.is_won(board, 2)){
            res["Moove"]= 'None';
            res["Score"]= 9999999999;
            //console.log(depth);
            //console.log(res);
            return res;
          }else if(this.is_won(board, 1)){
            res["Moove"]= 'None';
            res["Score"]= -99999999;
            //console.log(res);
            //console.log(depth);
            //console.log(res);
            return res;
          }else{
            res["Moove"]= 'None';
            res["Score"]= 0;
            //console.log(depth);
            //console.log(res);
          } 
        }else{
          res["Moove"]= 'None';
          res["Score"] = this.score_position(board, 2);
          //console.log(res);
          //console.log(depth);
          //console.log(res);
          return res;
        }
      }else{
        if(maximizingPlayer){
          value = Number.NEGATIVE_INFINITY;
          var choices = this.getMovePlayable(board);
          var number_of_choice = choices.length;
          var column = this.getRandomInt(number_of_choice);
          for(var i = 0 ; i < number_of_choice ; i++){
            var tempBoard = []
            var col = choices[i];
            row = this.get_open_row(board, col);
            for(z = 0 ; z<this.n ; z++){
              tempBoard.push(Array.from(board[z]))
            }
            tempBoard[row][col] = 2;
            var new_score = this.minmax(tempBoard, depth-1, alpha, beta, false)
            if(new_score["Score"] > value){
              value = new_score["Score"];
              column = col;
            }
            var alpha = Math.max(alpha, value);
            if(alpha >= beta){
              break;
            }
          }
          res["Moove"] = column;
          res["Score"] = value;
        }else{
          var value = Number.POSITIVE_INFINITY;
          var choices = this.getMovePlayable(board);
          var number_of_choice = choices.length;
          var column = this.getRandomInt(number_of_choice);
          for(i=0 ; i<number_of_choice ; i++){
            var tempBoard = [];
            var col = choices[i];
            row = this.get_open_row(board,col);
            for(j=0;j<this.n;j++){
              tempBoard.push(Array.from(board[j]));
            }
            tempBoard[row][col] = 1;
            var new_score = this.minmax(tempBoard, depth-1, alpha, beta, true)
            if(new_score["Score"] < value){
              value = new_score["Score"];
              column = col;
            }
            var beta = Math.min(beta, value)
            if(alpha >= beta){
              break;
            }
            
          }
          res["Moove"] = column;
          res["Score"] = value;
        } 
      }
      //console.log(depth);
      //console.log(res);
      return res;
```
