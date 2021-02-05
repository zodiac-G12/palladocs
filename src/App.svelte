<script>
import Board from './Board.svelte';
import Modal from './Modal.svelte';
import * as localForage from 'localforage';

let maxId = 12;
const sampleItem = () => {
  return {id: ++maxId, name: `title-${maxId}`, degree: "LOW", description: "description", completeness: 0};
}

let board = [
  {
    id: 1,
    name: "TODO",
    items: [
      {id: 4, name: "HOME PARTY", degree: "LOW", description: "description", completeness: 0},
      {id: 5, name: "KARAOKE", degree: "MEDIUM", description: "description", completeness: 10},
      {id: 6, name: "SEARCH WORK", degree: "HIGH", description: "description", completeness: 20},
      {id: 7, name: "SEND MONEY", degree: "CRITICAL", description: "description", completeness: 100},
      {id: 8, name: "EAT MEET", degree: "PENDING", description: "description", completeness: 90},
      {id: 9, name: "PLAY SPORT", degree: "LOW", description: "description", completeness: 10},
      {id: 10, name: "GAME CREAR", degree: "MEDIUM", description: "description", completeness: 0},
      {id: 11, name: "KILL BUGS", degree: "CRITICAL", description: "description", completeness: 30},
      {id: 12, name: "WASH BATH", degree: "PENDING", description: "description", completeness: 55}
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
localForage.getItem("board").then((value) => {
  if(value) {
    console.log(value)
    board = JSON.parse(value);
    return;
  }
  localForage.setItem("board", JSON.stringify(board));
});

localForage.getItem("maxId").then((value) => {
  if(value) {
    console.log(value)
    maxId = parseInt(value);
    return;
  }
  localForage.setItem("maxId", maxId);
});


const updateBoard = async (i, j, propName, value) => {
  try {
    board[i].items[j][propName] = value;
    await localForage.setItem("board", JSON.stringify(board));
    await localForage.setItem("maxId", maxId);
  } catch (error) {
    console.error(`At updateBoard: ${error}`);
  }
}

const addItem = async (i) => {
  try {
    board[i].items[board[i].items.length] = sampleItem();
    await localForage.setItem("board", JSON.stringify(board));
    await localForage.setItem("maxId", maxId);
  } catch (error) {
    console.error(`At addItem: ${error}`);
  }
}

const removeItem = async (i,idx) => {
  try {
    const updatedBoard = board[i].items;
    updatedBoard.splice(idx,1);
    board[i].items = updatedBoard;
    await localForage.setItem("board", JSON.stringify(board));
    await localForage.setItem("maxId", maxId);
  } catch (error) {
    console.error(`At removeItem: ${error}`);
  }
}

</script>

<style>
:global(*) {
  box-sizing: border-box;
  margin: 0;
}
</style>

<!-- importtant Modal wrap https://github.com/flekschas/svelte-simple-modal/issues/16-->
<Modal>
  <Board bind:columnItems={board} {updateBoard} {addItem} {removeItem}></Board>
</Modal>
