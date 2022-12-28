
<template>
  <div>
    <v-card class="board" :style="get_board_style()">
      <div v-if="completed" class="align-center justify-center d-flex overlay">
        <h2 class="text-white">You Win</h2>
      </div>
      <template v-for="tile in tiles" :key="tile.value">
        <div
          v-if="tile.value != (row * col) || completed"
          class="tile d-flex align-center justify-center elevation-3 bg-secondary rounded-lg"
          :style="get_tile_style(tile.position)"
          @click="move(tile)"
        >
          <span class="text-white text-h6">{{ tile.value }}</span>
        </div>
      </template>
    </v-card>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";

class Position {
  x: number;
  y: number;

  constructor(x: number = 1, y: number = 1) {
    this.x = x;
    this.y = y;
  }
}

class Tile {
  value: number;
  position: Position;

  constructor(value: number, position: Position) {
    this.value = value;
    this.position = position;
  }
}

const GAP_SIZE = 10;
let EMPTY_TILE: Tile;
let completed = ref(false)

const props = defineProps({
  row: {
    type: Number,
    default: 2,
  },
  col: {
    type: Number,
    default: 2,
  },
  tileSize: {
    type: Number,
    default: 100,
  },
});



const tiles = ref(new Array<Tile>());

function init() {
  const {row, col} = props
  const max = row * col
  // create values and randomize it's order
  const values = new Array(max).fill(null).map((v, i) => i + 1)
  values.sort(() => 0.5 - Math.random())

  for (let x = 0; x < row; x++) {
    for (let y = 0; y < col; y++) {
    const index = (x * col) + y
    const value = values[index]

    const tile = new Tile(value, new Position(x, y));
    tiles.value.push(tile);

    // Empty space or empty tile.
      if(value == max) {
        EMPTY_TILE = tile
      }
    }
  }
}

init();

function get_tile_style(position: Position): string {
  const { x, y } = position
  const { tileSize, col, row } = props
  let [i, j] = [(y % col), (x % row)]
  const left = i * tileSize + GAP_SIZE * (i + 1)
  const top =  j * tileSize + GAP_SIZE * (j + 1)

  return `left: ${left}px; top: ${top}px; width: ${tileSize}px; height: ${tileSize}px;`;
}

function get_board_style() {
  const { tileSize, row, col } = props;
  const h = row * tileSize + (row + 1) * GAP_SIZE;
  const w = col * tileSize + (col + 1) * GAP_SIZE;
  return `height: ${h}px; width: ${w}px;`;
}

function check_puzzel() {
  completed.value = tiles.value.every(tile => {
    const {x, y} = tile.position
    const index = (x * props.col) + y
    return tile.value - 1 == index
  })
}

function move(target_tile: Tile) {
  const {x: x1, y: y1} = target_tile.position
  const {x: x2, y: y2} = EMPTY_TILE.position
  const can_replace = Math.abs(x1 - x2) + Math.abs(y1 - y2) == 1

  if(can_replace) {
    const position = target_tile.position
    target_tile.position = EMPTY_TILE.position
    EMPTY_TILE.position = position
    check_puzzel()
  }
}

</script>
<style scoped>
.board {
  position: relative;
}
.tile {
  top: 0;
  left: 0;
  transition: all 0.5s;
}
.tile, .overlay {
  position: absolute;
  user-select: none;
  animation-name: slidein;
  animation-duration: 1s;
}

@keyframes slidein {
  from {
    transform: scale(0);
  }

  to {
    transform: scale(1);
  }
}
.overlay {
  width: 100%;
  height: 100%;
  background: rgba(126, 126, 126, 0.568);
  z-index: 10;
}
</style>
