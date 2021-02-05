<script>
import TextArea from "./TextAreaAutosize.svelte";

export let columnItems;
export let column;
export let item;
export let i;
export let j;

export let change;

let degreeReal = item.degree;

function changeName(e) {
  change(i, j, "name", e.target.value);
}

function changeDescription(e) {
  change(i, j, "description", e.target.value);
}

function changeCompleteness(e) {
  change(i, j, "completeness", parseInt(e.target.value));
}

function changeDegree(e) {
  console.log(e.target.innerText)
  degreeReal = e.target.innerText;
  change(i, j, "degree", e.target.innerText);
}

const degreeMap = {
  "LOW": "blue",
  "MEDIUM": "green",
  "HIGH": "orange",
  "CRITICAL": "red",
  "PENDING": "indigo"
};

const degreeColorMap = (degree, value) => {
  return value === degree ? degreeMap[degree] : "rgb(224,224,224)";
}
</script>

<style>
.title {
  outline: none;
  resize: none;
  margin: 0;
  margin-bottom: 10px;
  padding: 1px;
  height: 20px;
  border: none;
}
.completeness-slider {
  padding: 0;
}
.degree-container {
  display: flex;
  flex-wrap: wrap;
}
.degree {
  cursor: pointer;
  display: flex;
  color: white;
  text-align: center;
  border-radius: 20px;
  padding: 10px;
  padding-top: 2px;
  padding-bottom: 2px;
  /* user-select: none; */
  /* -moz-user-select: none; */
  /* -webkit-user-select: none; */
  /* -ms-user-select: none; */
}
</style>

<div>
  <textarea class="title" value={item.name} placeholder="title" on:change={changeName}></textarea>
  <TextArea bind:value={item.description} placeholder={"description"} change={changeDescription} minRows={1} maxRows={40}/>
  <input class="completeness-slider" type=range bind:value={item.completeness} on:change={changeCompleteness} min=0 max=100> {item.completeness}%
  <div class="degree-container">
    {#each Object.keys(degreeMap) as degree, idx (idx)}
      <div class="degree"
        on:click={changeDegree}
        style="background: {degreeColorMap(degree, degreeReal)}; color: {degree===degreeReal ? 'white' : 'black'}"
      >{degree}</div>
    {/each}
  </div>
</div>
