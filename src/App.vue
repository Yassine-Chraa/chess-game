<template>
  <div class="container">
    <div class="row">
      <div class="col-12">
        <div id="myBoard" style="width: 400px; margin: auto;margin"></div>
      </div>
    </div>
  </div>
</template>

<script>
import ChessBoard from "chessboardjs-vue";
import { Chess } from "chess.js";
export default {
  name: "App",
  methods: {
    configGame() {
      var board = null;
      const game = new Chess();

      const whiteSquareGrey = "#a9a9a9";
      const blackSquareGrey = "#696969";

      function removeGreySquares() {
        let square = document.querySelectorAll("#myBoard .square-55d63");
        for (let i = 0; i < square.length; i++) {
          square[i].style.backgroundColor = "";
        }
      }
      function greySquare(square) {
        let $square = document.querySelector("#myBoard .square-" + square);

        let background = whiteSquareGrey;

        if (
          (" " + $square.classList[1] + " ")
            .replace(/[\n\t]/g, " ")
            .indexOf(" black-3c85d ") > -1
        ) {
          background = blackSquareGrey;
        }

        $square.style.backgroundColor = background;
      }
      function onDragStart(source, piece) {
        // do not pick up pieces if the game is over
        if (game.game_over()) return false;

        // only pick up pieces for the side to move
        if (
          (game.turn() === "w" && piece.search(/^b/) !== -1) ||
          (game.turn() === "b" && piece.search(/^w/) !== -1)
        ) {
          return false;
        }
      }
      function onDrop(source, target) {
        removeGreySquares();
        // see if the move is legal
        let move = game.move({
          from: source,
          to: target,
          promotion: "q", // NOTE: always promote to a queen for example simplicity
        });

        // illegal move
        if (move === null) return "snapback";
      }
      function onMouseoverSquare(square) {
        // get list of possible moves for this square
        let moves = game.moves({
          square: square,
          verbose: true,
        });

        // exit if there are no moves available for this square
        if (moves.length === 0) return;

        // highlight the square they moused over
        greySquare(square);

        // highlight the possible squares for this piece
        for (var i = 0; i < moves.length; i++) {
          greySquare(moves[i].to);
        }
      }
      function onMouseoutSquare() {
        removeGreySquares();
      }
      function onSnapEnd() {
        board.position(game.fen());
      }

      let config = {
        draggable: true,
        position: "start",
        onDragStart: onDragStart,
        onDrop: onDrop,
        onMouseoutSquare: onMouseoutSquare,
        onMouseoverSquare: onMouseoverSquare,
        onSnapEnd: onSnapEnd,
      };

      board = ChessBoard("myBoard", config);
    },
  },
  mounted() {
    this.configGame();
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
