<script>
import { getContext } from 'svelte';
import { fly } from 'svelte/transition';
import { flip } from 'svelte/animate';
import { dndzone } from 'svelte-dnd-action';
import Popup from './Popup.svelte';

export let columnItems;
export let updateBoard;
export let addItem;
export let removeItem;
const { open } = getContext('simple-modal');

const flipDurationMs = 300;

let choiced = [];

const change = (i, j, propName, value) => {
  updateBoard(i, j, propName, value);
}

const add = (i) => {
  addItem(i);
}

const removes = (i) => {
  const idxs = choiced.map(id => {
    return columnItems[i].items.findIndex(item => item.id === id);
  }).sort().reverse().forEach((idx) => {
    removeItem(i, idx);
  })

  choiced = [];
}

const degreeColorMap = (degree) => {
  const colorMap = {
    "LOW": "blue",
    "MEDIUM": "green",
    "HIGH": "orange",
    "CRITICAL": "red",
    "PENDING": "indigo"
  };

  return colorMap[degree];
}

const completenessColorMap = (completeness) => {
  return `rgb(${255*(100 - completeness)/100},0,${255 - 255*(100 - completeness)/100})`;
}

const showPopup = (column, item, i, j) => {
  open(Popup, { columnItems, column, item, i, j, change });
};

function handleDndConsiderColumns(e) {
  columnItems = e.detail.items;
}

function handleDndFinalizeColumns(e) {
  columnItems = e.detail.items;
}

function handleDndConsiderCards(cid, e) {
  const colIdx = columnItems.findIndex(c => c.id === cid);
  columnItems[colIdx].items = e.detail.items;
  columnItems = [...columnItems];
}

function handleDndFinalizeCards(cid, e) {
  const colIdx = columnItems.findIndex(c => c.id === cid);
  columnItems[colIdx].items = e.detail.items;
  columnItems = [...columnItems];
}

function handleClick(column, item, i, j) {
  console.log("clicked", column, item);
  showPopup(column, item, i, j);
}
</script>

<div class="crown">
  <img alt="" class="icon" src="/palladocs/palladocs_128.png">
  <h3 class="app-name">palladocs</h3>
</div>
<section class="board">
  <!-- use:dndzone={{items:columnItems, flipDurationMs, type:'columns'}} on:consider={handleDndConsiderColumns} on:finalize={handleDndFinalizeColumns}> -->
  {#each columnItems as column, i (column.id)}
    <div class="column"><!-- animate:flip="{{duration: flipDurationMs}}"> -->
      <div class="column-title">{column.name}</div>
      <div class="icontainer">
        <div class="add" on:click={add(i)}>+</div>
        <div class="remove" on:click={removes(i)}>−</div>
      </div>
      <div class="column-content" use:dndzone={{items:column.items, flipDurationMs}}
           on:consider={(e) => handleDndConsiderCards(column.id, e)} on:finalize={(e) => handleDndFinalizeCards(column.id, e)}>
           {#each column.items as item, j (item.id)}
             <div class="card" animate:flip="{{duration: flipDurationMs}}" on:click={handleClick(column, item, i, j)}>
               <input class="checkbox" type="checkbox" bind:group={choiced} value={item.id}>
               <div>
                 <div style="margin-bottom: 10px">
                   {item.name}
                 </div>
                 <div style="display: flex">
                   <div class="degree" style="background: {degreeColorMap(item.degree)}">
                     {item.degree}
                   </div>
                   <div class="completeness" style="background: {completenessColorMap(item.completeness)}">
                     {item.completeness}%
                   </div>
                 </div>
               </div>
             </div>
           {/each}
      </div>
    </div>
  {/each}
</section>

<style>
.icon {
  height: 5vh;
  object-fit: contain;
  margin-left: -15vh;
}
.app-name {
  position: absolute;
  color: white;
  font-family: 'Acme', sans-serif;
}
.icontainer {
  display: flex;
  font-size: 3.5vh;
  height: 3.5vh;
  user-select: none;
  -moz-user-select: none;
  -webkit-user-select: none;
  -ms-user-select: none;
}
.add {
  background: darkslategray;
  color: white;
  width: 3.5vh;
  display: flex;
  justify-content: center;
  /* align-items: center; */
  line-height: 3.5vh;
  border-radius: 50%;
  cursor: pointer;
  margin-right: 0.25em;
}
.remove {
  background: darkslategray;
  color: white;
  width: 3.6vh;
  display: flex;
  justify-content: center;
  /* align-items: center; */
  line-height: 3.5vh;
  border-radius: 50%;
  cursor: pointer;
}
.crown {
  top: 0;
  left: 0;
  width: 100vw;
  position: fixed;
  background-color: steelblue;
  height: 7.5vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
.board {
  height: 80vh;
  /* margin-bottom: 40px; */
  padding: 0.5em;
  outline: none;
  margin-top: 10vh;
  overflow-y: hidden;
}
.column {
  height: 75vh;
  padding: 0.5em;
  padding-bottom: 2.5em;
  float: left;
  border: 1px solid #333333;
  /*Notice we make sure this container doesn't scroll so that the title stays on top and the dndzone inside is scrollable*/
  overflow-y: hidden;
  width: 30.33%;
  margin: 1.5%;
  /* filter: blur(2px); */
  color: black;
  background-color: lightgoldenrodyellow;
}
.column-content {
  height: 95%;
  /* Notice that the scroll container needs to be the dndzone if you want dragging near the edge to trigger scrolling */
  overflow-y: scroll;
}
.column-title {
  margin-bottom: 1em;
  display: flex;
  justify-content: center;
  align-items: center;
}
.card {
  height: 10vh;
  width: 100%;
  padding: 10px;
  margin: 0.4em 0;
  display: flex;
  /* justify-content: center; */
  align-items: center;
  /* background-color: #dddddd; */
  background-color: snow;
  border: 1px solid #333333;
  color: black;
}
.checkbox {
  margin: 0;
  margin-right: 10px;
}
.degree {
  color: white;
  text-align: center;
  border-radius: 20px;
  padding: 10px;
  padding-top: 2px;
  padding-bottom: 2px;
}
.completeness {
  color: white;
  text-align: center;
  border-radius: 20px;
  padding: 10px;
  width: 60px;
  padding-top: 2px;
  padding-bottom: 2px;
}
@media (orientation: portrait) {
  .board {
    width: 300%;
    overflow-x: scroll;
    height: 70vh;
  }
  .column {
    height: 65vh;
  }
  .card {
    margin-right: 10%;
    width: 90%;
  }
}
@media (orientation: landscape) {
  .board {
    width: 100vw;
    overflow-x: hidden;
  }
}
</style>
