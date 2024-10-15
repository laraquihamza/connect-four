<script setup lang="ts">
import { computed, ref } from 'vue';
var board = ref([
    [0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0, 0],
]);
var hoveredCell = ref([0, 0]);
let currentPlayer = 1;
function hoverTopCell(j: number) {
    let i = board.value.length - 1;
    for (; i>=0; i--) {
        if (board.value[i][j] === 0) {
            hoveredCell.value = [i, j];
            break;
        }
    }
}

function resetBoard() {
    board.value = [
        [0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0],
        [0, 0, 0, 0, 0, 0, 0],
    ];
}
function isHovered(i: number, j: number) {
    console.log(hoveredCell.value[0], hoveredCell.value[1]);
    return hoveredCell.value[0] === i && hoveredCell.value[1] === j;
}

function setCell(j: number, value: number) {
    let i = board.value.length - 1;
    for (; i >= 0; i--) {
        if (board.value[i][j] === 0) {
            break;
        }
    }
    if(i>=0) {
        board.value[i][j] = value;
        currentPlayer = currentPlayer === 1 ? 2 : 1;
        hoverTopCell(j);
    }
}

const gameStatus = computed(()=>{
    const rows = board.value.length;
    const cols = board.value[0].length;

    // Vérification horizontale
    for (let row = 0; row < rows; row++) {
        for (let col = 0; col < cols - 3; col++) {
            const current = board.value[row][col];
            if (current !== 0 &&
                current === board.value[row][col + 1] &&
                current === board.value[row][col + 2] &&
                current === board.value[row][col + 3]) {
                return current; // Retourne le joueur gagnant (1 ou 2)
            }
        }
    }

    // Vérification verticale
    for (let row = 0; row < rows - 3; row++) {
        for (let col = 0; col < cols; col++) {
            const current = board.value[row][col];
            if (current !== 0 &&
                current === board.value[row + 1][col] &&
                current === board.value[row + 2][col] &&
                current === board.value[row + 3][col]) {
                return current; // Retourne le joueur gagnant (1 ou 2)
            }
        }
    }

    // Vérification diagonale (haut-gauche vers bas-droite)
    for (let row = 0; row < rows - 3; row++) {
        for (let col = 0; col < cols - 3; col++) {
            const current = board.value[row][col];
            if (current !== 0 &&
                current === board.value[row + 1][col + 1] &&
                current === board.value[row + 2][col + 2] &&
                current === board.value[row + 3][col + 3]) {
                return current; // Retourne le joueur gagnant (1 ou 2)
            }
        }
    }

    // Vérification diagonale (bas-gauche vers haut-droite)
    for (let row = 3; row < rows; row++) {
        for (let col = 0; col < cols - 3; col++) {
            const current = board.value[row][col];
            if (current !== 0 &&
                current === board.value[row - 1][col + 1] &&
                current === board.value[row - 2][col + 2] &&
                current === board.value[row - 3][col + 3]) {
                return current; // Retourne le joueur gagnant (1 ou 2)
            }
        }
    }

    // Aucun gagnant trouvé
return 0;
});
</script>
<template>
    <div v-if="gameStatus === 0" class="gameboard">
        <div class="message-bar">
            <span class="heading3 dark">Player {{ currentPlayer }} Turn</span>
        </div>
        <div class="board">
            <div class="gamerow" v-for="(column,i) in board" v-bind:key="i">
                <div class="gamecircle" v-for="(cell,j) in column" v-bind:key="j" v-on:mouseover="hoverTopCell(j)"
                    v-on:click="setCell(j, currentPlayer)"
                    :class="[{ 'temp-choice': isHovered(i, j) }, { 'p1': board[i][j] === 1 }, { 'p2': board[i][j] === 2 }]">
                </div>
            </div>
        </div>
    </div>
    <div v-else class="gameboard">
        <div class="message-bar">
            <span class="heading3 dark">Player {{ gameStatus }} Won !</span>
        </div>
        <div class="board">
            <button class="reset-button" v-on:click="resetBoard">Reset Game</button>
        </div>
    </div>
</template>