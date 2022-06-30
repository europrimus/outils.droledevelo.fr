<script>
  import { onMount } from 'svelte';
  import { writable } from "svelte/store";

  export let gearsets = writable([]);
  export let name;

  function addGearset() {
          $gearsets = [...$gearsets, ""]
  }
  function removeGearset() {
          $gearsets = [...$gearsets.slice(0,$gearsets.length - 1)]
  }

  onMount(() => {
          gearsets = writable(
            JSON.parse(
              localStorage.getItem("driveTrain_" + name)
            ) || []
          );
          gearsets.subscribe(
            val => localStorage.setItem("driveTrain_" + name, JSON.stringify(val))
          );
  })
</script>

{#each $gearsets as gearset}
  <span contenteditable="true" bind:textContent={gearset} type=number min=10 max=100></span>
{/each}
<button on:click={addGearset}>+</button>
<button on:click={removeGearset}>-</button>

<style>
  span {
    margin: 0.2em;
    padding: 0.1em 0.4em;
    background: #f0f0f0;
  }


</style>
