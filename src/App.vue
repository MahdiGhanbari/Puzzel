<template>
  <v-app>
    <v-main>
      <v-container>
        <v-toolbar flat height="100">
          <v-btn @click="createPuzzel" variant="flat" color="red" large class="mr-2"
            >new puzzel</v-btn
          >
          <v-text-field
            v-model="row"
            label="Row"
            variant="solo"
            density="compact"
            hide-details
            type="number"
            min="3"
            class="w-25 mx-2"
          />
          <v-text-field
            v-model="column"
            label="Column"
            variant="solo"
            density="compact"
            hide-details
            type="number"
            min="3"
            class="w-25 mx-2"
          />
          <v-text-field
            v-model="size"
            label="Tile Size"
            variant="solo"
            density="compact"
            hide-details
            type="number"
            min="30"
            class="w-25 mx-2"
          />
        </v-toolbar>
        <v-row class="mt-5" justify="center">
          <v-col v-for="(puzzel, i) in puzzels" :key="i" class="d-flex align-center justify-center">
            <Board v-bind="puzzel"/>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import Board from "@/components/Board.vue";
import { ref } from 'vue';

class Puzzel {
  tileSize: number;
  col: number;
  row: number;

  constructor(tileSize: number, col: number, row: number) {
    this.tileSize = Math.max(tileSize, 30)
    this.col = Math.max(col, 3)
    this.row = Math.max(row, 3)
  }
}



const puzzels = ref(new Array<Puzzel>())
let size = ref(100)
let column = ref(3)
let row = ref(3)

function createPuzzel() {
  const puzzel = new Puzzel(size.value, column.value, row.value)

  puzzels.value.push(puzzel)
}

</script>

