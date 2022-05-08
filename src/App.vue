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
        // see if the move is legal
        var move = game.move({
          from: source,
          to: target,
          promotion: "q", // NOTE: always promote to a queen for example simplicity
        });

        // illegal move
        if (move === null) return "snapback";
      }
      function onSnapEnd() {
        board.position(game.fen());
      }
      var config = {
        draggable: true,
        position: "start",
        onDragStart: onDragStart,
        onDrop: onDrop,
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
