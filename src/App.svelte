<script>
import Board from './Board.svelte';
import Modal from './Modal.svelte';

let board = [
  {
    id: 1,
    name: "TODO",
    items: [
      {id: 41, name: "item41", degree: "LOW", description: "description", completeness: 0},
      {id: 42, name: "item2", degree: "MEDIUM", description: "description", completeness: 10},
      {id: 43, name: "item43", degree: "HIGH", description: "description", completeness: 20},
      {id: 44, name: "item44", degree: "CRITICAL", description: "description", completeness: 100},
      {id: 45, name: "item45", degree: "PENDING", description: "description", completeness: 90},
      {id: 46, name: "item46", degree: "LOW", description: "description", completeness: 10},
      {id: 47, name: "item47", degree: "MEDIUM", description: "description", completeness: 0},
      {id: 48, name: "item48", degree: "CRITICAL", description: "description", completeness: 30},
      {id: 49, name: "item49", degree: "PENDING", description: "description", completeness: 55}
    ]
  },
  {
    id: 2,
    name: "PROGRESS",
    items: []
  },
  {
    id: 3,
    name: "DONE",
    items: []
  }
];

const updateBoard = (i, j, propName, value) => {
  board[i].items[j][propName] = value;
}

</script>

<style>
:global(*) {
  box-sizing: border-box;
  margin: 0;
}
</style>

<Modal>
  <Board columnItems={board} {updateBoard}></Board>
</Modal>
